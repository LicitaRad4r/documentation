# Backend

API REST em Node.js, TypeScript e Fastify 5, com validação de entrada em Zod 4. Fica em `server/` do repositório de código.

## Visão Geral

O código é organizado **por funcionalidade**. Cada módulo declara a interface do seu repositório e as implementações reais ficam em `infra/` (Firestore, Firebase Auth). O `server.ts` liga as duas pontas e o `buildApp(deps)` monta a API com essas dependências injetadas, o que permite testar com repositórios em memória e relógio fixo, sem Firebase.

```text
server/src/
├── server.ts       # composição: adaptadores reais + abre a porta
├── app.ts          # buildApp(deps): monta a API (usado também nos testes)
├── auth.ts         # hook único de autenticação e autorização
├── erros.ts        # ErroApi e formato Erro
├── dominio/        # tipo Edital, normalização de texto, datas de Brasília
├── perfil/  feed/  certidoes/   # rotas, schemas Zod e regras de cada funcionalidade
├── score/          # calcularScore (função pura)
├── ingestao/       # cliente do PNCP, adapter, serviço, rota do job, CLI
├── infra/          # adaptadores: Firestore, Auth, arquivos, prontidão
└── seed/           # dados golden para o emulador
```

## Rotas

| Método | Caminho | Acesso |
| :--- | :--- | :--- |
| GET | `/health`, `/healthz`, `/readyz` | público (`/readyz` também consulta o Firestore) |
| GET, PUT | `/v1/perfil` | autenticado; o primeiro `PUT` cria a empresa, grava os *claims* e responde `201` |
| GET | `/v1/feed` | autenticado, com empresa; filtros, ordenação e paginação no servidor |
| GET | `/v1/certidoes`, `/v1/certidoes/{id}` | só `decisor` da empresa dona |
| PUT | `/v1/certidoes/{id}/renovacao` | só `decisor`; `multipart/form-data` com arquivo e nova validade |
| POST | `/jobs/ingestao-pncp` | token do job; rota interna, fora do contrato do front |

As rotas de negócio respondem com e sem o prefixo `/v1`. O OpenAPI também declara `POST /v1/certidoes` e `GET /v1/editais/{id}`, que **ainda não existem no código**.

**Planejado (Checkpoint 3, US20):** as rotas de acompanhamento de editais ainda não existem e não estão no OpenAPI atual.

| Método | Caminho | Função |
| :--- | :--- | :--- |
| POST | `/v1/acompanhamentos` | começa a acompanhar um edital (etapa inicial: Interessado); repetir a chamada responde `200`, sem duplicar |
| GET | `/v1/acompanhamentos` | lista da empresa, ordenada pelo prazo mais próximo |
| PATCH | `/v1/acompanhamentos/{id}` | muda de etapa, uma por vez; transição inválida responde `422`; o Resultado exige "Venceu" ou "Não venceu" e não muda mais |
| DELETE | `/v1/acompanhamentos/{id}` | deixa de acompanhar, só nas etapas Interessado e Preparando |

Valem as mesmas regras de segurança das demais rotas: `401` sem token, `403` para recurso de outra empresa e `404` para id inexistente. Como o CORS hoje só libera `GET`, `PUT`, `POST` e `OPTIONS`, é preciso incluir `PATCH` e `DELETE` em `app.ts`.

## Validação com Zod

[Zod](https://zod.dev) é uma biblioteca de **schemas para TypeScript**. O mesmo schema faz duas coisas:

1. **Valida em tempo de execução.** O TypeScript só confere tipos na compilação; um JSON que chega pela rede pode ter qualquer formato.
2. **Gera o tipo estático**, com `z.infer`, então regra e tipo nunca saem de sincronia.

No LicitaRadar ele protege a API de dados do cliente e de uma **fonte externa instável** (o PNCP).

| Schema | Arquivo | O que valida |
| :--- | :--- | :--- |
| `PerfilEntrada` | `perfil/schema.ts` | corpo do `PUT /v1/perfil`; também os perfis lidos do banco na ingestão e o *seed* |
| `ConsultaFeed` | `feed/consulta.ts` | *query string* do `GET /v1/feed` |
| `ContratacaoPncpSchema` | `ingestao/pncp.ts` | cada contratação devolvida pelo PNCP |

O schema declara forma e regras, e o tipo sai dele. A rota usa `safeParse`, que não lança exceção, e `erroDeZod` converte a falha no erro padrão da API:

```ts
export const PerfilEntrada = z
  .object({
    ufs: z.array(z.string().regex(/^[A-Z]{2}$/, 'UF inválida')).default([]),
    valorMinimo: z.number().min(0).nullable().default(null),
    valorMaximo: z.number().min(0).nullable().default(null),
    // ...
  })
  .refine((p) => p.valorMinimo === null || p.valorMaximo === null || p.valorMinimo <= p.valorMaximo, {
    path: ['valorMaximo'],
    message: 'O valor máximo deve ser maior ou igual ao mínimo',
  })
export type PerfilDados = z.infer<typeof PerfilEntrada>

// na rota
const parsed = PerfilEntrada.safeParse(req.body)
if (!parsed.success) throw erroDeZod(parsed.error)   // 400; depois disso, parsed.data é um PerfilDados válido
```

Com `valorMinimo` maior que `valorMaximo`, o cliente recebe `400` e o frontend mostra a mensagem no campo certo:

```json
{ "codigo": "VALIDACAO", "mensagem": "Dados inválidos.",
  "detalhes": [{ "campo": "valorMaximo", "mensagem": "O valor máximo deve ser maior ou igual ao mínimo" }],
  "requestId": "…" }
```

**Recursos usados:**

| Recurso | Para quê |
| :--- | :--- |
| `.default(...)` | campo ausente vira valor padrão (`perfilVazio()` é só `PerfilEntrada.parse({})`) |
| `.refine` / `.superRefine` | regras que o tipo não expressa: UFs sem repetição, mínimo ≤ máximo, datas `De ≤ Ate` |
| `z.coerce.number()` | a *query string* chega como texto; converte antes de validar |
| `.transform().pipe()` | `uf=DF&uf=GO` e `uf=DF,GO` viram a mesma lista, em maiúsculas, e só então são validados |
| `.nullish()` | campo do PNCP pode faltar ou vir nulo sem derrubar a leitura |

**Na ingestão**, o `ContratacaoPncpSchema` é uma camada anticorrupção: lista só os campos usados e aceita, por exemplo, `razaoSocial` e `razaosocial` (a API real e o manual divergem). Contratação que não passa é contada como `invalida` e ignorada; empresa com perfil inválido é pulada, sem interromper o lote.

**Onde o Zod não é usado:** o upload de certidão (`multipart`) é validado à mão, e o `openapi.yaml` é escrito à mão, **não gerado** dos schemas como diz o ADR-0002. Um teste de contrato (`tests/contract/`) detecta divergências entre eles. No frontend o Zod também aparece, em formulários e filtros da URL ([ver Frontend](frontend.md#zod-no-frontend)).

## Segurança, Erros e Logs

- **Autenticação:** o hook `onRequest` valida o ID token do Firebase (`Authorization: Bearer`) antes de ler o corpo; sem token válido é sempre `401`. Só as rotas de saúde são públicas.
- **Isolamento entre empresas:** `empresaId` e `role` vêm só do token. O hook `preHandler` rejeita (`403`) qualquer `orgId` ou `empresaId` diferente na query ou no corpo, e recurso de outra empresa também é `403`, sem revelar dados.
- **Papéis:** `decisor` acessa tudo; `analista` não acessa certidões. Token sem `empresaId` ou `role` é `403 EMPRESA_NAO_CONFIGURADA`.
- **Job de ingestão:** usa um token próprio (`INGEST_JOB_TOKEN`), comparado em tempo constante; sem o token configurado, a rota recusa tudo, e o token de usuário não abre o job.
- **Upload:** 1 arquivo de até 5 MB; o tipo (PDF, JPG ou PNG) é detectado pela **assinatura do conteúdo**, não pelo que o cliente declara.
- **Erros:** sempre no formato `{ codigo, mensagem, detalhes?, requestId }`, com códigos estáveis (`VALIDACAO`, `NAO_AUTENTICADO`, `EMPRESA_NAO_CONFIGURADA`, `NAO_ENCONTRADO`, `ACESSO_NEGADO`, `ERRO_INTERNO`). O `500` não traz detalhes.
- **Logs:** JSON com `requestId` (gerado no servidor), `route` e `status`; sem IP, e-mail, token nem a mensagem do erro, porque erros do Firestore trazem caminhos de documento.

Os testes que sustentam cada controle estão em [Testes](../Testes/estrategia_de_testes.md).

## Score de Aderência

`calcularScore` é uma função pura: mesmo perfil, edital e data geram sempre a mesma nota (0 a 100). Roda na ingestão; o feed só lê o resultado gravado.

| Critério | Regra | Pontos |
| :--- | :--- | :--- |
| Termos-chave (obrigatório) | frase no objeto; ou só na descrição / palavras; nenhum termo descarta | 40; 20 |
| Prazo (obrigatório) | dias úteis até o fim das propostas; abaixo do mínimo (padrão 3) descarta | ≥ 8: 10; 4 a 7: 5; menos: 0 |
| Modalidade (opcional) | a primeira do perfil é a prioritária; fora da lista descarta | 10 ou 5 |
| Valor (opcional) | na faixa; até 10% fora; acima disso descarta; sem valor pontua 0 | 15; 5 |
| UF (opcional) | UF do edital está no perfil; não descarta | 25 |

A nota é `round(100 × obtidos ÷ base)`, com base 50 (termos + prazo) mais o peso de cada critério opcional **configurado**. Faixas: `alta` (80 a 100), `media` (50 a 79) e `baixa` (0 a 49). O segmento nunca descarta hoje, porque a consulta do PNCP não o informa. Regra completa em [Score de Aderência](../requirements/produto/negocio/Regradenegocio/score.md) e ADR-0008.

## Ingestão do PNCP

Acionada pela rota `POST /jobs/ingestao-pncp`, pelo comando `npm run ingest` ou pelo workflow agendado; os três usam o mesmo serviço.

1. Consulta o PNCP por modalidade (6, 4, 5 e 8), até 3 páginas de 50, com pausa de 5 s; repete `429` e `5xx` com espera crescente.
2. Valida cada contratação com Zod e converte em `Edital`; ignora (e conta) inválidas, encerradas, sem prazo, sem UF ou de modalidade fora do perfil.
3. Grava os editais (id = número de controle do PNCP, então regravar não duplica).
4. Calcula o score de cada empresa para cada edital e remove o score dos pares que passaram a ser descartados.
5. Devolve um resumo. Se uma modalidade falha, as outras seguem; se todas falham, responde `502`.
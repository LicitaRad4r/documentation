# Padrão de Logs de Segurança e Conformidade (LGPD)

## 1. Visão Geral

Este documento define o padrão de logs da API para requisições bloqueadas com status `401 (Não Autenticado)` e `403 (Acesso Negado)`, visando a conformidade com a LGPD e a investigação de incidentes sem expor dados pessoais.

Os logs são escritos em JSON na saída padrão da API (biblioteca de log do Fastify). O local onde serão consultados em produção depende da hospedagem, ainda não publicada (o ADR-0004 propõe o Render Free).

## 2. Especificação do Formato de Log

Os logs de bloqueio geram JSON com apenas os campos abaixo. A configuração está em `server/src/app.ts` e `server/src/erros.ts`, e é verificada pelos testes `seguranca.test.ts` e `erros-log.test.ts` (caso CT-SEG-011).

### Campos Permitidos

| Campo | Conteúdo |
| :--- | :--- |
| `level` | Nível numérico do log. Bloqueios usam `40` (aviso) |
| `time` | Instante do registro, em milissegundos Unix |
| `requestId` | UUID gerado pelo servidor para cada requisição. **Nunca é aceito do cliente** e também vai na resposta de erro, para o usuário informar ao suporte |
| `route` | Modelo da rota, com o parâmetro sem valor (ex.: `/v1/certidoes/:id`), para não registrar IDs |
| `status` | Código HTTP: `401` ou `403` |
| `msg` | Texto fixo: `acesso bloqueado` |

O `pid` e o `hostname` padrão da biblioteca foram removidos (`base: null`).

O código de erro de domínio (`NAO_AUTENTICADO`, `EMPRESA_NAO_CONFIGURADA` ou `ACESSO_NEGADO`) **vai na resposta ao cliente**, junto do `requestId`, mas **não é gravado no log**. Para saber o motivo de um bloqueio, procure o `requestId` na resposta.

### Outras linhas de log da API

| Situação | `level` | `msg` | Campos |
| :--- | :--- | :--- | :--- |
| Toda resposta | `30` (info) | `requisicao` | `requestId`, `route`, `status` |
| Bloqueio `401` ou `403` | `40` (aviso) | `acesso bloqueado` | `requestId`, `route`, `status` |
| `/readyz` com dependência fora | `40` (aviso) | `api nao pronta` | `requestId`, `route`, `status` (`503`) |
| Erro inesperado (`500`) | `50` (erro) | `erro inesperado` | `requestId`, `route`, `status`, `erro` (tipo), `codigo` (código técnico, se houver) e `pilha` (só as linhas `at ...`) |

O log padrão de requisição do Fastify está desligado porque grava a URL completa, que pode conter filtros e identificadores.

### Dados Proibidos (Política de Redação e LGPD)

É estritamente proibido registrar em log:

- Endereços de IP dos clientes.
- Endereços de e-mail, nomes ou dados cadastrais.
- Tokens de autenticação, senhas ou cabeçalhos HTTP brutos (`req.headers`, `req.query`, `req.body`).
- A **mensagem** de erros inesperados: as mensagens do Firestore trazem o caminho do documento (por exemplo `certidoes/cert-a-1`). O log de `500` guarda só tipo, código e pilha.

## 3. Como Consultar

**Ambiente local (Docker Compose).** Os logs ficam no terminal do serviço `api`. Para listar só os bloqueios:

```bash
docker compose logs --no-log-prefix api | grep '"msg":"acesso bloqueado"'
```

**Produção.** Ainda não existe. Quando a hospedagem for definida, esta seção deve ser atualizada com o caminho no painel de logs e o filtro correspondente, que será a busca pelo texto `acesso bloqueado` (e, para um caso específico, pelo `requestId` informado pelo usuário).

## 4. Pontos em Aberto

- Registrar o código de domínio e o método HTTP no log de bloqueio facilitaria a análise, mas exige alterar `erros.ts` e o teste CT-SEG-011.
- Definir por quanto tempo os logs ficam guardados (retenção) conforme a hospedagem escolhida.
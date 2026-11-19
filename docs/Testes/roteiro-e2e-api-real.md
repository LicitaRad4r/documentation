# Roteiro E2E contra a API real (CP2 e CP3)
 
Roda a aplicação inteira: front real (sem mock), API Fastify, emuladores de Auth e de Firestore, com os dados do seed. Automatizado em `client/tests-e2e/api-real.spec.ts` (E2E-01 a 12) e `client/tests-e2e/api-real-kanban.spec.ts` (KAN-SEG-01 a 08). Os testes com a tag `@seguranca` também rodam contra a API publicada no Render (seção "Contra o Render").
 
**Neste documento:** [Pré-requisitos](#pré-requisitos) · [Usuários do seed](#usuários-do-seed) · [Cenários](#cenários) · [Kanban](#cenários-do-kanban-api-real-kanbanspects) · [Contra o Render](#contra-o-render-api-publicada) · [Evidência](#evidência-a-guardar) · [Fora do roteiro](#fora-deste-roteiro)
 
## Pré-requisitos
 
1. Estar na branch `main`.
2. Subir tudo com o front **sem mock**:
```bash
VITE_API_MOCK=false docker compose up --build
```
3. Rodar o seed (BUG-003) e conferir a mensagem "Seed ok":
```bash
docker compose exec api npm run seed
```
4. Instalar o Playwright no cliente, uma vez:
```bash
cd client && npm ci && npx playwright install chromium
```
5. Executar:
```bash
cd client
npx playwright test -c playwright.api-real.config.ts
```
   Variáveis opcionais: `WEB_URL` (padrão 5173), `API_URL` (3333) e `AUTH_EMULATOR_URL` (9099).
 
> [!NOTE]
> O E2E-09 altera uma certidão. Para repetir a execução, rode o seed de novo (ele recria usuários e dados).
 
## Usuários do seed
 
Senha de todos: `senha-local-123` (só no emulador).
 
| E-mail | Empresa | Papel |
| --- | --- | --- |
| `decisor.a@licitaradar.local` | emp-a (TI, DF/GO) | decisor |
| `analista.a@licitaradar.local` | emp-a | analista |
| `decisor.b@licitaradar.local` | emp-b (RS) | decisor |
 
## Cenários
 
| ID | Cenário | Passos | Resultado esperado | Liga a |
| --- | --- | --- | --- | --- |
| E2E-01 | Sem token e token adulterado | `GET /v1/feed` sem `Authorization`; depois com o token do decisor A com os 4 últimos caracteres trocados | 401 `NAO_AUTENTICADO` nos dois | CT-SEG-001, 002 |
| E2E-02 | Papel incorreto | Pegar token no emulador; `GET /v1/certidoes` como analista e como decisor | Analista 403 `ACESSO_NEGADO`; decisor 200 | CT-SEG-005, 006 |
| E2E-03 | Isolamento entre empresas | Decisor A chama `GET /v1/feed?empresaId=emp-b`; comparar o topo do feed de A e de B | 403 no forjado; topos diferentes | CT-SEG-007 a 009, CT-FEED-003 |
| E2E-04 | Sem sessão e senha errada | Abrir `/` sem login; entrar com senha errada | Tela "Entrar"; "E-mail ou senha incorretos." e continua no login | US02 |
| E2E-05 | Feed do seed | Entrar como decisor A | "Aquisição de notebooks" no topo, aderência alta; "Serviços de limpeza predial" não aparece | CT-FEED-001, 002 |
| E2E-06 | Ranking por empresa | Entrar como decisor B | Primeiro item é do RS | CT-FEED-003 |
| E2E-07 | Filtro sem resultado | Abrir `/?q=hemodialise`; clicar em "Limpar filtros" | Mensagem de vazio; depois a lista volta | CT-FILT-002, 003 |
| E2E-08 | Analista e Certidões | Entrar como analista A; ver o menu; abrir `/certidoes` | Menu sem Certidões; "Acesso negado" | US06 |
| E2E-09 | Renovar certidão | Decisor A abre Certidões; renova uma urgente com PDF e validade futura | "Certidão renovada." e ela passa para Em dia | CT-CERT-001, 003 a 007 |
| E2E-10 | Sair | Clicar em Sair | Volta para "Entrar" | US02 |
| E2E-11 | Saúde da API | `GET /healthz` e `GET /readyz` | 200 nos dois | RNF / saúde |
| E2E-12 | Job de ingestão sem token | `POST /jobs/ingestao-pncp` sem token | 401 | RNF06 |
 
 
## Cenários do kanban (`api-real-kanban.spec.ts`)
 
Todos são `@seguranca @kanban`, rodam em série e só apagam os registros que eles mesmos criaram.
 
| ID | Cenário | Resultado esperado |
| --- | --- | --- |
| KAN-SEG-01 | Sem token, token inválido e token expirado, em todas as rotas de `/acompanhamentos` | 401 `NAO_AUTENTICADO` |
| KAN-SEG-02 | Analista e decisor da empresa listam o kanban | 200 (qualquer papel da empresa usa o kanban) |
| KAN-SEG-03 | A empresa A cria um acompanhamento | 201 (ou 200 se já existia), sem `empresaId` na resposta |
| KAN-SEG-04 | A empresa B lista o kanban | Não vê o registro da A |
| KAN-SEG-05 | A empresa B tenta alterar (PATCH) e remover (DELETE) o registro da A | 403 `ACESSO_NEGADO`, e nada muda |
| KAN-SEG-06 | `empresaId` de outra empresa na query ou no corpo | 403 |
| KAN-SEG-07 | Id inexistente e corpo inválido | 404 e 400 |
| KAN-SEG-08 | Job de ingestão com token errado ou com token de usuário | 401 |
 
## Contra o Render (API publicada)
 
Roda só os testes `@seguranca` (13 testes: 5 de `api-real.spec.ts` e 8 do kanban), com poucas chamadas e sem carga. O primeiro acesso ao Render Free pode levar cerca de 1 minuto, por isso o `--timeout=120000`.
 
```bash
cd client
export API_URL=https://licitaradar-y2gp.onrender.com
export E2E_SENHA=<senha do seed de produção>
export FIREBASE_API_KEY=<apiKey web do Firebase>
npx playwright test -c playwright.api-real.config.ts --grep @seguranca --timeout=120000 | tee ../autorizacao-render.txt
```
 
Antes de guardar `autorizacao-render.txt` como evidência, confira que não há senha, `apiKey` nem token no arquivo (`grep -nE "eyJ|AIza"` não deve achar nada). A senha do seed de produção nunca vai para o repositório.
 
Resultado de 16/11/2026: 13 de 13. Detalhes na [matriz](matriz-rastreabilidade.md) (seção "Execuções registradas") e no [relatório](relatoriocp2.md).
 
## Evidência a guardar
 
- Relatório HTML do Playwright, capturas de falha e o commit testado.
- O resultado de cada E2E na coluna "Resultado" da [matriz de rastreabilidade](matriz-rastreabilidade.md), com data e commit.
## Fora deste roteiro
 
Itens sem cenário E2E: contador de editais novos (US21) e detalhe do edital (`GET /v1/editais/{editalId}`, sem rota no servidor). O perfil da empresa (BUG-005) e o acompanhamento de edital (US20) já têm tela e API; o kanban tem a suíte de autorização acima, mas ainda não um E2E de tela.

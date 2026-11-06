# Roteiro E2E contra a API real (CP2)

Roda a aplicação inteira: front real (sem mock), API Fastify, emulador de Auth e de Firestore, com os dados do seed. Automatizado em `client/tests-e2e/api-real.spec.ts`.

## Pré-requisitos
1. `main`
2. Subir tudo com o front **sem mock**:
   ```bash
   VITE_API_MOCK=false docker compose up --build
   ```
3. Rodar o seed (BUG-003) e conferir a mensagem "Seed ok":
   ```bash
   docker compose exec api npm run seed
   ```
4. Instalar o Playwright no cliente, uma vez: `cd client && npm ci && npx playwright install chromium`.
5. Executar:
   ```bash
   cd client
   npx playwright test -c playwright.api-real.config.ts
   ```
   Variáveis opcionais: `WEB_URL` (5173), `API_URL` (3333), `AUTH_EMULATOR_URL` (9099).
6. O E2E-09 altera uma certidão. Para repetir, rode o seed de novo (ele recria usuários e dados).

## Usuários do seed (senha `senha-local-123`)
| E-mail | Empresa | Papel |
| --- | --- | --- |
| decisor.a@licitaradar.local | emp-a (TI, DF/GO) | decisor |
| analista.a@licitaradar.local | emp-a | analista |
| decisor.b@licitaradar.local | emp-b (RS) | decisor |

## Cenários
| ID | Cenário | Passos | Resultado esperado | Liga a |
| --- | --- | --- | --- | --- |
| E2E-01 | Sem token e token adulterado | `GET /v1/feed` sem `Authorization`; depois com o token do decisor A com os 4 últimos caracteres trocados | 401 `NAO_AUTENTICADO` nos dois | CT-SEG-001, 002 |
| E2E-02 | Papel incorreto | Pegar token no emulador; `GET /v1/certidoes` como analista e como decisor | analista 403 `ACESSO_NEGADO`; decisor 200 | CT-SEG-005, 006 |
| E2E-03 | Isolamento entre empresas | Decisor A chama `GET /v1/feed?empresaId=emp-b`; comparar o topo do feed de A e de B | 403 no forjado; topos diferentes | CT-SEG-007 a 009, CT-FEED-003 |
| E2E-04 | Sem sessão e senha errada | Abrir `/` sem login; entrar com senha errada | Tela "Entrar"; "E-mail ou senha incorretos." e continua no login | US02 |
| E2E-05 | Feed do seed | Entrar como decisor A | "Aquisição de notebooks" no topo, aderência alta; "Serviços de limpeza predial" não aparece | CT-FEED-001, 002 |
| E2E-06 | Ranking por empresa | Entrar como decisor B | Primeiro item é do RS | CT-FEED-003 |
| E2E-07 | Filtro sem resultado | `/?q=hemodialise`; "Limpar filtros" | Mensagem de vazio; depois a lista volta | CT-FILT-002, 003 |
| E2E-08 | Analista e Certidões | Entrar como analista A; ver o menu; abrir `/certidoes` | Menu sem Certidões; "Acesso negado" | US06 |
| E2E-09 | Renovar certidão | Decisor A abre Certidões; renova uma urgente com PDF e validade futura | "Certidão renovada." e ela passa para Em dia | CT-CERT-001, 003 a 007 |
| E2E-10 | Sair | Clicar em Sair | Volta para "Entrar" | US02 |

## Evidência a guardar (matriz)
Relatório HTML do Playwright, capturas de falha e o commit testado. Registre o resultado de cada E2E na coluna "Resultado" da matriz de rastreabilidade, com data e commit.

## Fora deste roteiro (ainda sem tela ou API)
Perfil da empresa (BUG-005), acompanhamento de edital (US20) e contador de editais novos (US21).


# Relatório de testes: Checkpoint 2 e Checkpoint 3
 
**Projeto:** LicitaRadar · **Responsáveis pela qualidade:** Maria Júlia Sena e Kauan Tarchetti · **Última atualização:** 19/11/2026
 
 
**Neste documento:** [1. Resumo](#1-resumo) · [2. O que foi testado](#2-o-que-foi-testado) · [3. Casos do plano](#3-casos-do-plano-situação) · [4. Bugs e lacunas](#4-bugs-e-lacunas) · [5. E2E contra a API real](#5-e2e-contra-a-api-real-06112026) · [6. Autorização no Render (CP3)](#6-autorização-contra-a-url-publicada-cp3-e-jornada-do-kanban) · [7. Como reproduzir](#7-como-reproduzir)
 
## 1. Resumo
 
| Item | Resultado |
| --- | --- |
| Servidor: unitários, de API, de contrato e de ramos (`npm run test:unit`) | ✅ **220 de 220** (15 arquivos), em 19/11/2026 |
| Cliente: componentes e lógica (`npm run test:unit`) | ✅ **97 passaram** + 1 `todo` (18 arquivos), em 19/11/2026 |
| Integração com emulador (`npm run test:int`) | 27 testes: 21 do deny-all e 6 do kanban sobre o Firestore. Deny-all: ✅ 21 de 21 em 28/10/2026. Não reexecutados em 19/11; o CI roda no job `firestore rules` |
| E2E local contra a API real (`api-real.spec.ts`, 12 cenários) | ⚠️ **10 de 12** em 06/11/2026 (Docker + emulador + seed, Chromium). E2E-11 e E2E-12 falharam só porque as rotas ainda estavam no PR #25; hoje estão na `main` e passaram no Render (seção 6) |
| Autorização na API publicada (Render, 16/11/2026) | ✅ **13 de 13** (seção 6) |
| Carga (k6, emulador, 16/11/2026) | ✅ 792 requisições, 0% de falha; maior p95 de leitura 46,1 ms (meta 800 ms) e de escrita 36,8 ms (meta 1500 ms) |
| Cobertura do servidor (`npm run test:cov`, todos os arquivos medidos) | **97,4%** de instruções · 91,9% de ramos · 96,6% de funções · 98,8% de linhas (era 80,6% de instruções no CP2) |
| Casos do plano (CT-…) com teste que leva o ID | **36 de 39** |
| Contrato OpenAPI | v0.3.0 |
 
## 2. O que foi testado
 
Arquivos do servidor ficam em `server/tests/`; os do cliente, em `client/src/`.
 
### Servidor: 220 testes
 
| Frente | Arquivo | Testes | Casos do plano |
| --- | --- | ---: | --- |
| Contrato OpenAPI × API | `contract/openapi-contract.test.ts` | 18 | contrato v0.3 |
| Feed e filtros (API) | `unit/feed.test.ts` | 17 | CT-FEED-001 a 004, 009; CT-FILT-001, 004 a 007 |
| Busca textual | `unit/busca-textual.test.ts` | 22 | CT-FILT-006 e variações |
| Certidões (API) | `unit/certidoes.test.ts` | 23 | CT-CERT-003 a 007, 010 |
| Perfil | `unit/perfil.test.ts` | 10 | perfil da empresa |
| Score | `unit/score.test.ts` | 13 | cálculo e faixas |
| **Score: 10 casos tabelados do Caderno** | `unit/score-tabelado.test.ts` | 10 | fronteiras de valor, tolerância de 10%, sem valor, termo só na descrição, UF diferente |
| **Ramos (caixa branca)** | `unit/ramos.test.ts` | 11 | ramos de `score.ts` e `consulta.ts` |
| **Desempenho do feed** | `unit/desempenho.test.ts` | 1 | CT-FILT-009 (p95 ≤ 800 ms, 200 amostras, em memória) |
| Segurança (token, papel, isolamento) | `unit/seguranca.test.ts` | 14 | CT-SEG-001 a 009, 011 |
| CORS | `unit/cors.test.ts` | 7 | preflight sem token; 401 com cabeçalho de CORS |
| Erros e log | `unit/erros-log.test.ts` | 3 | formato `Erro`, 500 genérico |
| **Kanban de acompanhamento (US20)** | `unit/acompanhamentos.test.ts` | 37 | rotas `/acompanhamentos`, transições e isolamento |
| **Ingestão do PNCP** | `unit/ingestao.test.ts` | 27 | job, normalização e idempotência |
| **Prontidão** | `unit/prontidao.test.ts` | 7 | `/healthz` e `/readyz` |
| Regras do Firestore (deny-all) | `integration/firestore-deny.all.test.ts` | 21 (emulador) | CT-SEG-010 |
| **Kanban sobre o Firestore** | `integration/acompanhamentos-firestore.test.ts` | 6 (emulador) | repositório e API completa no emulador |
 
### Cliente: 97 testes + 1 `todo`
 
| Frente | Arquivo | Testes |
| --- | --- | ---: |
| Feed | `features/feed/PaginaFeed.test.tsx`, `filtros.test.ts`, `PaginaFeed.complementos`, `PaginaFeed.erro-filtros` | 11 + 9 + 2 + 1 |
| Certidões | `features/certidoes/PaginaCertidoes.test.tsx`, `.falha-envio`, `.fronteiras` | 9 + 2 + 1 |
| Acompanhamento (kanban) | `features/acompanhamento/PaginaAcompanhamento.test.tsx` | 11 |
| Detalhe do edital | `features/editais/PaginaDetalheEdital.test.tsx` | 5 |
| Perfil | `features/perfil/PaginaPerfil.test.tsx`, `api.test.tsx` | 9 + 2 |
| Login e acesso | `features/acesso/acesso.test.tsx` | 7 |
| Estado da consulta, app | `components/estado/EstadoDaConsulta.test.tsx`, `App.test.tsx` | 3 + 2 |
| Infraestrutura | `lib/cache.test.ts`, `lib/configFirebase.test.ts`, `lib/firebase.test.ts`, `services/http.test.ts` | 6 + 6 + 6 + 6 |
 
**Segurança.** Cobertos: sem token (401), token inválido ou expirado (401), papel incorreto (403), recurso de outra empresa (403 `ACESSO_NEGADO`; 404 para id inexistente) e `empresaId` de outra empresa na query ou no corpo (403). A tela trata 401 (volta ao login e depois à página de origem) e 403 `ACESSO_NEGADO` (página "Acesso negado"); 403 `EMPRESA_NAO_CONFIGURADA` fica na tela.
 
**Classificação por técnica.** Cerca de 50 dos testes unitários chamam funções internas (caixa branca: `score`, `consulta`, `normalizar`, política de retentativa). O restante passa pela API, pela interface ou pelo contrato (caixa preta ou intermediária). O plano não prevê técnica de caixa branca explícita; a cobertura de ramos (85,0%) é efeito dos testes de ramos.
 
## 3. Casos do plano: situação
 
Com teste que leva o ID: 36 de 39. Sem ID: **CT-FEED-005**, **CT-FEED-007** e **CT-CERT-008**.
 
| CT | Situação | Observação |
| --- | --- | --- |
| CT-FEED-007 | Existe teste sem etiqueta | "lista vazia por todos_descartados" em `PaginaFeed.test.tsx`; falta só colocar o ID no título |
| CT-CERT-008 | `it.todo` | A mensagem "Todas as certidões estão em dia" não existe no código |
| CT-FEED-005 | Sem teste | O componente não tem lógica de tempo para testar (fronteiras 0, 100 e 500 ms) |
| CT-FEED-008 | Parcial | Política de retentativa testada (5xx até 2 vezes, 4xx nunca) e erro com "Tentar novamente"; falta E2E com falha real |
| CT-FILT-002, CT-FILT-003 | Parcial | Testes de componente com mock; o texto do vazio na tela difere do plano |
| CT-CERT-002 | Parcial | Fronteiras de −1, 15, 16, 30 e 31 dias; a tela mostra a de 31 dias no grupo "Em dia", o plano diz que não aparece |
| CT-FILT-009 | Parcial | Medição em memória; falta medir com o emulador |
 
No plano esses casos são E2E; os testes atuais são de componente com mock (MSW) ou unitários.
 
## 4. Bugs e lacunas
 
A coluna "Situação" foi conferida no código da `main` em 19/11/2026.
 
| ID | Descrição | Severidade | Dono | Situação |
| --- | --- | --- | --- | --- |
| BUG-001 | O front não tinha tela de login: com `VITE_API_MOCK=false` toda chamada ia sem token e a API respondia 401. | Crítica para o CP2 | Front-End | ✅ Corrigido: existe `PaginaLogin` e os E2E-04 a 10 passam (06/11) |
| BUG-002 | `docker-compose.yml` e `client/.env.example` subiam o front com `VITE_API_MOCK=true` por padrão. | Alta | DevOps / Front-End | ✅ Corrigido: o padrão agora é `VITE_API_MOCK=false` |
| BUG-003 | O seed (`npm run seed`) não roda no `docker compose up`. | Média | Back-End / DevOps | ⚠️ Mitigado: o README documenta `docker compose exec api npm run seed`; o passo continua manual |
| BUG-004 | O CI não rodava os testes do cliente nem o teste de integração do deny-all. | Média | DevOps | ✅ Corrigido: o CI tem `client (lint + build + test)`, `playwright (mock)` e `firestore rules (test:int)` |
| BUG-005 | Não existia tela para configurar o perfil da empresa. | Alta (parte da Jornada 1 da ficha) | Front-End | ✅ Corrigido: existe `PaginaPerfil` (9 testes) |
 
> [!NOTE]
> IDs sugeridos pela PO a partir da revisão de 04/11. O QA confirma, abre as issues e ajusta a severidade.
 
### Lacunas abertas
 
- `openapi.yaml` documenta `GET /v1/editais/{editalId}` (US12), e o cliente tem a tela de detalhe, mas **não há rota no servidor** que implemente esse caminho. Decidir: implementar a rota ou tirar do contrato.
- O campo `servers` do `openapi.yaml` só lista `localhost:3333`; falta a URL do Render.
- CT-FEED-005, CT-FEED-007 (falta o ID no título do teste) e CT-CERT-008 (`it.todo`) continuam sem teste com ID.
- A coleção Postman e o Newman previstos na estratégia ainda não rodam no CI.
## 5. E2E contra a API real (06/11/2026, `main`)
 
| Cenário | Resultado |
| --- | --- |
| E2E-01 a 03: segurança pela API (sem token, papel, isolamento) | ✅ Passou |
| E2E-04: login (sem sessão, senha errada) | ✅ Passou |
| E2E-05: feed do decisor A com o edital A no topo | ✅ Passou |
| E2E-06: ranking da empresa B diferente do da A | ✅ Passou |
| E2E-07: filtro sem resultado e Limpar | ✅ Passou (após corrigir o teste, que navegava antes de o login terminar) |
| E2E-08: analista sem Certidões e com "Acesso negado" pela URL | ✅ Passou |
| E2E-09: renovar certidão urgente passa para "Em dia" | ✅ Passou (após corrigir a asserção do teste) |
| E2E-10: sair volta ao login | ✅ Passou |
| E2E-11: `/healthz` e `/readyz` | ❌ **Falha esperada**: rotas só existem no PR #25 (na `main` responde 401) |
| E2E-12: job de ingestão recusa sem token | ❌ **Falha esperada**: rota só existe no PR #25 (na `main` responde 404) |
 
As duas correções (E2E-07 e E2E-09) foram de teste, não de produto. A jornada de login, feed, filtros, certidões, isolamento e logout funcionou contra a API e o emulador reais. Isso comprova o fechamento do BUG-001 (login).
 
E2E-11 e E2E-12 falhavam por falta das rotas na `main`; elas já estão na `main` e passaram contra o Render em 16/11/2026 (seção 6).
 
## 6. Autorização contra a URL publicada (CP3) e jornada do kanban
 
| Item | Detalhe |
| --- | --- |
| Ambiente | API no Render, Firebase real, dados de teste do seed de produção (2 empresas, 3 usuários). Sem carga |
| Data | 16/11/2026 |
| Resultado | ✅ **13 de 13** passaram (25,9 s): 5 da suíte de segurança base e 8 do kanban |
 
**Casos cobertos**
 
- Sem token, token inválido e token expirado (401).
- Papel incorreto nas certidões (403).
- Empresa errada no feed, nas certidões e no kanban (403).
- `empresaId` forjado (403).
- Rotas do kanban: `POST`, `GET`, `PATCH` e `DELETE /acompanhamentos`.
- Job de ingestão com `INGEST_JOB_TOKEN` inválido (401).
**Conclusão:** nenhuma rota devolveu dados de outra empresa.
 
**Limitação:** o token expirado é simulado (JWT com `exp` vencido e assinatura falsa).
 
## 7. Como reproduzir
 
### Local (emulador)
 
```bash
# servidor (na main)
cd server && npm ci
npm run test:cov          # 220 testes
npm run test:int          # 27 testes de integração (precisa do emulador: docker compose up -d firebase)
 
# cliente
cd client && npm ci && npm run test:unit   # 97 testes + 1 todo
 
# E2E contra a API real
VITE_API_MOCK=false docker compose up --build               # terminal 1
docker compose exec api npm run seed                        # terminal 2
cd client && npx playwright test -c playwright.api-real.config.ts
```
 
### API publicada (Render)
 
```bash
cd client
export API_URL=https://licitaradar-y2gp.onrender.com
export E2E_SENHA=<senha do seed>
export FIREBASE_API_KEY=<apiKey web do Firebase>
npx playwright test -c playwright.api-real.config.ts --grep @segura

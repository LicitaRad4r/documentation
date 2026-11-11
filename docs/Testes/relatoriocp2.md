# Primeiro relatório de testes (Checkpoint 2)

**Projeto:** LicitaRadar · **Responsável pela qualidade:** Maria Júlia SEna · **Última atualização:** 04/11/2026

> Execução feita na branch `feat/api-base` (código das jornadas 1 a 4), que ainda não foi integrada à `main`. Depois do merge, rodar de novo na `main` e atualizar a data.
## 1. Resumo

| Item                                                                      | Resultado                                                                                                                 |
| ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Servidor: unitários, de API, de contrato e de ramos (`npm run test:unit`) | **195 de 195** passaram (14 arquivos) (174 unit/contrato e 21 de integração) (execução 11/11/2026)                                                                                   |
| Cliente: componentes e lógica (`npx vitest run`)                          | **72 passaram + 1 `todo`** (15 arquivos) (execução 11/11/2026)|
| Integração com emulador (deny-all do Firestore, `npm run test:int`)       | **21 de 21** (2 arquivos)  (execução 11/11/2026)|
| E2E contra a API real (`api-real.spec.ts`, 12 cenários)                   | **12 de 12 passaram**  (execução 11/11/2026)|
| Cobertura do código do produto (Todos Arquivos)                       | **79,21%** de instruções · 81,13% de ramos · 70,55% de funções · 79,25% de linhas                                             |
| Casos do plano (CT-…) com teste que leva o ID                             | **36 de 39** (eram 29)                                                                                                    |
| Contrato OpenAPI                                                          | v0.3.0; divergências do plano fechadas                                                                                    |

## 2. O que foi testado

| Frente                                   | Arquivos                                                                                                                               | Testes        | Casos do plano                                                                         |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------- | -------------------------------------------------------------------------------------- |
| Contrato OpenAPI × API                   | `server/tests/contract/openapi-contract.test.ts`                                                                                       | 18            | contrato v0.3                                                                          |
| Feed e filtros (API)                     | `server/tests/unit/feed.test.ts`                                                                                                       | 17            | CT-FEED-001 a 004, 006, 009; CT-FILT-001, 004 a 007                                    |
| Busca textual                            | `unit/busca-textual.test.ts`                                                                                                           | 22            | CT-FILT-006 e variações                                                                |
| Certidões (API)                          | `unit/certidoes.test.ts`                                                                                                               | 23            | CT-CERT-001, 003 a 007, 010                                                            |
| Perfil                                   | `unit/perfil.test.ts`                                                                                                                  | 10            | perfil da empresa                                                                      |
| Score                                    | `unit/score.test.ts`                                                                                                                   | 13            | cálculo e faixas                                                                       |
| **Score: 10 casos tabelados do Caderno** | `unit/score-tabelado.test.ts`                                                                                                          | 10            | fronteiras de valor, tolerância de 10%, sem valor, termo só na descrição, UF diferente |
| **Ramos (caixa branca)**                 | `unit/ramos.test.ts`                                                                                                                   | 11            | ramos de `score.ts` e `consulta.ts`                                                    |
| **Desempenho do feed**                   | `unit/desempenho.test.ts`                                                                                                              | 1             | CT-FILT-009 (p95 ≤ 800 ms, 200 amostras, em memória)                                   |
| Segurança (token, papel, isolamento)     | `unit/seguranca.test.ts`                                                                                                               | 14            | CT-SEG-001 a 009, 011                                                                  |
| CORS                                     | `unit/cors.test.ts`                                                                                                                    | 5             | preflight sem token; 401 com cabeçalho de CORS                                         |
| Erros e log                              | `unit/erros-log.test.ts`                                                                                                               | 3             | formato `Erro`, 500 genérico                                                           |
| **Ingestão do PNCP, saúde e job**        | `unit/ingestao.test.ts`                                                                                                                | 27            | `/healthz`, `/readyz`, rota do job com `INGEST_JOB_TOKEN` (casos novos a mapear na matriz) |
| Regras do Firestore (deny-all)           | `integration/firestore-deny.all.test.ts`                                                                                               | 21 (emulador) | CT-SEG-010                                                                         |
| Telas (cliente)                          | `client/src/**/*.test.ts(x)`                                                                                                           | 72 + 1 `todo` | feed, filtros, certidões, perfil, login e acesso, estados da consulta                  |
| **Novos no cliente**                     | `PaginaFeed.erro-filtros`, `PaginaFeed.complementos`, `PaginaCertidoes.falha-envio`, `PaginaCertidoes.fronteiras`, `lib/cache.test.ts` | 12            | CT-FILT-002, 003, 008; CT-CERT-002, 009; CT-FEED-008                                   |
| E2E contra a API real                    | `client/tests-e2e/api-real.spec.ts`                                                                                                    | 12            | CT-SEG-001 a 009, CT-FEED-003, jornadas 1 a 4 (ver seção 4)                            |

**Segurança.** Cobertos: sem token (401), token inválido ou expirado (401), papel incorreto (403), recurso de outra empresa (403 `ACESSO_NEGADO`; 404 para id inexistente) e `empresaId` de outra empresa na query ou no corpo (403). A tela trata 401 (volta ao login e depois à página de origem) e 403 `ACESSO_NEGADO` (página "Acesso negado"); 403 `EMPRESA_NAO_CONFIGURADA` fica na tela.

**Classificação por técnica.** Cerca de 50 dos testes unitários chamam funções internas (caixa branca: `score`, `consulta`, `normalizar`, política de retentativa). O restante passa pela API, pela interface ou pelo contrato (caixa preta ou intermediária). O plano não prevê técnica de caixa branca explícita; a cobertura de ramos (81,1%) é efeito dos testes de ramos.

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

## 4. Bugs e lacunas abertos

| ID | Descrição | Severidade | Dono |
| --- | --- | --- | --- |
| BUG-001 | O CI não roda os testes do cliente (só lint e build), o Playwright nem o teste de integração do deny-all. | Média | DevOps | **Aberto** |
| BUG-002 | Não existe tela para configurar o perfil da empresa (termos, UFs, modalidades, faixa de valor); só a chamada de API em `client/src/features/perfil/api.ts`. | Alta (é parte da Jornada 1 da ficha) | Front-End | **Aberto** |
| BUG-003 | `docker-compose.yml` e `.env.example` sobem o front com `VITE_API_MOCK=true` por padrão: a tela mostra dados falsos, contrariando "telas ligadas à API, sem dados falsos". | Alta | DevOps / Front-End | **Aberto** (o README explica como trocar, mas o padrão continua `true`; é preciso recriar o container `web` depois de mudar o `.env`) |

### Resultado do E2E contra a API real (11/11/2026, `main`)

| Cenário | Resultado |
| --- | --- |
| E2E-01 a 03 (segurança pela API: sem token, papel, isolamento) | Passou |
| E2E-04 login (sem sessão, senha errada) | Passou |
| E2E-05 feed do decisor A com o edital A no topo | Passou |
| E2E-06 ranking da empresa B diferente do da A | Passou |
| E2E-07 filtro sem resultado e Limpar | Passou (após corrigir o teste, que navegava antes de o login terminar) |
| E2E-08 analista sem Certidões e com "Acesso negado" pela URL | Passou |
| E2E-09 renovar certidão urgente passa para "Em dia" | Passou (após corrigir a asserção do teste) |
| E2E-10 sair volta ao login | Passou |
| E2E-11 `/healthz` e `/readyz` públicos, 200 e sem dados | Passou  |
| E2E-12 job de ingestão recusa sem token e com token de usuário do Firebase | Passou  |

As duas correções foram de teste, não de produto. A jornada de login, feed, filtros, certidões, isolamento e logout funcionou contra a API e o emulador reais. Isso comprova o fechamento do BUG-011 (login), que fica como corrigido.

Em 06/11 os E2E-11 e 12 falhavam de propósito, porque as rotas só existiam no PR #25; depois do merge passaram. Os E2E-07 e 09 precisaram de correção no teste (não no produto): o 07 navegava antes de o login terminar e o 09 procurava um texto que a tela não mostra. A jornada de login, feed, filtros, certidões, isolamento e logout funcionou contra a API e o emulador reais

## 5. Como reproduzir
Requisitos: Node 22.10 ou superior (o CI usa 24; `nvm use 24`), Docker aberto e o `.env` criado com `cp .env.example .env`.

```bash
# servidor (na branch feat/api-base, até o merge)
cd server && npm ci
npm run test:cov       
npm run test:int          
# cliente
cd client && npm ci && npx vitest run   

# E2E contra a API real
# no .env: VITE_API_MOCK=false
docker compose up --build -d
docker compose up -d --force-recreate web      # o front só lê o .env ao ser criado
docker compose exec api npm run seed           # repita antes de cada rodada (o E2E-09 altera uma certidão)
cd client && npx playwright test -c playwright.api-real.config.ts --reporter=list
```

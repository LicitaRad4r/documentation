# Primeiro relatório de testes (Checkpoint 2)

**Projeto:** LicitaRadar · **Responsável pela qualidade:** Maria Júlia SEna · **Última atualização:** 04/11/2026

> Execução feita na branch `feat/api-base` (código das jornadas 1 a 4), que ainda não foi integrada à `main`. Depois do merge, rodar de novo na `main` e atualizar a data.

## 1. Resumo

| Item | Resultado |
| --- | --- |
| Servidor: unitários + contrato (`npm run test:cov`) | **120 de 120** passaram · 8 arquivos |
| Cliente: componentes e regras de tela (`npx vitest run`) | **42 de 42** passaram · 7 arquivos |
| Integração com emulador: deny-all do Firestore (`npm run test:int`) | **21 de 21** passaram (execução de 28/10/2026) |
| **Total** | **183 testes, 0 falhas** |
| Cobertura do servidor (v8) | Linhas **98,3%** · Comandos 96,3% · Ramos 90,5% · Funções 98,1% |
| Matriz de rastreabilidade | 39 CTs: 34 com teste passando, 1 parcial, 4 pendentes |
| Bugs abertos | Ver seção 4 |

## 2. O que foi testado

| Frente | Arquivos | Testes | Casos do plano |
| --- | --- | --- | --- |
| Feed ranqueado e score (Jornada 1) | `server/tests/unit/feed.test.ts`, `score.test.ts` | parte dos 120 | CT-FEED-001 a 004, 006, 009 |
| Filtros (Jornada 2) | `server/tests/unit/feed.test.ts`, `busca-textual.test.ts` | parte dos 120 | CT-FILT-001, 004 a 007 |
| Certidões (Jornada 3) | `server/tests/unit/certidoes.test.ts` | parte dos 120 | CT-CERT-001, 003 a 007, 010 |
| Segurança e autorização (Jornada 4) | `server/tests/unit/seguranca.test.ts`, `erros-log.test.ts` | parte dos 120 | CT-SEG-001 a 009, 011 |
| Contrato OpenAPI v0.3 | `server/tests/contract/openapi-contract.test.ts` | parte dos 120 | vários |
| Regras do Firestore (deny-all) | `server/tests/integration/firestore-deny.all.test.ts` | 21 | CT-SEG-010 |
| Telas: estados de carregando, vazio, erro, filtros, certidões | `client/src/**/*.test.ts(x)` | 42 | CT-FEED-005, 008; CT-FILT-002, 003; CT-CERT-002, 007, 008 (parcial) |

**Autorização.** Toda rota protegida é testada com os 4 cenários pedidos pelo Caderno: sem token (CT-SEG-001), token inválido/adulterado (CT-SEG-002), token expirado (CT-SEG-003), papel errado (CT-SEG-005/006) e empresa errada (CT-SEG-007 a 009).

## 3. Pendências de teste

| CT | O que falta |
| --- | --- |
| CT-FEED-007 | Teste da mensagem "todos descartados" (o mock tem o cenário `descartados`, mas não há teste) |
| CT-FILT-008 | Erro durante a pesquisa mantendo os filtros |
| CT-FILT-009 | Medição do p95 de leitura (≤ 800 ms) com o seed |
| CT-CERT-008 | Testar o texto "Todas as certidões estão em dia" (hoje só a lista vazia) |
| CT-CERT-009 | Falha de rede no envio da renovação mantendo a certidão anterior |
| E2E | Os testes de tela rodam contra o mock (MSW). Falta ao menos um roteiro ponta a ponta contra a API real com o emulador |

## 4. Bugs e lacunas abertos

| ID | Descrição | Severidade | Dono |
| --- | --- | --- | --- |
| BUG-001 | O front não tem tela de login: com `VITE_API_MOCK=false` toda chamada vai sem token e a API responde 401. A demo ponta a ponta não roda sem isso. | Crítica para o CP2 | Front-End |
| BUG-002 | `docker-compose.yml` e `client/.env.example` sobem o front com `VITE_API_MOCK=true` por padrão: a tela mostra dados falsos, contrariando "telas ligadas à API, sem dados falsos". | Alta | DevOps / Front-End |
| BUG-003 | O seed (`npm run seed`) não roda no `docker compose up`; precisa de passo manual não documentado no README. | Média | Back-End / DevOps |
| BUG-004 | O CI não roda os testes do cliente (só lint e build) nem o teste de integração do deny-all. | Média | DevOps |
| BUG-005 | Não existe tela para configurar o perfil da empresa (termos, UFs, modalidades, faixa de valor); só a chamada de API em `client/src/features/perfil/api.ts`. | Alta (é parte da Jornada 1 da ficha) | Front-End |

> IDs sugeridos pela PO a partir da revisão de 04/11. O QA confirma, abre as issues e ajusta a severidade.

## 5. Como reproduzir

```bash
# servidor (na branch feat/api-base, até o merge)
cd server && npm ci
npm run test:cov          # 120 testes + cobertura
npm run test:int          # 21 testes do deny-all (precisa do emulador: docker compose up -d firebase)

# cliente
cd client && npm ci && npx vitest run   # 42 testes
```
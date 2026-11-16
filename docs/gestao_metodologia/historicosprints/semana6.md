# Semana 6 — Checkpoint 3: Nuvem, Integração e Jornada Nova

**Período:** 09/11/2026 – 16/11/2026 (Checkpoint 3 em 15/11; publicação do front e alertas fechados em 16/11)

## Objetivo da Sprint

Entregar o **Checkpoint 3**: ambiente publicado no custo zero (Firebase Hosting e API no Render Free), deploy automático, `/healthz` e `/readyz` com alerta, teste de fumaça e de autorização contra a URL pública, e **uma jornada nova como *Must* com critério numérico** (acompanhamento de editais, US20).

---

## Entregas

Caixas marcadas têm PR mesclado na `main`.

**Produto e documentação**

- [x] US20 como *Must have*, Jornada 2 no MVP; revisões R3 e R4 em [Revisões do Backlog](../../backlog/revisoes-backlog.md)
- [x] 47 de 68 critérios de aceite comprovados nas [Histórias de Usuário](../../backlog/historias.md), incluindo os 11 da US20
- [x] [Roteiro de Demonstração](../../backlog/roteirodemonstracao.md) do CP2 e do CP3
- [x] ADRs do Custo Zero fechados, incluindo o ADR-0013 de monitoramento ([#74](https://github.com/LicitaRad4r/documentation/pull/74))
- [x] Matriz e relatório de testes com a rodada contra a URL pública ([#75](https://github.com/LicitaRad4r/documentation/pull/75))
- [ ] Prints FinOps do Render, do GitHub Actions e do UptimeRobot; runbook de rollback

**Backend**

- [x] API de acompanhamentos (`POST`, `GET`, `PATCH`, `DELETE`), contrato OpenAPI e CORS — [#38](https://github.com/LicitaRad4r/LicitaRadar/pull/38)
- [x] Testes do `/readyz` e documentação de saúde — [#37](https://github.com/LicitaRad4r/LicitaRadar/pull/37)
- [x] Alerta de falha do job de ingestão por issue e e-mail — [#39](https://github.com/LicitaRad4r/LicitaRadar/pull/39)
- [x] Latência (`latenciaMs`) em todo log de requisição — [#45](https://github.com/LicitaRad4r/LicitaRadar/pull/45)
- [ ] `GET /v1/editais/{editalId}`; ingestão agendada ligada (secrets)

**Frontend**

- [x] Quadro Kanban da US20 com arrastar, Resultado e selos de prazo — [#40](https://github.com/LicitaRad4r/LicitaRadar/pull/40)
- [x] Tela de perfil (US08) — [#35](https://github.com/LicitaRad4r/LicitaRadar/pull/35) e [#41](https://github.com/LicitaRad4r/LicitaRadar/pull/41); tela de detalhe (US12) — [#34](https://github.com/LicitaRad4r/LicitaRadar/pull/34)

**Qualidade e DevOps**

- [x] CI com Vitest do cliente, Playwright e regras do Firestore — [#32](https://github.com/LicitaRad4r/LicitaRadar/pull/32)
- [x] `Dockerfile.render`, smoke test e k6 — [#31](https://github.com/LicitaRad4r/LicitaRadar/pull/31)
- [x] API no Render com auto-deploy na `main` (`licitaradar-y2gp.onrender.com`) — [#36](https://github.com/LicitaRad4r/LicitaRadar/pull/36)
- [x] **Front publicado em `licitaradar-13047.web.app`** depois do CI verde, com preview por PR e link no comentário — [#42](https://github.com/LicitaRad4r/LicitaRadar/pull/42)
- [x] Regras e índices do Firestore publicados pelo deploy; `render.yaml` e `docs/nuvem.md` — [#43](https://github.com/LicitaRad4r/LicitaRadar/pull/43) e [#44](https://github.com/LicitaRad4r/LicitaRadar/pull/44)
- [x] UptimeRobot no `/readyz` a cada 5 min, com e-mail; p95 medido no k6 — [#45](https://github.com/LicitaRad4r/LicitaRadar/pull/45)
- [x] 8 testes de autorização do kanban; 13 de 13 passaram contra o Render — [#46](https://github.com/LicitaRad4r/LicitaRadar/pull/46)

---

## Checkpoint 3 — situação em 16/11

| Critério do Gate 3 | Situação |
|---|---|
| `/healthz` e `/readyz` | Feito e publicado |
| Deploy automático e preview em PR | Feito: API no Render, front no Hosting, preview por PR (#42) |
| Alertas | Feito: falha de job (#39), indisponibilidade pelo UptimeRobot (#45), p95 no k6 |
| Segredos protegidos | Feito: Secrets do GitHub e do Render, contas separadas por função, gitleaks |
| Teste de fumaça na URL pública | Roda depois de cada deploy (#44) |
| Autorização na URL pública | 13 de 13 contra o Render, nenhuma rota devolveu dados de outra empresa |
| Jornada nova como *Must* com critério numérico | Feita de ponta a ponta (#38, #40) |
| Revisão do backlog em `/docs/backlog` | Publicada |
| FinOps (teto US$ 12) | US$ 0 |

**Números:** 16 PRs mesclados no código (6 deles em 16/11); 202 testes unitários no servidor, 97 no cliente, 26 E2E; k6 com p95 máximo de 46 ms na leitura (meta 800 ms) e 37 ms na escrita (meta 1500 ms).

**Contribuições:** Luís (#37, #38) · João (#34, #35, #39, #40, #41) · Kauan (#31, #46, deploy no Render, seed de produção, testes na URL pública) · Giovanni (#33, #36, #42–#45, projeto Firebase) · Júlia Neiva (#32, ADRs do Custo Zero) · Maria Júlia (revisão e aprovação dos PRs, backlog, histórias, roteiro).

---

## Débitos para a Semana 7

| Item | Dono | Impacto |
|---|---|---|
| Prints FinOps (Render, Actions, UptimeRobot); registrar a aprovação da ADR-0001 | PO | Alto |
| `GET /v1/editais/{editalId}` | Back-End | Alto |
| Ingestão agendada ligada (`ENABLE_PNCP_INGEST`) | DevOps | Médio |
| Domínios dos previews autorizados no Firebase Auth | DevOps | Médio |
| Feed paginado no banco (hoje lê a coleção inteira) | Back-End | Médio |
| Certidões fora do disco local do Render (ADR-0014) | Back-End | Médio |
| Limpeza do teste do kanban (`criadoAqui`) | QA | Baixo |
| Runbook de rollback | Tech Lead | Baixo |
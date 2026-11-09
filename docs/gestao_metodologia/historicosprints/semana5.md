# Semana 5 — Integração do Núcleo e Início do Checkpoint 3

**Período:** 02/11/2026 – 08/11/2026

Reconstruída a partir dos commits e PRs dos repositórios [LicitaRadar](https://github.com/LicitaRad4r/LicitaRadar) e [documentation](https://github.com/LicitaRad4r/documentation), do relatório de testes do CP2 e do backlog do CP2. Os números de testes foram conferidos em 08/11/2026. 

---

## Objetivo da Sprint

Integrar na `main` o núcleo escrito até o Gate 2, destravar o front da Jornada 4 (login), fechar os testes pendentes, e começar o **Checkpoint 3 — Nuvem e integração** (ingestão do PNCP, `/healthz`, `/readyz`, ambiente reproduzível).

---

## Entregas Realizadas

### Integração na `main`

- [x] PRs das Jornadas 2, 3 e 4, do contrato v0.3, dos testes de contrato e do plano de custo zero mesclados (#10, #11, #12, #14, #16, #17, #18), 02/11
- [x] `feat/api-base` (API completa do CP2) integrada à `main` (PR #13), 04/11

### Backend

- [x] Busca textual e log de erro 500 (correções da revisão do QA), 03/11
- [x] **CORS** para o front, com preflight sem token (04/11, PR #21)
- [x] **Ingestão do PNCP**, `/healthz` e `/readyz` (05/11, mesclado em 07/11, PR #25)
- [x] Correção do teste de contrato e dos tipos do front para o contrato v0.3 (03/11)

### Frontend

- [x] **Jornada 4:** tela de login e tratamento central de 401 e 403 (04/11, PR #24)
- [x] Camada `services`, selo de nota do score e estados revisados no feed (04/11)
- [x] Config do Firebase obrigatória, sem travar a sessão (06/11, PR #28)
- [x] Teste de contrato do servidor apontando para `client/src/services/schema.d.ts`
- [ ] Tela para configurar o perfil da empresa (BUG-005, ainda sem tela; só existe `perfil/api.ts`)

### Qualidade e Testes

- [x] Testes faltantes do CP2, testes E2E e **10 casos tabelados do score** (06/11, PR #26)
- [x] **Roteiro E2E contra a API real** (06/11) e relatório de testes atualizado
- [x] EP/BVA atualizado com a US10 (05/11)
- [x] E2E contra a API e o emulador reais: **10 de 12 cenários passaram** em 06/11; os 2 restantes (`/healthz`, `/readyz` e ingestão) dependiam do PR #25, hoje integrado
- [ ] 5 casos pendentes: CT-FEED-007, CT-FILT-008, CT-FILT-009 (p95 com emulador), CT-CERT-008 e CT-CERT-009
- [ ] CI rodando os testes do cliente e o deny-all (BUG-004)

### DevOps e Ambiente

- [x] **Guia oficial de como rodar** com Docker Compose (04 e 05/11, PR #22 e #50)
- [x] `.env` obrigatório no Compose (06/11, PR #29)
- [x] CODEOWNERS com a PO como revisora de todos os PRs (04 e 05/11)
- [ ] Compose com a API real por padrão: `VITE_API_MOCK=true` continua como padrão (BUG-002)
- [ ] Seed rodando no `docker compose up` (BUG-003)

### Documentação

- [x] **ADRs v0.3** com Status, decisões substituídas e pendências do feedback do CP1 e CP2 (05/11, PR #59)
- [x] **Backlog do CP2** e roteiro da demo do Gate 2 (05/11, PR #60)
- [x] Logs de segurança 401 e 403 (PR #40, 08/11)
- [x] Visão do produto, histórias, jornadas e relatórios atualizados (03 e 04/11)
- [x] Guia de como rodar e roteiro E2E publicados
- [ ] Revisões do backlog (`revisoesbacklog.md` ainda vazio)

### Checkpoint 3 (progresso)

| Critério do Gate 3 | Situação em 08/11 |
|---|---|
| `/healthz` e `/readyz` | Feito (código) |
| Logs JSON com `requestId`, rota, status e latência | Parcial: `requestId` nos erros; falta confirmar latência e rota |
| Deploy (Hosting e API publicada), preview em PR | Não iniciado: só existem `ci.yml` e `ingestao-pncp.yml` |
| Ingestão agendada | Código pronto; workflow desligado até definir `ENABLE_PNCP_INGEST` e os secrets |
| Alertas de 5xx, p95 e falha de job | Não iniciado |
| Segredos protegidos, imagens com SHA, sem `latest` | Não iniciado |
| FinOps (teto de US$ 12 no Gate 3) | US$ 0 gastos (plano gratuito) |

---

## Resumo Técnico

| Métrica | Valor |
|---------|-------|
| Commits no repositório de código | 12 |
| Commits no repositório de documentação | 13 |
| PRs mesclados | 24 (16 no código, 8 na documentação) |
| Testes do servidor (unitários e de contrato, rodados em 08/11) | 174 de 174 passando |
| Testes do cliente (rodados em 08/11) | 72 passando e 1 `todo` |
| Testes de integração do deny-all | 21 (relatório de 28/10; requerem emulador, não reexecutados em 08/11) |
| E2E contra a API real (06/11) | 10 de 12 passaram (2 dependiam do PR #25) |
| Casos do plano com teste identificado | 36 de 39 (relatório de 04/11) |
| Gasto de nuvem | US$ 0 |

!!! warning "Números do relatório desatualizados"
    O relatório de 04/11 cita 147 testes de servidor e 60 de cliente. Em 08/11 os testes são 174 e 72. Atualize o relatório e a matriz antes do Gate 3.

---

## Contribuições da Equipe

### PO (Maria Júlia Sena)

- **Foco:** backlog do CP2 e roteiro da demo, atualização de histórias, jornadas e visão, revisão de todos os PRs.
- **Commits:** `e3da7c1`, `be4dab2`, `f2e031c`, `5bc3380`, `df88c6d`.

### Tech Lead (Júlia Neiva)

- **Foco:** ADRs v0.3, logs de segurança, revisão e merge dos PRs.
- **Commits:** `812ffc0`, `432458e`.

### Back-End (Luís Felipe Albuquerque)

- **Foco:** CORS, correções do QA, ingestão do PNCP, `/healthz` e `/readyz`.
- **Commits:** `df60607`, `8ce626a`, `4a0320c`, `287db28`.

### Front-End (João Nunes)

- **Foco:** Jornada 4 (login, 401 e 403), camada de serviços e config do Firebase.
- **Commits:** `21b2202`, `3ca1796`, `53c9667`, `0de6db6`.

### QA (Kauan Tarchetti)

- **Foco:** testes faltantes do CP2, E2E contra a API real, casos tabelados do score e relatório.
- **Commits:** `3bf232f`, `70ffac0`, `837ef21`, `47c5362`.

### DevOps (Giovanni Ferreira)

- **Foco:** guia de como rodar, `.env` obrigatório e CODEOWNERS.
- **Commits:** `8e3f6d1`, `ab569df`, `f71064e`, `7d19bf5`.

---

## Aprendizados e Decisões

!!! info "Decisão: ingestão do PNCP agendada pelo GitHub Actions"
    O workflow `ingestao-pncp.yml` chama uma rota protegida da API (ADR-0007) e fica desligado até a variável `ENABLE_PNCP_INGEST=true` e os secrets `API_BASE_URL` e `INGEST_JOB_TOKEN` existirem. O ADR-0007 continua **Proposto**, com pendência de decisão e de evidência do contrato real do PNCP.

!!! info "Decisão: registrar divergências entre ADR e código"
    O ADR v0.3 passou a registrar o que diverge do código (persistência em PostgreSQL × Firestore, `/health` × `/healthz`, rota de ingestão, contratos OpenAPI) em vez de reescrever as decisões sem rastro.

!!! warning "Risco: Gate 3 sem a base de deploy"
    O Caderno pede Firebase Hosting, Cloud Run, Workload Identity Federation e Secret Manager. O time adotou custo zero (Firebase Spark, Neon, Render), ainda com o ADR-0001 como Proposto. **Antes de 15/11** é preciso registrar a aprovação do tutor para o desvio.

!!! warning "Risco: pendências do Gate 2 ainda abertas"
    Tela de perfil, Compose com a API real, seed no Compose, CI com testes do cliente e do deny-all e 5 casos de teste continuam em aberto.

---

## Débitos Técnicos para a Próxima Sprint

IDs do backlog do CP2.

| ID | Descrição | Impacto | Prazo |
|----|-----------|---------|-------|
| G2-05 | Tela para configurar o perfil (US08, Must) | Alto | 09/11 |
| G2-02 | Compose com a API real por padrão, sem o mock | Alto | 07/11 |
| G2-03 | Rodar o seed no `docker compose up` ou documentar o passo | Médio | 07/11 |
| G2-04 | CI rodando testes do cliente e o deny-all com emulador | Médio | 09/11 |
| G2-06 | Fechar CT-FEED-007, CT-FILT-008, CT-FILT-009, CT-CERT-008 e CT-CERT-009 | Alto | 09/11 |
| G2-08 | Atualizar a matriz de rastreabilidade e o relatório de testes | Médio | 08/11 |
| G2-09 | Alinhar ADRs 0005, 0006 e 0007 ao código e atualizar `Arquitetura.md` | Alto | 07/11 |
| G2-10 | Preencher `revisoesbacklog.md` com ao menos 2 revisões | Médio | 08/11 |
| G2-11 | Ingestão incremental do PNCP rodando localmente, sem duplicar | Alto | 12/11 |
| G2-12 | Roteiro e ensaio de 15 minutos da demo | Médio | 12/11 |
| CP3 | Deploy, preview em PR, alertas, logs JSON completos e segredos protegidos | Crítico | 15/11 |
| US20 | Acompanhar edital: só entra no MVP se estiver em PR até 15/11 | Médio | 15/11 |

---

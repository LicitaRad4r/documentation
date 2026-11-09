# Semana 4 — Núcleo Funcional (Checkpoint 2)

**Período:** 26/10/2026 – 01/11/2026 (Gate 2 em 01/11)

Reconstruída a partir dos commits e PRs dos repositórios [LicitaRadar](https://github.com/LicitaRad4r/LicitaRadar) e [documentation](https://github.com/LicitaRad4r/documentation). 

---

## Objetivo da Sprint

Fechar o **Checkpoint 2 — Núcleo funcional**: 3 a 5 jornadas ponta a ponta rodando localmente, API integrada ao front, regras deny-all no Firestore, testes de segurança, EP/BVA v1, matriz de rastreabilidade e primeiro relatório de testes.

---

## Entregas Realizadas

### Backend (API Fastify + Zod)

- [x] **Jornada 1:** feed ranqueado por score determinístico, com seed do golden set (31/10)
- [x] **Jornada 2:** filtros dinâmicos no feed, validados com Zod (31/10)
- [x] **Jornada 3:** certidões, com `GET /certidoes` e `PUT /certidoes/{id}/renovacao` (01/11)
- [x] **Jornada 4:** segurança, com hook único de autenticação, `requestId` nos erros, `GET /certidoes/{id}` e casos CT-SEG-001 a 011 (01/11)
- [x] Correção do `PUT /perfil` (ignora `empresaId` nulo vindo do eco do GET)

### Contrato OpenAPI

- [x] v0.2 para o Checkpoint 2 (30/10)
- [x] Campo `totalIngeridos` na resposta do feed (30/10)
- [x] v0.3 com os ajustes pedidos pelo front (30/10)
- [x] Alinhamento com RF03, US06 e a matriz de permissões (31/10)
- [x] v0.3 final: `GET /certidoes/{id}`, 403 no perfil, `FeedItem` completo e claim `orgId` (01/11)

### Frontend (React)

- [x] Integração da API ao front, com estados de carregando, vazio, erro e sucesso (28/10)
- [x] Página do feed com paginação e faixa de aderência (28/10)
- [x] Adaptação ao contrato v0.3 (01/11)
- [x] Painel de certidões do decisor com renovação (01/11)
- [x] Barra de filtros com etiquetas removíveis (01/11)
- [ ] Tela de login (entregue só em 04/11, na Semana 5)
- [ ] Tela para configurar o perfil da empresa (não existe até 08/11)

### Qualidade e Testes

- [x] Regras do Firestore **deny-all** e teste de integração (CT-SEG-10), 28/10
- [x] **Testes de contrato** OpenAPI × API (31/10)
- [x] **Matriz de rastreabilidade** criada (28/10 e 31/10)
- [ ] Matriz com teste e resultado nos 39 casos (hoje só o CT-SEG-010 está preenchido, G2-08)
- [x] Relatório de testes do deny-all e documentação de testes no MkDocs
- [x] Revisão das jornadas do usuário (30/10)

### FinOps, DevOps e Arquitetura

- [x] **Plano custo zero** adotado, com evidência do plano Firebase Spark (31/10)
- [x] ADRs revisados para custo zero (v0.2), 30/10
- [x] **Gitleaks** no CI, usando a CLI sem licença de organização (31/10)
- [x] Definition of Ready adicionada e DoD movida para Requisitos (31/10)
- [ ] Alertas de orçamento 50/80/100% (substituídos pelas evidências do plano gratuito, sujeito a aprovação do tutor)

### Segurança

- [x] Firestore deny-all: o navegador nunca acessa o banco direto
- [x] Casos de segurança: sem token, token inválido ou expirado, papel incorreto, tenant incorreto e acesso indevido

---

## Resumo Técnico

| Métrica | Valor |
|---------|-------|
| Commits no repositório de código | 23 |
| Commits no repositório de documentação | 16 |
| PRs mesclados | 13 (4 no código, 9 na documentação) |
| Jornadas implementadas na API | 4 (feed, filtros, certidões, segurança) |
| Versões do contrato OpenAPI | v0.2 e v0.3 em 3 dias (30/10 a 01/11), a partir da v0.1 de 18/10 |
| Casos de segurança implementados | CT-SEG-001 a 011 |
| Gasto de nuvem | US$ 0 (plano gratuito) |

---

## Contribuições da Equipe

### PO (Maria Júlia Sena)

- **Foco:** correções de navegação e MoSCoW e revisão dos PRs de documentação.
- **Commits:** `d67cda2`, `35c57f8`, `3eca69b` e merges de PR.

### Tech Lead (Júlia Neiva)

- **Foco:** revisão para custo zero (ADR v0.2), DoR, DoD e revisão e merge dos PRs.
- **Commits:** `9030217`, `1e6700b`, `3e5e843`.

### Back-End (Luís Felipe Albuquerque)

- **Foco:** Jornadas 1 a 4 da API e contrato OpenAPI v0.2 e v0.3.
- **Commits:** `f834c4a`, `e7cd430`, `7b4b721`, `17a0fb6`, `649b2ce`, `0f70165`, `dfaa273`.

### Front-End (João Nunes)

- **Foco:** integração com a API, feed, filtros e certidões.
- **Commits:** `f8b5af7`, `bf95f58`, `f0fa4dd`, `824f87b`, `77350d2`.

### QA (Kauan Tarchetti)

- **Foco:** deny-all, testes de contrato, matriz de rastreabilidade e relatórios.
- **Commits:** `c86090c`, `f95b2f3`, `3a7a38a`, `f683755`, `f0168c3`.

### DevOps (Giovanni Ferreira)

- **Foco:** plano custo zero, evidência do Spark e gitleaks no CI.
- **Commits:** `5a1f0b7`, `55ebee1`, `20842c0`.

---

## Aprendizados e Decisões

!!! info "Decisão: plano de custo zero (30 a 31/10)"
    Sem voucher de nuvem, não há como configurar alertas de orçamento 50/80/100%. O time adotou Firebase Spark, Neon e Render, e a prova de custo passou a ser o conjunto de prints dos planos gratuitos. O **ADR-0001 segue como Proposto**: falta registrar quem aprovou e a data, e é preciso confirmar com o tutor que isso substitui o critério do Caderno.

!!! info "Decisão: contrato OpenAPI como acordo entre front e back"
    O contrato passou da v0.1 (18/10) para a v0.2 e a v0.3 entre 30/10 e 01/11, com ajustes pedidos pelo front. Os testes de contrato (`openapi-contract.test.ts`) impedem que API e especificação divirjam.

!!! info "Decisão: Firestore deny-all"
    O navegador nunca lê nem escreve direto no banco. Todo acesso passa pela API, que valida token, papel e empresa.

!!! warning "Risco: Gate 2 formalmente atrasado"
    O núcleo foi escrito entre 28/10 e 01/11 e integrado à `main` só em 04/11. A demo local sem configuração oculta ainda dependia do login no front, do Compose com a API real e do seed (ver débitos).

---

## Débitos Técnicos para a Próxima Sprint

Registrados no relatório de testes de 04/11 e no backlog do CP2.

| ID | Descrição | Impacto |
|----|-----------|---------|
| BUG-001 | Front sem tela de login: com a API real, toda chamada vai sem token | Crítico para o CP2 |
| BUG-002 | Compose sobe o front com `VITE_API_MOCK=true` (dados falsos) | Alto |
| BUG-003 | Seed não roda no `docker compose up` e o passo não está no README | Médio |
| BUG-004 | CI não roda os testes do cliente nem o deny-all com emulador | Médio |
| BUG-005 | Não existe tela para configurar o perfil da empresa (US08, Must) | Alto |
| G2-06 | 5 casos de teste pendentes: CT-FEED-007, CT-FILT-008, CT-FILT-009, CT-CERT-008 e CT-CERT-009 | Alto |
| G2-09 | ADRs 0005, 0006 e 0007 divergem do código (só Firestore; ingestão por rota inexistente) | Alto |

---
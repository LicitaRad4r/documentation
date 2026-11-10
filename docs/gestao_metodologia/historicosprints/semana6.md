# Semana 6 — Checkpoint 3: Nuvem, Integração e Jornada Nova

**Período:** 09/11/2026 – 15/11/2026 (Checkpoint 3 em 15/11/2026)

!!! info "Registro em andamento"
    Esta página foi aberta em 09/11/2026 e é atualizada até a entrega. As caixas marcadas `[x]` têm evidência (PR mesclado ou arquivo na `main`). Contribuições por pessoa e números de testes serão preenchidos no fechamento da sprint, a partir dos commits e PRs.

---

## Objetivo da Sprint

Entregar o **Checkpoint 3**: ambiente publicado no custo zero (Firebase Hosting e API no Render Free), deploy automático, `/healthz` e `/readyz` com alerta, teste de fumaça e de autorização contra a URL pública, e **uma jornada nova como *Must* com critério de aceite numérico** (acompanhamento de editais, US20).

---

## Entregas

### Produto e Documentação

- [x] **US20 passou a *Must have*** (09/11). Registrado em [Revisões do Backlog](../../backlog/revisoes-backlog.md), na [Matriz MoSCoW](../../backlog/MoSCoW.md), nas [Histórias de Usuário](../../backlog/historias.md) e no [Escopo do MVP](../../requirements/produto/negocio/visao/escopo.md)
- [x] Jornadas atualizadas: Acompanhamento até o resultado é a **Jornada 2** e entra no MVP
- [x] Documentação reorganizada e links corrigidos; `mkdocs build --strict` passa sem avisos
- [x] Documentos de arquitetura (`Arquitetura.md`, `backend.md`, `infraestrutura.md`) alinhados ao código, com o acompanhamento marcado como planejado
- [ ] Revisão do backlog da S5/S6 publicada em `/docs/backlog` (feita nesta página e em `revisoes-backlog.md`, falta o PR ser mesclado)
- [ ] ADRs revisados pela Tech Lead (Neon → Firestore, status "Aceito", UptimeRobot e arquivos de certidão)
- [ ] Prints do FinOps (Render, UptimeRobot, GitHub) em `docs/architecture/finops/`
- [ ] Runbook de rollback
- [ ] Roteiro da demo de 15/11

### Backend

- [ ] Rotas `POST`, `GET`, `PATCH` e `DELETE` de `/v1/acompanhamentos` com as regras da US20
- [ ] CORS liberando `PATCH` e `DELETE`
- [ ] Contrato OpenAPI com as rotas de acompanhamento e `npm run gen:api`
- [ ] Dockerfile de produção da API (hoje as imagens são de desenvolvimento)
- [ ] Ingestão agendada ligada: `ENABLE_PNCP_INGEST=true` e os secrets `API_BASE_URL` e `INGEST_JOB_TOKEN`

### Frontend

- [ ] Quadro "Acompanhados" com as colunas Interessado, Preparando, Enviado e Resultado, ordenado pelo prazo mais próximo e com destaque de 0 a 3 dias
- [ ] Tela de perfil da empresa (US08, ainda sem tela)
- [ ] Estado de "carregando" para a primeira chamada à API acordada (cold start de cerca de 1 minuto)
- [ ] Tela de detalhe do edital (US12)

### Qualidade e Testes

- [ ] Testes de autorização da US20 (`401`, `403`, `404`, `422`)
- [ ] Teste de fumaça após o deploy
- [ ] Testes de autorização contra a URL publicada, com relatório
- [ ] CI rodando os testes do cliente e os de integração com emulador
- [ ] Atualizar a matriz de rastreabilidade e o relatório de testes do CP3

### DevOps e Ambiente

- [ ] Deploy automático do frontend no Firebase Hosting (canal de preview em PR e produção na `main`)
- [ ] Deploy automático da API no Render
- [ ] Configuração como código: `firebase.json` com Hosting, `firestore.indexes.json`, `render.yaml` e `.env.example` completo
- [ ] UptimeRobot consultando `/readyz` a cada 5 minutos, com alerta por e-mail

---

## Checkpoint 3 (acompanhamento)

| Critério do Gate 3 | Situação em 09/11 |
|---|---|
| `/healthz` e `/readyz` | Feito (código) |
| Deploy automático e preview em PR | Não iniciado |
| Alertas (indisponibilidade e falha de job) | Não iniciado |
| Segredos protegidos | Plano definido (GitHub Secrets e variáveis do Render), exceção ao Workload Identity a registrar em ADR |
| Teste de fumaça e testes de autorização na URL pública | Não iniciado |
| Jornada nova como *Must* com critério numérico | Definida (US20); implementação pendente |
| Revisão do backlog em `/docs/backlog` | Escrita nesta sprint |
| FinOps (teto de US$ 12; Custo Zero = US$ 0) | US$ 0 gastos; prints pendentes |

---

## Aprendizados e Decisões

!!! info "Decisão: US20 é a jornada nova do Checkpoint 3"
    Em 09/11/2026 o acompanhamento de editais passou de *Should have* condicional para *Must have*. Motivo e impacto em [Revisões do Backlog](../../backlog/revisoes-backlog.md), Revisão 1.

!!! warning "Risco: tempo para entregar o quadro e o deploy na mesma semana"
    O quadro de acompanhamento depende de rotas novas no back e de uma tela nova no front, e o deploy depende de arquivos de configuração que ainda não existem. A ordem sugerida é: rotas e contrato primeiro, tela depois, deploy em paralelo.

!!! warning "Risco: arquivos de certidão em disco local"
    Em produção no Render Free o disco é perdido a cada deploy. Falta decidir onde guardar os arquivos (só metadados, Supabase Storage com exceção em ADR, ou outra opção).

!!! warning "Risco: aprovação do Custo Zero ainda registrada como *Proposto*"
    Os ADRs 0001, 0004 e 0010 seguem como *Propostos*. O registro da aprovação do tutor precisa entrar antes de 15/11.

---

## Débitos Técnicos para a Próxima Sprint

Pendências da Semana 5 que seguem abertas: tela de perfil (G2-05), Compose com a API real por padrão (G2-02), seed no Compose (G2-03), CI com testes do cliente e do deny-all (G2-04) e os 5 casos de teste pendentes (G2-06).
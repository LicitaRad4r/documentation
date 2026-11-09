# Revisões do Backlog

O backlog é refinado toda semana, na sprint "Backlog refinado". Cada revisão registra **a data, o que mudou de prioridade e por quê**. Este documento é o registro oficial; o estado atual das prioridades está na [Matriz MoSCoW](MoSCoW.md) e as pendências do Gate 2 estão no [Backlog do CP2](backlogcp2.md).

### Como ler as fontes

- **Git**: a mudança aparece em um commit verificável (o hash está na tabela).
- **Documento**: o motivo está escrito em uma página do repositório.

## Critério de pronto

| Critério | Situação |
|----------|----------|
| Pelo menos 2 revisões registradas | Atendido: 3 registradas (S3, S4 e S5) |
| Uma revisão na S3 | Atendido: R1, de 22/10, com evidência no Git |
| Uma revisão na S4 | **Parcial:** R2 (30 e 31/10) registra mudanças de escopo e de custo, mas a Matriz MoSCoW não mudou na S4. A PO confirma se houve reunião e a data |

## Resumo

| Revisão | Sprint | Data | Registrada em | Principais mudanças |
|---------|--------|------|---------------|---------------------|
| R1 | S3 | 22/10/2026 | 08/11/2026 | MoSCoW refeita sobre RF e histórias; filtros de Must para Should; e-mail, push e CSV de Should para Could |
| R2 | S4 | 30 e 31/10/2026 | 08/11/2026 | Alertas de orçamento trocados pelo plano de custo zero; histórias renumeradas na matriz de testes |
| R3 | S5 | 03 a 05/11/2026 | 08/11/2026 | Cadastro, recuperação de senha e convites de Must para Could; acompanhar edital (US20) entra com prazo |

---

## R1 — Sprint 3 — 22/10/2026

**Responsável:** PO · **Commit:** [`0eeb12e`](https://github.com/LicitaRad4r/documentation/commit/0eeb12e) · **Participantes:** [confirmar]

A MoSCoW de 17/10 era uma lista de funcionalidades. Nesta revisão ela passou a ser montada a partir dos Requisitos Funcionais e das histórias de usuário (US), para sustentar a rastreabilidade exigida no Gate 2.

| Item | Antes | Depois | Por quê | Fonte |
|------|-------|--------|---------|-------|
| Filtros do feed (US10, RF05) | Must | **Should** | Os filtros só restringem o feed já ranqueado (US09 e US11), que é o núcleo | Documento |
| Acessos, autenticação, convites e isolamento (US01 a US06) | Sem item | **Must** | O Gate 2 exige testes de papel e de tenant incorreto | Documento (Cronograma) |
| Detalhes e anexos do edital (US12) | Sem item | **Must** | [confirmar] | Git |
| Fluxo de decisão (US13, US17 a US19) | Sem item | **Should** | Importante para o fluxo de valor completo | Documento |
| Alerta de vencimento de certidões (US14 a US16) | Must | Must | Sem mudança de prioridade; passou a citar as histórias | Git |
| Notificações por e-mail e push | Should | **Could** | O alerta visual dentro da plataforma basta para validar o produto | Documento |
| Exportação para CSV ou Excel | Should | **Could** | Desejável, mas não bloqueia a validação do produto | Documento |
| Palavras negativas, integração com calendário, dashboard de ROI | Should ou Could | **Fora da matriz** | [confirmar] | Git |
| Deploy na nuvem | Won't ("Semana 2") | **Removido do Won't** | O Cronograma prevê deploy no Gate 3 (S5 e S6) | Documento (Cronograma) |
| Robôs de lances, envio direto de propostas, leitura de PDF com IA ou OCR, outros portais além do PNCP | Sem item (só "submissão automática") | **Won't** | Alinhado ao "não entra neste MVP" do Guia de Projetos | Documento (Guia) |

**Também na S3 (24/10):** o QA definiu a ordem de execução dos testes por risco (segurança, certidões, feed, filtros, contrato e E2E), em [`criterios-prioritarios.md`](../Testes/criterios-prioritarios.md), commit [`17d99a0`](https://github.com/LicitaRad4r/documentation/commit/17d99a0). O motivo é a ordem dos riscos R1 a R10 da estratégia de testes.

---

## R2 — Sprint 4 — 30 e 31/10/2026

**Responsável:** PO e Tech Lead · **Commits:** [`9030217`](https://github.com/LicitaRad4r/documentation/commit/9030217), [`e8567d7`](https://github.com/LicitaRad4r/documentation/commit/e8567d7) · **Participantes:** [confirmar]


| Item | Antes | Depois | Por quê | Fonte |
|------|-------|--------|---------|-------|
| Alertas de orçamento de 50, 80 e 100% (exigência do Gate 1 e do Caderno) | Exigidos | **Substituídos** pelos prints dos planos gratuitos | O voucher de US$ 50 acabou e o teto de custo passou a ser US$ 0 | Documento (ADRs v0.2, FinOps) |
| Plataforma: Google Cloud, Cloud Run e PostgreSQL | Prevista nos ADRs v0.1 | **Firebase Spark, Neon e Render** | Mesmo motivo: teto de US$ 0. O ADR-0001 segue como Proposto até a aprovação do tutor | Documento (ADRs) |
| Histórias citadas nos casos de teste | Numeração antiga (por exemplo US04 e US05) | **Numeração de 22/10** (US09, US10 e US11) | Alinhar a matriz de rastreabilidade à renumeração da R1 | Git |

---

## R3 — Sprint 5 — 03 a 05/11/2026

**Responsável:** PO · **Commits:** [`e3da7c1`](https://github.com/LicitaRad4r/documentation/commit/e3da7c1) (03/11), [`be4dab2`](https://github.com/LicitaRad4r/documentation/commit/be4dab2) (04/11), [`df88c6d`](https://github.com/LicitaRad4r/documentation/commit/df88c6d) (05/11, backlog do CP2) · **Participantes:** [confirmar]

É a maior revisão do ciclo: o MVP foi reduzido ao núcleo (feed ranqueado, certidões e acompanhamento do edital).

| Item | Antes | Depois | Por quê | Fonte |
|------|-------|--------|---------|-------|
| Cadastro self-service da empresa (US01) | Must | **Could** | No MVP, empresas e usuários são criados por script | Documento |
| Recuperação de senha (US03) | Must | **Could** | O MVP usa a recuperação padrão do Firebase Auth | Documento |
| Convites da Analista (US04 e US05) | Must | **Could** | [confirmar] | Git |
| Fluxo de decisão (US13, US17 a US19) | Should | **Could** | Foi substituído no MVP por "Acompanhar edital" (US20) | Documento |
| Acompanhar edital (US20) | Sem item | **Should, condicional** (entrou como Must em 03/11 e passou a Should em 04/11) | Entra no MVP se estiver em PR até 15/11; senão vira Could. A ingestão do PNCP, a tela de perfil e a ligação do front à API vêm antes | Documento |
| Contador de editais novos (US21) | Should | **Could** | Depende da ingestão do PNCP e de guardar a última visita de cada usuário | Documento |
| Detalhe do edital com link para o PNCP (US12) | Must | **Should** | [confirmar] | Git |
| Predição de chance de vitória | Sem item | **Won't** | [confirmar] | Git |
| Pendências do Gate 2 (G2-01 a G2-13) | Dispersas em relatórios | **Backlog único**, com dono, critério de aceite e prazo; 12 itens Must e 1 Should | Fechar o Gate 2 antes de qualquer item de deploy | Documento (Backlog do CP2) |

---
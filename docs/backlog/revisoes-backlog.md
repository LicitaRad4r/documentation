# Revisões do Backlog

O backlog é revisto toda semana. Esta página registra o que mudou de prioridade ou de critério, por quê, e onde a mudança está refletida. O estado atual está na [Matriz MoSCoW](MoSCoW.md) e nas [Histórias de Usuário](historias.md); o recorte do MVP, no [Escopo do MVP](../requirements/produto/negocio/visao/escopo.md).

## Linha do tempo

| Revisão | Sprint | Data | Em uma frase |
| :--- | :--- | :--- | :--- |
| R1 | Semana 3 | 22/10 | MoSCoW refeita sobre RF e histórias; fronteira do MVP definida |
| R2 | Semana 4 | 30–31/10 | Plano Custo Zero substitui o GCP pago |
| R3 | Semana 5 | 03–05/11 | MVP reduzido ao núcleo; US20 entra condicional; pendências do Gate 2 viram backlog |
| R4 | Semana 6 | 09/11 | US20 vira *Must* com critérios numéricos; escopo do CP3 fechado |

---

### R1 — Semana 3 · 22/10/2026

**Contexto.** A MoSCoW de 17/10 era uma lista de funcionalidades sem ligação com os requisitos. O Gate 2 pedia rastreabilidade RF → história → teste.

**Decisões.**
- Entram como *Must*: acessos, autenticação e isolamento (US01–US06) e detalhe do edital (US12).
- Descem: filtros do feed (US10) para *Should*; notificações por e-mail/push e exportação CSV para *Could*.
- Fora do MVP (*Won't*): robôs de lances, envio de propostas, leitura de PDF com IA, outros portais além do PNCP.
- Deploy na nuvem deixa de ser *Won't* — o Cronograma o prevê no Gate 3.

**Evidência.** Commit [`0eeb12e`](https://github.com/LicitaRad4r/documentation/commit/0eeb12e); ordem de testes por risco em [`criterios-prioritarios.md`](../Testes/criterios-prioritarios.md).

---

### R2 — Semana 4 · 30 e 31/10/2026

**Contexto.** O voucher de US$ 50 do GCP acabou. O teto de custo do projeto passou a ser US$ 0.

**Decisões.**
- Plataforma: de Google Cloud / Cloud Run / PostgreSQL para Firebase Spark + Render Free (ADR-0001, em *Proposto* até a aprovação do tutor).
- Alertas de orçamento de 50/80/100% substituídos por prints dos planos gratuitos como evidência FinOps.
- Casos de teste renumerados para as histórias da R1.

**Evidência.** Commits [`9030217`](https://github.com/LicitaRad4r/documentation/commit/9030217) e [`e8567d7`](https://github.com/LicitaRad4r/documentation/commit/e8567d7).

---

### R3 — Semana 5 · 03 a 05/11/2026

**Contexto.** Quinto dia da Sprint 5 com itens do Gate 2 em atraso. O time precisava fechar o núcleo antes de qualquer deploy.

**Decisões.**
- Para *Could*: cadastro self-service (US01), recuperação de senha (US03), convites (US04, US05), fluxo de decisão (US13, US17–US19), contador de editais novos (US21).
- Para *Should*: detalhe do edital (US12).
- Entra "Acompanhar edital" (US20) como *Should* condicional — vira *Must* se estiver em PR até 15/11.
- As 13 pendências do Gate 2 (G2-01 a G2-13) passam a um backlog único, com dono, critério de aceite e prazo.

**Evidência.** Commits [`e3da7c1`](https://github.com/LicitaRad4r/documentation/commit/e3da7c1), [`be4dab2`](https://github.com/LicitaRad4r/documentation/commit/be4dab2) e [`df88c6d`](https://github.com/LicitaRad4r/documentation/commit/df88c6d).

---

### R4 — Semana 6 · 09/11/2026

**Contexto.** O Guia do Checkpoint 3 exige uma jornada nova da S5–S6 como *Must*, com critério de aceite numérico.

**Decisões.**
- US20 sobe para ***Must*** e passa a ser a Jornada 2 ("Acompanhamento até o resultado").
- Critérios da US20 ganham números: "Prazo próximo" de 0 a 3 dias corridos (Brasília); "Prazo encerrado" no fim da lista; p95 ≤ 800 ms na leitura e ≤ 1,5 s na escrita; uma etapa por vez, `422` em transição inválida; Resultado com "Venceu"/"Não venceu" e imutável; `401`, `403` e `404` definidos.
- Escopo do CP3 fechado (tabela abaixo).

**Evidência.** `MoSCoW.md`, `historias.md` (US20), `jornadas.md` (Jornada 2), `escopo.md`.

---
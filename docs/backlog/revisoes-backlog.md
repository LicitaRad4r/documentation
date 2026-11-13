# Revisões do Backlog

Registro das mudanças de prioridade e de critérios do backlog. Cada revisão diz o que mudou, por quê e onde a mudança está refletida. Os documentos de referência são as [Histórias de Usuário](historias.md), a [Matriz MoSCoW](MoSCoW.md) e o [Escopo do MVP](../requirements/produto/negocio/visao/escopo.md).

---

## Revisão 1 — US20 (Acompanhar edital) passa a *Must have*

**Data:** 09/11/2026 · **Sprint:** Semana 6 · **Responsável:** PO

| | |
| :--- | :--- |
| **Antes** | *Should have*, condicional: entrava no MVP só se estivesse em PR até 15/11/2026. |
| **Depois** | ***Must have***. É a jornada nova do Checkpoint 3 (Jornada 2, "Acompanhamento até o resultado"). |
| **Motivo** | O Checkpoint 3 exige uma jornada nova da S5–S6 como *Must*, com critério de aceite numérico. O time ainda não tinha uma jornada nova com esse status, e o acompanhamento de editais é a que fecha o ciclo de uso do produto: o usuário acha o edital no feed e segue o andamento até o resultado. |
| **Impacto** | Entram no escopo da Semana 6 a tela do quadro "Acompanhados" (Front-End) e as rotas `POST`, `GET`, `PATCH` e `DELETE` de `/v1/acompanhamentos` (Back-End), com testes de autorização. O CORS do servidor precisa liberar `PATCH` e `DELETE`. |
| **Risco aceito** | Se o quadro não estiver pronto para a demo de 15/11, a Jornada 1 (triagem) e a Jornada 3 (certidões) continuam sendo mostradas, mas o time não cumpre o critério de jornada nova do Gate 3. |
| **Refletido em** | `MoSCoW.md`, `historias.md` (US20), `jornadas.md` (Jornada 2) e `escopo.md`. |

---

## Revisão 2 — Critérios de aceite da US20 com números

**Data:** 09/11/2026 · **Sprint:** Semana 6 · **Responsável:** PO

| | |
| :--- | :--- |
| **Antes** | Critérios descritivos, sem limite de tempo de resposta e sem regra clara de destaque de prazo. |
| **Depois** | Destaque "Prazo próximo" para editais com 0 a 3 dias corridos até o prazo (fuso de Brasília); "Prazo encerrado" no fim da lista para editais em Interessado ou Preparando; p95 de no máximo 800 ms na leitura e 1,5 s na escrita com o *golden set* carregado; transição de uma etapa por vez, com `422` para transição inválida; Resultado exige "Venceu" ou "Não venceu" e não muda mais; `401`, `403` e `404` definidos. |
| **Motivo** | O Checkpoint 3 pede critério de aceite numérico para a jornada nova, e o QA precisa escrever o teste a partir da frase. |
| **Refletido em** | `historias.md` (US20). |

---

## Como registrar uma nova revisão

Copie o bloco de uma revisão acima e preencha: data, sprint, responsável, antes, depois, motivo, impacto e onde a mudança aparece. Se a prioridade mudar, atualize também a tabela da [Matriz MoSCoW](MoSCoW.md) e o seu histórico de alterações.
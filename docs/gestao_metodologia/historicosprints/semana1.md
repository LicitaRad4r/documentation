# Semana 1 — Estrutura Inicial

**Período:** 04/10/2026 – 10/10/2026

---

## Objetivo da Sprint

Formar a equipe, escolher o desafio do ciclo B2B (04/10 a 05/12/2026) e abrir a base do repositório de código, deixando o caminho livre para o Checkpoint 1 (Fundação), marcado para 18/10.

---

## Entregas Realizadas

### Projeto e Equipe

- [x] Projeto escolhido no Guia de Escolha: **LicitaRadar** (GovTech), triagem de editais do PNCP para PMEs **[confirmar data e quem votou]**
- [x] Papéis definidos: 1 PO, 1 Tech Lead, Back-End, Front-End, QA e DevOps **[confirmar data]**
- [ ] Ata ou registro da reunião de kickoff (não há registro no repositório)

### Repositório de código

- [x] Criação do repositório `LicitaRad4r/LicitaRadar` na organização do ciclo
- [x] Estrutura inicial com `client/` (front-end) e `server/` (back-end), 28 arquivos e cerca de 4 mil linhas de partida

---

## Resumo Técnico

| Métrica | Valor |
|---------|-------|
| Commits no repositório de código | 1 |
| Commits no repositório de documentação | 0 (repo ainda não existia) |
| PRs mesclados | 0 |
| Repositórios ativos | 1 |
| Dias restantes até o Checkpoint 1 | 8 |

---

## Contribuições da Equipe

### Tech Lead (Júlia Neiva)

- **Foco:** estrutura inicial do monorepo.
- **Commits:** `aa612ce`.

### Demais papéis (PO, Back-End, Front-End, QA, DevOps)

- Sem commits nesta semana. Atividades de alinhamento e leitura do Caderno B2B **[confirmar]**.

---

## Aprendizados e Decisões

### Decisão: monorepo de código e repositório separado para documentação
O código ficou em um repositório e a documentação (MkDocs) em outro, o que ficou claro na Semana 2, quando a pasta de documentação foi removida da `main` do repositório de código.

### Risco identificado: semana com pouco registro
Quase todo o trabalho de fundação do Checkpoint 1 aconteceu entre 11/10 e 18/10. A equipe teve uma semana para preparar o que o Caderno prevê para duas. Para os próximos checkpoints, a reunião semanal de 20 a 30 minutos precisa gerar registro.

---

## Débitos Técnicos para a Próxima Sprint

| Item | Descrição | Impacto |
|------|-----------|---------|
| Ambiente | Docker Compose com API, front e emuladores; CI com lint e build | Crítico para o Gate 1 |
| Documentação | Criar o repositório de documentação e publicar no GitHub Pages | Crítico para o Gate 1 |
| Produto | Visão, Ishikawa, MoSCoW, critérios mensuráveis, entrevistas | Crítico para o Gate 1 |
| Arquitetura | ADRs, OpenAPI v0.1, Definition of Done, estratégia de testes | Crítico para o Gate 1 |

---

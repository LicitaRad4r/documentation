# Semana 3 — Detalhamento do Checkpoint 2

**Período:** 19/10/2026 – 25/10/2026

Reconstruída a partir dos commits e PRs dos repositórios [LicitaRadar](https://github.com/LicitaRad4r/LicitaRadar) e [documentation](https://github.com/LicitaRad4r/documentation). 

---

## Objetivo da Sprint

Preparar o **Checkpoint 2 — Núcleo funcional** (01/11): detalhar histórias, critérios de aceite e jornadas, atualizar ADRs e DoD, e montar o plano de testes (EP/BVA v1 e critérios prioritários).

---

## Entregas Realizadas

### Produto e Requisitos

- [x] Critérios de aceite das histórias **US05** e **US10** para o CP2 (20/10)
- [x] Detalhamento das **jornadas de utilizador** para o CP2 (22 e 23/10)
- [x] Reestruturação geral das pastas e da navegação do MkDocs (21/10)

### Arquitetura e Processo

- [x] ADRs atualizados (19/10)
- [x] Definition of Done atualizada (19/10)
- [x] **Definition of Ready** adicionada junto ao DoD (23/10)
- [x] Integração do PR #4 (`feat/api-base`) no repositório de código, com o contrato OpenAPI v0.1 e o README do server (21/10)

### Qualidade e Testes

- [x] **EP/BVA v1** (partição de equivalência e valores-limite) para o CP2 (24/10)
- [x] **Critérios de priorização** dos testes (24/10)
- [x] Correção da Jornada 2 e da formatação do EP/BVA

### Código

- [ ] Implementação da API (feed, score, perfil): sem commits de código nesta semana
- [ ] Integração do front com a API

Nenhum commit de código chegou à `main` entre 19 e 25/10.

---

## Resumo Técnico

| Métrica | Valor |
|---------|-------|
| Commits no repositório de código | 0 |
| Commits no repositório de documentação | 12 |
| PRs mesclados | 6 (4 na documentação, 2 no código) |
| Documentos novos | EP/BVA v1, critérios prioritários, DoR, jornadas detalhadas |
| Jornadas detalhadas | Jornadas de utilizador do CP2, com critérios de aceite (numeração da página Jornadas) |

---

## Contribuições da Equipe

### PO (Maria Júlia Sena)

- **Foco:** critérios de aceite do CP2 e jornadas de utilizador.
- **Commits:** `77d29fe`, `5adb70b`, `0eeb12e`, `d87728c`.

### Tech Lead (Júlia Neiva)

- **Foco:** ADRs, DoD, DoR e revisão e merge dos PRs.
- **Commits:** `4786a68`, `f573b1c`, `05daaee`.

### QA (Kauan Tarchetti)

- **Foco:** EP/BVA v1 e critérios prioritários.
- **Commits:** `9d554a4`, `cecf2e7`, `8c677fc`, `17d99a0`.

---

## Aprendizados e Decisões

!!! info "Decisão: critérios de aceite antes do código"
    US05 e US10 ganharam critérios de aceite mensuráveis antes da implementação, para sustentar a matriz de rastreabilidade (requisito → aceite → teste → resultado) exigida no Gate 2.

!!! warning "Risco: documentação à frente do código"
    A semana foi toda de documentação. O código do núcleo (feed, score, filtros, certidões e segurança) só chegou ao repositório a partir de 31/10, a um dia do Gate 2. É o principal motivo do backlog em atraso registrado depois.

---

## Débitos Técnicos para a Próxima Sprint

| Item | Descrição | Impacto |
|------|-----------|---------|
| API | Jornadas 1 a 4 (feed, filtros, certidões, segurança) | Crítico |
| Front | Integração com a API e estados de carregamento, vazio, erro e sucesso | Crítico |
| Segurança | Regras do Firestore deny-all e testes de acesso | Alto |
| Testes | Testes de contrato e matriz de rastreabilidade | Alto |
| FinOps | Definir a evidência de custo (teto de US$ 5 no Gate 2) | Médio |

---
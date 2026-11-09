# Semana 2 — Fundação (Checkpoint 1)

**Período:** 11/10/2026 – 18/10/2026 (Gate 1 em 18/10)

Reconstruída a partir dos commits e PRs dos repositórios [LicitaRadar](https://github.com/LicitaRad4r/LicitaRadar) e [documentation](https://github.com/LicitaRad4r/documentation).

---

## Objetivo da Sprint

Fechar o **Checkpoint 1 — Fundação**: validar problema, escopo, arquitetura e base técnica, com ambiente reproduzível e documentação publicada.

---

## Entregas Realizadas

### Produto e Negócio

- [x] Primeira versão da Visão do Produto (11/10)
- [x] Visão, regras de filtros, regra do score e **diagrama de Ishikawa** (15/10)
- [x] Visão geral e diagrama de arquitetura (17/10)
- [x] **Matriz MoSCoW** (17/10)
- [x] Seções de **LGPD** e **defensibilidade** na Visão do Produto (17/10)
- [x] Páginas de requisitos no menu de navegação
- [ ] 2 entrevistas ou evidências secundárias rotuladas (sem registro no repositório)

### Arquitetura

- [x] **OpenAPI v0.1** do MVP, no repositório de documentação e no de código (18/10)
- [x] Documento de **ADRs v0.1** com 11 decisões (18/10)
- [x] **Definition of Done** (18/10)
- [x] README do `server/` com instruções (18/10)
- [x] Estratégia de testes (14/10)

### Ambiente e DevOps

- [x] **Docker Compose** com API, frontend e emuladores de Auth e Firestore (13/10)
- [x] **CI** com lint e build a cada Pull Request (13/10)
- [x] **CODEOWNERS** e template de Pull Request (13/10)
- [x] README com instruções para rodar localmente (13/10)
- [x] `.gitignore` ignorando arquivos `.env`
- [ ] Seed reproduzível (o seed do golden set só entrou em 31/10)

### Frontend

- [x] Projeto cliente configurado com React, Tailwind v4, shadcn, testes e estrutura de pastas (13/10)
- [x] Correção do build com `@tailwindcss/vite`

### Segurança e Custos

- [x] Testes iniciais de isolamento de tenant (15/10)
- [x] Scripts de teste configurados no servidor (16/10)
- [ ] Alertas de orçamento 50/80/100% (não configurados; a decisão posterior foi o plano de custo zero, na Semana 4)

### Documentação

- [x] Repositório de documentação criado e publicado no GitHub Pages (14/10)
- [x] Pasta de documentação removida da `main` do repositório de código (13/10)
- [x] Reorganização da estrutura de pastas e do `mkdocs.yml`

---

## Resumo Técnico

| Métrica | Valor |
|---------|-------|
| Commits no repositório de código | 18 |
| Commits no repositório de documentação | 20 |
| PRs mesclados | 5 |
| ADRs publicados | 11 (v0.1, sem campo de Status) |
| Contrato OpenAPI | v0.1 |
| Páginas de documentação novas | Visão, impacto, viabilidade, personas, escopo, MoSCoW, arquitetura, estratégia de testes, DoD |

---

## Contribuições da Equipe

### PO (Maria Júlia Sena)

- **Foco:** Visão, Ishikawa, MoSCoW, LGPD e defensibilidade, estrutura do site.
- **Commits:** `6bab97f`, `a12f5ec`, `99b4336`, `f2d8008`, `aadb270`.

### Tech Lead (Júlia Neiva)

- **Foco:** repositório de documentação, ADRs, DoD, OpenAPI v0.1 e revisão dos PRs.
- **Commits:** `ad8c1a4`, `3b5f486`, `37308a1`, `25ef501`, `69d75ea`.

### Back-End (Luís Felipe Albuquerque)

- **Foco:** contrato OpenAPI v0.1 e README do servidor.
- **Commits:** `19aa9d3`, `7941005`.

### Front-End (João Nunes)

- **Foco:** setup do cliente (React, Tailwind, shadcn, testes).
- **Commits:** `ae46044`.

### QA (Kauan Tarchetti)

- **Foco:** estratégia de testes, testes de isolamento de tenant e scripts de teste.
- **Commits:** `0fcbd59`, `1dd01d1`, `3302eca`.

### DevOps (Giovanni Ferreira)

- **Foco:** Docker Compose, CI, CODEOWNERS, README e correções de build.
- **Commits:** `2749358`, `975927c`, `d8f5c7b`, `2f29d4e`, `70320e5`.

---

## Aprendizados e Decisões

!!! info "Decisão: documentação em repositório próprio"
    A documentação (MkDocs Material, publicada no GitHub Pages) saiu do repositório de código em 13/10 e passou a viver em `LicitaRad4r/documentation`, com ADRs e FinOps apontando para lá.

!!! info "Decisão: stack em Node.js, TypeScript, Fastify e Zod, com SPA React"
    Registrada nos ADR-0002 e ADR-0003 da v0.1. A v0.1 previa GCP, Cloud Run, PostgreSQL e Firebase; parte dessa escolha mudaria na Semana 4 (ver custo zero).

!!! warning "Pendências do Gate 1"
    Ficaram sem evidência no repositório: as 2 entrevistas (ou evidências secundárias rotuladas), os alertas de orçamento 50/80/100% e o seed reproduzível.

---

## Débitos Técnicos para a Próxima Sprint

| Item | Descrição | Impacto |
|------|-----------|---------|
| Entrevistas | Registrar 2 entrevistas ou evidências secundárias rotuladas | Alto (item do Gate 1) |
| Backlog | Refinar histórias com critérios de aceite e detalhar as jornadas | Alto |
| Testes | EP/BVA v1 e critérios prioritários | Alto |
| API | Implementar feed, score e perfil conforme o OpenAPI | Crítico |
| Custos | Definir como comprovar custo (alertas ou plano gratuito) | Médio |

---

# Cronograma e Planeamento das Sprints

Este documento detalha o roteiro prático para a condução do ciclo B2B do LicitaRadar (de 04/10 a 05/12/2026), desde o primeiro *commit* até ao Demo Day. O planeamento está dividido em 5 *Checkpoints* oficiais (Gates), organizados em 9 *Sprints* semanais[cite: 4, 7].


---

## Mapa do Ciclo e Checkpoints

### Checkpoint 1: Fundação (Sprints 1 e 2)
**Prazo:** Fim da Semana 2 (18/10/2026) | **Objetivo:** Validar problema, escopo, arquitetura e base técnica[cite: 4].

| Área | O que precisa de estar pronto |
| :--- | :--- |
| **Produto e Negócio** | Visão do produto, Ishikawa, backlog MoSCoW, critérios mensuráveis, 2 entrevistas, métrica da demo e hipótese de defensibilidade[cite: 5]. |
| **Arquitetura** | 4 ADRs, OpenAPI v0.1, repositório organizado, DoD, estratégia de testes e *stack* principal justificada[cite: 5]. |
| **Ambiente** | Docker Compose com API, frontend e emuladores (Auth/Firestore), CI com lint e build, seed reprodutível[cite: 5]. |
| **Segurança e Custos** | `.env.example` sem segredos, alertas de orçamento configurados (50/80/100%) e teste inicial de isolamento de *tenant*[cite: 5]. |
| **Demonstração (Gate)** | Explicar quem tem o problema, qual é o MVP, como será construído e validado, e qual a estimativa de custos[cite: 5]. |

---

### Checkpoint 2: Núcleo Funcional (Sprints 3 e 4)
**Prazo:** Fim da Semana 4 (01/11/2026) | **Objetivo:** Validar o núcleo do produto a funcionar localmente[cite: 4].

| Área | O que precisa de estar pronto |
| :--- | :--- |
| **Funcionalidade** | 3 a 5 jornadas ponta a ponta (Feed, Filtros, Certidões); API integrada ao frontend; estados (carregamento, vazio, erro, sucesso); OpenAPI atualizado[cite: 5]. |
| **Segurança** | Security Rules *deny-all*; testes sem token, token inválido/expirado, papel incorreto, tenant incorreto e acesso indevido[cite: 5]. |
| **Qualidade** | EP/BVA v1; testes de contrato; testes dos critérios prioritários; matriz (requisito → aceite → teste → resultado); relatório em `/docs`[cite: 5]. |
| **FinOps** | Gasto acumulado ≤ US$ 5; listas paginadas; *queries* controladas; sem *listeners* grandes não justificados[cite: 5]. |
| **Demonstração (Gate)** | Executar a jornada principal localmente sem configurações manuais ocultas ou dependências instáveis[cite: 5]. |

---

### Checkpoint 3: Nuvem e Integração (Sprints 5 e 6)
**Prazo:** Fim da Semana 6 (15/11/2026) | **Objetivo:** Validar que o sistema funciona publicado e observável[cite: 4].

| Área | O que precisa de estar pronto |
| :--- | :--- |
| **Deploy** | Firebase Hosting; Cloud Run; *preview* em PR; produção após *merge*; *rewrite* `/api` a funcionar[cite: 6]. |
| **Operação** | `/healthz`, `/readyz`, logs JSON (request id, rota, status, latência), alertas (5xx, p95 e falha de *job*)[cite: 6]. |
| **Infra Segura** | Workload Identity Federation; sem chaves JSON permanentes; segredos no Secret Manager; retenção de 5 imagens (com SHA); sem `latest` em produção[cite: 6]. |
| **FinOps** | Gasto acumulado ≤ US$ 12; revisão de leituras/escritas; min-instances = 0; limites de job e TTL (se aplicável)[cite: 6]. |
| **Demonstração (Gate)** | Utilizador externo acede, autentica-se, executa a jornada e recebe a resposta correta sem depender de ambiente local[cite: 6]. |

---

### Checkpoint 4: Qualidade e Estabilização (Sprints 7 e 8)
**Prazo:** Fim da Semana 8 (29/11/2026) | **Objetivo:** Parar de expandir o escopo e tornar o MVP confiável[cite: 4].

| Área | O que precisa de estar pronto |
| :--- | :--- |
| **Pipeline** | Trilhas paralelas; lint bloqueia build → build bloqueia testes → falha bloqueia deploy; varredura de segredos, dependências e imagens[cite: 6]. |
| **Testes** | Unitários, contrato, integração, autorização, falhas/timeouts, carga local, *smoke* E2E pós-deploy; JUnit e cobertura em `/docs/testes`[cite: 6]. |
| **Metas Globais** | Domínio ≥ 80%, Global ≥ 60%; zero vulnerabilidades críticas/altas; autorização em 100% das rotas; Lighthouse ≥ 90; p95 (leitura ≤ 800ms, escrita ≤ 1.5s)[cite: 6]. |
| **Operação e Custo** | *Rollback* ensaiado; exportação manual do Firestore; aos US$ 40: reduzir instâncias e desligar recursos não críticos[cite: 6]. |
| **Demonstração (Gate)** | Nenhum *bug* crítico aberto; pipeline bloqueante funcional; *rollback* comprovado; dados de demonstração preparados[cite: 6]. |

---

### Checkpoint Final: Demo Day (Sprint 9)
**Prazo:** Semana 9 (05/12/2026) | **Objetivo:** Homologar, demonstrar e apresentar o produto[cite: 4].

| Área | O que precisa de estar pronto |
| :--- | :--- |
| **Homologação** | Fluxo principal E2E fluido; `/readyz` saudável; produção acessível sem dependência local; plano de contingência pronto[cite: 7]. |
| **Roteiro** | Problema, hipótese de valor, jornada principal, critérios de sucesso, segurança, qualidade, custo e métrica de defensibilidade[cite: 7]. |
| **Demo LicitaRadar** | Demonstração clara focada em: configuração de **perfis**, exploração do **feed** ranqueado, acompanhamento de **edital** e **snapshot** de alertas/certidões[cite: 7]. |
| **Evidências** | Links para Produção, CI, relatórios de testes/cobertura, Lighthouse, custos e *rollback* documentados[cite: 7]. |

---

##  Controlo Semanal da Equipa

Para além dos *gates* quinzenais, a equipa fará uma reunião curta (20 a 30 minutos) semanalmente para responder às seguintes questões de alinhamento[cite: 7]:

| Pergunta | Evidência Esperada / Ação |
| :--- | :--- |
| **1. O que foi concluído?** | PR, *issue* fechada ou demonstração funcional. |
| **2. O que está bloqueado?** | Registo do bloqueio, impacto no cronograma e responsável designado. |
| **3. Qual critério foi coberto?** | Caso de teste relacionado com o requisito em causa. |
| **4. Qual risco aumentou?** | Atualização no *backlog*, ADR ou ativação do plano de mitigação. |
| **5. Quanto já gastámos?** | Relatório de orçamento (GCP) e eventual ação de contenção necessária. |
| **6. O que será feito na próxima semana?** | Histórias de utilizador repriorizadas e responsáveis alocados. |
- **Critérios prioritários:** executar, na ordem de risco, os casos de teste e registrar o resultado com prova.

- ## 2. Priorização

A prioridade vem da seção 2 da estratégia de testes (riscos R1 a R10).

| Ordem | Frente | Casos | Risco | Prioridade | Depende de |
| --- | --- | --- | --- | --- | --- |
| 1 | Segurança e isolamento entre empresas | CT-SEG-001 a 011 | R7 | Alta | gancho de autenticação |
| 2 | Certidões (classificação e rotina das 00h00) | CT-CERT-001, 004 a 007, 010 | R3 | Alta | `GET /certidoes` e renovação |
| 3 | Feed: ordenação, sem PNCP | CT-FEED-001 a 004, 009 | R1, R5 | Alta | `GET /feed` e seed |
| 4 | Filtros: só restringem, não recalculam | CT-FILT-001, 004 a 007, 009 | R1, R6 | Alta e Média | `GET /feed` com parâmetros |
| 5 | Contrato de resposta | todas as rotas | R8 | Média | rotas implementadas |
| 6 | E2E (Playwright) | CT-FEED-005 a 008, CT-FILT-002/003/008, CT-CERT-003/008/009 | R9 | Média | front |

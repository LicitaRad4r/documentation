# Critérios prioritários
 
Ordem para executar os casos de teste (CT) conforme o risco, registrando o resultado com prova (print, log ou relatório do CI).
 
A prioridade vem da seção 2 da [estratégia de testes](estrategia_de_testes.md) (riscos R1 a R10). Os casos estão na [matriz de rastreabilidade](matriz-rastreabilidade.md).
 
| Ordem | Frente | Casos | Risco | Prioridade | Depende de | Situação (19/11/2026) |
| :---: | --- | --- | --- | --- | --- | --- |
| 1 | Segurança e isolamento entre empresas | CT-SEG-001 a 011 | R7 | Alta | gancho de autenticação | ✅ Executada (API e Render); falta cobrir os parciais da matriz |
| 2 | Certidões (classificação e rotina das 00h00) | CT-CERT-001, 004 a 007, 010 | R3 | Alta | `GET /certidoes` e renovação | ✅ Executada |
| 3 | Feed: ordenação, sem PNCP | CT-FEED-001 a 004, 009 | R1, R5 | Alta | `GET /feed` e seed | ✅ Executada |
| 4 | Filtros: só restringem, não recalculam | CT-FILT-001, 004 a 007, 009 | R1, R6 | Alta e Média | `GET /feed` com parâmetros | ✅ Executada, com CT-FILT-009 parcial (p95 medido em memória) |
| 5 | Contrato de resposta | todas as rotas | R8 | Média | rotas implementadas | ✅ Contrato v0.3 testado; falta `/v1/editais/{editalId}` |
| 6 | E2E (Playwright) | CT-FEED-005 a 008, CT-FILT-002, 003 e 008, CT-CERT-003, 008 e 009 | R9 | Média | front | ⚠️ Em parte: faltam CT-FEED-005, CT-FEED-007 e CT-CERT-008 |

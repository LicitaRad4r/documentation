# Testes do LicitaRadar
 
Índice da pasta `docs/Testes`. Leia na ordem abaixo.
 
| Ordem | Documento | Para que serve |
| :---: | --- | --- |
| 1 | [Estratégia de testes](estrategia_de_testes.md) | Camadas, riscos, ambientes, padrão de caso de teste, CI e gestão de bugs |
| 2 | [Matriz de rastreabilidade](matriz-rastreabilidade.md) | Requisito → critério de aceite → caso de teste (CT) → resultado |
| 3 | [EP/BVA](ep-bva-v1.md) | Classes de equivalência e valores-limite de cada CT |
| 4 | [Critérios prioritários](criterios-prioritarios.md) | Ordem de execução dos CTs, por risco |
| 5 | [Roteiro E2E contra a API real](roteiro-e2e-api-real.md) | Passo a passo dos testes ponta a ponta |
| 6 | [Relatório CP2/CP3](relatoriocp2.md) | Resultados, bugs abertos e como reproduzir |
| 7 | [Testes manuais Postman](testes-manuais.md) | Evidências dos testes, execução contra a API |
 
## Convenções
 
- **ID do caso de teste:** `CT-<MÓDULO>-<NNN>` (ex.: `CT-SEG-005`). Os IDs de E2E (`E2E-01`) e do kanban (`KAN-SEG-01`) são dos scripts do Playwright e se ligam aos CTs na matriz.
- **Resultado:** ✅ Passou · ⚠️ Parcial · ❌ Falhou · ⬜ Pendente.
- **Jornadas:** 1 Feed (`FEED`) · 2 Filtros (`FILT`) · 3 Certidões (`CERT`) · 4 Segurança (`SEG`).
## No repositório de código
 
Documentos de operação que complementam estes testes (em [`LicitaRad4r/LicitaRadar`](https://github.com/LicitaRad4r/LicitaRadar)):
 
- `docs/nuvem.md`: Firebase e Render, variáveis e exemplos de ambiente.
- `docs/operacao/alertas.md`: UptimeRobot em `/readyz`, logs no Render e p95 no k6.
- `docs/operacao/k6-p95.md`: tabela do teste de carga (16/11/2026).
- `.github/workflows/`: CI, Deploy, Smoke test, Hosting preview e Ingestão PNCP.
*Última revisão: 19/11/2026, conferida contra a `main` do repositório de código (`15eeff4`) e a da documentação (`d8a0b39`).*

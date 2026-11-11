# Resultado do teste de carga (k6)

Alvo: http://localhost:3333 · usuário: decisor.a@licitaradar.local · leitura: 5 VUs por cenário · escrita: 2 VUs · rodada curta

| Rota | Tipo | Mediana | p95 | Máximo | Meta p95 | Resultado |
| --- | --- | --- | --- | --- | --- | --- |
| GET /v1/feed (sem filtros) | leitura | 14.6 ms | 25.0 ms | 46.1 ms | ≤ 800 ms | passou |
| GET /v1/feed (com filtros) | leitura | 14.6 ms | 23.7 ms | 48.2 ms | ≤ 800 ms | passou |
| GET /v1/certidoes | leitura | 15.0 ms | 25.0 ms | 45.6 ms | ≤ 800 ms | passou |
| PUT /v1/perfil | escrita | 9.8 ms | 19.4 ms | 28.3 ms | ≤ 1500 ms | passou |

Requisições: 563 · falhas HTTP: 0.00% · checks aprovados: 100.00%

# Teste de carga com k6 (CT-FILT-009)

No Plano Custo Zero, o p95 é medido **só localmente**, com o `docker compose` e o seed. Nunca contra o Firestore de produção: a cota gratuita acabaria e a medição não representaria o produto.

## O que é medido

| Cenário | Rota | Tipo | Meta (p95) |
| --- | --- | --- | --- |
| Feed sem filtros | `GET /v1/feed` | leitura | ≤ 800 ms |
| Feed com filtros | `GET /v1/feed?uf=…&modalidade=…&q=…&valorMin=…&page=…` | leitura | ≤ 800 ms |
| Certidões | `GET /v1/certidoes` | leitura | ≤ 800 ms |
| Perfil | `PUT /v1/perfil` | escrita | ≤ 1,5 s |

- **Dados:** seed oficial (editais A a F, empresas A e B, 7 certidões). Usuário: decisor da empresa A.
- **Escrita:** o `PUT /v1/perfil` regrava o mesmo perfil da empresa A, então o seed continua intacto.
- **Carga:** 5 usuários virtuais por cenário de leitura e 2 na escrita, com subida de 20 s, patamar de 60 s e descida de 10 s. Pensada para uma máquina de desenvolvimento, não para estressar.
- **Critérios de aprovação** (o k6 sai com erro se algum falhar): p95 de leitura < 800 ms, p95 de escrita < 1,5 s, menos de 1% de falhas HTTP e mais de 99% dos checks aprovados.
- **Trava de segurança:** o script se recusa a rodar se `API_URL` ou `AUTH_URL` não forem da máquina local (`localhost`, `127.0.0.1` ou `host.docker.internal`).

# Resultado do teste de carga (k6)

Alvo: http://localhost:3333 · usuário: decisor.a@licitaradar.local · leitura: 5 VUs por cenário · escrita: 2 VUs · rodada curta


## Rodada para testar script

| Rota | Tipo | Mediana | p95 | Máximo | Meta p95 | Resultado |
| --- | --- | --- | --- | --- | --- | --- |
| GET /v1/feed (sem filtros) | leitura | 14.6 ms | 25.0 ms | 46.1 ms | ≤ 800 ms | passou |
| GET /v1/feed (com filtros) | leitura | 14.6 ms | 23.7 ms | 48.2 ms | ≤ 800 ms | passou |
| GET /v1/certidoes | leitura | 15.0 ms | 25.0 ms | 45.6 ms | ≤ 800 ms | passou |
| PUT /v1/perfil | escrita | 9.8 ms | 19.4 ms | 28.3 ms | ≤ 1500 ms | passou |

Requisições: 563 · falhas HTTP: 0.00% · checks aprovados: 100.00%

## Rodada Completa

| Rota | Tipo | Mediana | p95 | Máximo | Meta p95 | Resultado |
| --- | --- | --- | --- | --- | --- | --- |
| GET /v1/feed (sem filtros) | leitura | 10,4 ms | 17,8 ms | 46,3 ms | ≤ 800 ms | passou |
| GET /v1/feed (com filtros) | leitura | 10,2 ms | 18,4 ms | 41,7 ms | ≤ 800 ms | passou |
| GET /v1/certidoes | leitura | 10,3 ms | 17,5 ms | 44,6 ms | ≤ 800 ms | passou |
| PUT /v1/perfil | escrita | 8,1 ms | 12,2 ms | 20,8 ms | ≤ 1500 ms | passou |

3.716 requisições, 0,00% de falhas HTTP e 100% dos checks aprovados. Antes da rodada oficial, uma rodada curta (`CURTO=1`, 15 s) serviu só para conferir o script e não entra como evidência.

Leitura do resultado: o p95 de leitura ficou em 18,4 ms no pior cenário, bem abaixo dos 800 ms, e o de escrita em 12,2 ms, abaixo dos 1,5 s. A carga é leve de propósito (máquina de desenvolvimento, emulador local); o resultado mostra que o código não tem gargalo com o seed, e não a capacidade do Render.


## Como rodar

```bash
cp .env.example .env                       # se ainda não existir
docker compose up --build -d
docker compose exec api npm run seed
k6 run k6/carga.js                         # rodada completa (~1,5 min)
CURTO=1 k6 run k6/carga.js                 # rodada de 15 s para conferir o script
```

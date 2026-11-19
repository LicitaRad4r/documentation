# Validação manual da API (Postman): resultados e evidências
 
**Projeto:** LicitaRadar · **Coleção:** `licitaradar-api.postman_collection.json` · **Casos:** 65 (pastas 00 a 07)
 
| Campo | Preencher |
|---|---|
| Data da execução | 19/11/2026|
| Executado por |Kauan Tarchetti |
| Ambiente | (X) Render `https://licitaradar-y2gp.onrender.com`  ( ) Local (emulador) |


## 00 Infra (rotas públicas, sem token)
 
### API-01 · GET `/healthz`
 
- **Quem chama:** sem token
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; status ok; não traz dados
- **Observação do caso:** A API está de pé. Pública.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok 


<img width="707" height="281" alt="image" src="https://github.com/user-attachments/assets/6bdb1b26-f5ef-49c5-a70d-583d047833c1" />

 
---
### API-02 · GET `/readyz`
 
- **Quem chama:** sem token
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; pronta
- **Observação do caso:** A API consegue falar com o Firestore. Pública. Em 503, a API está de pé mas sem o banco.
**Validado?**
 
- [X] Sim
- [ ] Não

Status recebido: 200 ok 
 
<img width="712" height="285" alt="image" src="https://github.com/user-attachments/assets/745ea424-4179-41b6-a316-69b776be8982" />

 
---
### API-03 · GET `/health`
 
- **Quem chama:** sem token
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; tem status e timestamp
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok  
 
<img width="705" height="283" alt="image" src="https://github.com/user-attachments/assets/ed6ce631-5768-4296-8ffd-eef363c01c8b" />

---
 
## 01 Preparação (guarda ids usados nas pastas seguintes)
 
### PREP-01 · GET `/v1/feed?pageSize=100` — (decisor A, 100 itens)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; itens é uma lista com editais de teste
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok  
 
<img width="705" height="524" alt="image" src="https://github.com/user-attachments/assets/de94dd0e-5cdd-4c40-b4d3-ca7c951072ab" />

 
---
### PREP-02 · GET `/v1/acompanhamentos?pageSize=100` — (decisor A) e escolhe um edital livre
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; há um edital do feed ainda não acompanhado (o seed não é tocado)
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok 
 
<img width="713" height="519" alt="image" src="https://github.com/user-attachments/assets/5947d0d2-c9d5-42c8-b734-6130c0a9c9eb" />

 
---
### PREP-03 · GET `/v1/certidoes` — (decisor A)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; a empresa A tem certidões
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok  

<img width="712" height="519" alt="image" src="https://github.com/user-attachments/assets/db791806-6c83-4dbc-bf65-dd9b8f2eafce" />

 
---
### PREP-04 · GET `/v1/certidoes` — (decisor B)
 
- **Quem chama:** decisor B (`decisor.b@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; a empresa B tem certidões
**Validado?**
 
- [X] Sim
- [ ] Não

Status recebido: 200 ok 
 
<img width="711" height="500" alt="image" src="https://github.com/user-attachments/assets/0d64978c-165c-424d-a5c4-d880c4f30ef1" />

 
---
### PREP-05 · GET `/v1/perfil` — (decisor A)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200
**Validado?**
 
- [X] Sim
- [ ] Não

Status recebido: 200 ok  

<img width="706" height="549" alt="image" src="https://github.com/user-attachments/assets/ec57ca76-0667-45b4-ba3b-97610719f198" />

 
---
 
## 02 Autenticação e autorização (CT-SEG)
 
### SEG-01 · GET `/v1/feed` — sem token
 
- **Quem chama:** sem token
- **Status esperado:** 401 Unauthorized · código de erro `NAO_AUTENTICADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 401; codigo NAO_AUTENTICADO
- **Observação do caso:** CT-SEG-001
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized  
 
<img width="705" height="383" alt="image" src="https://github.com/user-attachments/assets/0b6abacf-5767-49ad-a269-a0d28b9ab6e4" />


 
---
### SEG-02 · GET `/v1/feed` — com token inválido
 
- **Quem chama:** token inválido (texto qualquer)
- **Status esperado:** 401 Unauthorized · código de erro `NAO_AUTENTICADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 401; codigo NAO_AUTENTICADO
- **Observação do caso:** CT-SEG-002
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized  
 
<img width="716" height="390" alt="image" src="https://github.com/user-attachments/assets/50008efb-7fea-492f-9feb-fa4b1ec51e6d" />

 
---
### SEG-03 · GET `/v1/feed` — com token expirado (simulado)
 
- **Quem chama:** token expirado (simulado pelo script)
- **Status esperado:** 401 Unauthorized · código de erro `NAO_AUTENTICADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 401; codigo NAO_AUTENTICADO
- **Observação do caso:** CT-SEG-003. O token é um JWT com exp no passado e assinatura falsa.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized 
 
<img width="709" height="384" alt="image" src="https://github.com/user-attachments/assets/f5da756c-a2b2-482d-9bcc-a1022349c678" />

 
---
### SEG-04 · GET `/v1/nao-existe` — Rota inexistente sem token
 
- **Quem chama:** sem token
- **Status esperado:** 401 Unauthorized
- **Em *Test Results* deve aparecer (tudo verde):** status 401
- **Observação do caso:** O hook de segurança roda antes do roteamento: nem rota inexistente responde sem login.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized

<img width="706" height="383" alt="image" src="https://github.com/user-attachments/assets/7bf01a49-4332-4002-9df6-bc7b2e629a1f" />

 
---
### SEG-04b · GET `/v1/nao-existe` — Rota inexistente com login
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 404 Not Found · código de erro `NAO_ENCONTRADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 404; codigo NAO_ENCONTRADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 404 Not Found
 
<img width="711" height="372" alt="image" src="https://github.com/user-attachments/assets/29c983e5-0c71-4f72-8cfe-765b00a18af3" />

 
---
### SEG-05 · GET `/v1/certidoes` — com analista
 
- **Quem chama:** analista A (`analista.a@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
- **Observação do caso:** CT-SEG-005
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden
 
<img width="710" height="389" alt="image" src="https://github.com/user-attachments/assets/28f9bb7d-5a4e-4892-bdd2-367b6c0c1efb" />

 
---
### SEG-06 · GET `/v1/certidoes` — com decisor
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200
- **Observação do caso:** CT-SEG-005 (contraprova)
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="710" height="517" alt="image" src="https://github.com/user-attachments/assets/c6f87582-fac5-4bbd-a06b-90dcba6cd46f" />

 
---
### SEG-07 · GET `/v1/certidoes/{{certidaoIdB}}` — GET certidão da empresa B com decisor A
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO; não revela dados da certidão
- **Observação do caso:** CT-SEG-007
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden
 
<img width="712" height="399" alt="image" src="https://github.com/user-attachments/assets/06aa625a-6675-4c02-aaf0-c2b5e43f2f25" />

 
---
### SEG-08 · PUT `/v1/certidoes/{{certidaoIdB}}/renovacao` — PUT renovação da certidão da B com decisor A
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
- **Observação do caso:** CT-SEG-008. O dono é checado antes de ler o corpo; nada é gravado.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden
 
<img width="703" height="385" alt="image" src="https://github.com/user-attachments/assets/7cd4b63c-deea-4cf9-9663-776b9f8080b7" />

 
---
### SEG-09 · PUT `/v1/certidoes/{{certidaoIdA}}/renovacao` — PUT renovação com analista
 
- **Quem chama:** analista A (`analista.a@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
- **Observação do caso:** CT-SEG-006
**Validado?**
 
- [X] Sim
- [ ] Não
Status recebido: 403 Forbidden
 
<img width="704" height="377" alt="image" src="https://github.com/user-attachments/assets/55da1d21-ea0d-4c95-8302-07f688e03f93" />

 
---
### SEG-10 · GET `/v1/perfil?orgId=emp-b` — com orgId da empresa B
 
- **Quem chama:** analista A (`analista.a@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
- **Observação do caso:** CT-SEG-009
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden
 
<img width="709" height="410" alt="image" src="https://github.com/user-attachments/assets/92a4f8fb-ffbc-4f34-8ab5-79db351ab1e6" />

 
---
### SEG-11 · GET `/v1/feed?empresaId=emp-b` — com empresaId forjado
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
- **Observação do caso:** A empresa vem só do token; empresaId/orgId de outra empresa na query ou no corpo é 403.
**Validado?**
 
- [X] Sim
- [ ] Não
 
Status recebido: 403 Forbidden
 
<img width="712" height="410" alt="image" src="https://github.com/user-attachments/assets/760ad27e-38c5-4481-b3b9-2ee7eb03b3a4" />

 
---
### SEG-12 · GET `/v1/certidoes` — Corpo da resposta de bloqueio não traz dados nem pessoais
 
- **Quem chama:** analista A (`analista.a@`)
- **Status esperado:** 403 Forbidden
- **Em *Test Results* deve aparecer (tudo verde):** status 403; só campos do erro; não traz e-mail nem id de empresa
- **Observação do caso:** CT-SEG-011
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden 
 
<img width="717" height="392" alt="image" src="https://github.com/user-attachments/assets/cc90cc35-57c3-4d98-837f-61f215a0cf38" />

 
---
 
## 03 Perfil
 
### PERF-01 · GET `/v1/perfil`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; tem os campos do perfil
**Validado?**
 
- [X] Sim
- [ ] Não

Status recebido: 200 ok
 
<img width="710" height="528" alt="image" src="https://github.com/user-attachments/assets/696cdb4a-7ac9-49eb-826d-7404af90af23" />

 
---
### PERF-02 · PUT `/v1/perfil` — com os mesmos dados (não altera o seed)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; devolve o perfil salvo
- **Observação do caso:** Reenvia o perfil lido em PREP-05: valida o PUT sem mudar o dado.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok

<img width="707" height="518" alt="image" src="https://github.com/user-attachments/assets/2a6ae349-bc38-4dad-864a-b0f5e0c39f28" />


---
### PERF-03 · PUT `/v1/perfil` — com valor mínimo maior que o máximo
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO; aponta o campo valorMaximo
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="715" height="485" alt="image" src="https://github.com/user-attachments/assets/bfa2812d-6b6b-485e-9f3c-c279e23874a9" />

 
---
### PERF-04 · PUT `/v1/perfil` — com UF inválida
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="714" height="477" alt="image" src="https://github.com/user-attachments/assets/a4fab793-1e08-408d-80d2-a53aa77952eb" />


---
### PERF-05 · PUT `/v1/perfil` — sem token
 
- **Quem chama:** sem token
- **Status esperado:** 401 Unauthorized · código de erro `NAO_AUTENTICADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 401; codigo NAO_AUTENTICADO
- **Observação do caso:** Sem token é sempre 401, mesmo com corpo inválido.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized

<img width="717" height="364" alt="image" src="https://github.com/user-attachments/assets/602a78fb-cb66-45f9-9c2b-71feb242cf44" />

---
 
## 04 Feed e filtros (CT-FEED, CT-FILT)
 
### FEED-01 · GET `/v1/feed` — (padrão)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; tem a paginação; ordem de score não crescente; score entre 0 e 100
- **Observação do caso:** CT-FEED-001
**Validado?**
 
- [X] Sim
- [ ] Não

Status recebido: 200 ok
 
<img width="712" height="522" alt="image" src="https://github.com/user-attachments/assets/323481a7-cf09-463f-9fff-670f1512555b" />

---
### FEED-02 · GET `/v1/feed?pageSize=100`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; até 100 itens
- **Observação do caso:** CT-FEED-009 (limite superior válido)
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="716" height="520" alt="image" src="https://github.com/user-attachments/assets/f2ee1c04-7538-4c33-a281-69984a164c8b" />

 
---
### FEED-03 · GET `/v1/feed?pageSize=101`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
- **Observação do caso:** CT-FEED-009
**Validado?**
 
- [X] Sim
- [ ] Não
Status recebido: 400 Bad Request
 
<img width="717" height="482" alt="image" src="https://github.com/user-attachments/assets/ff9a2614-7658-4b05-ba64-596d1ad8838d" />

---
### FEED-04 · GET `/v1/feed?pageSize=0`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
- **Observação do caso:** CT-FEED-009
**Validado?**
 
- [X] Sim
- [ ] Não

Status recebido: 400 Bad Request
 
<img width="709" height="477" alt="image" src="https://github.com/user-attachments/assets/cb15e031-b225-4492-bded-b6a8addae84f" />


---
### FEED-05 · GET `/v1/feed?page=0`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
- **Observação do caso:** CT-FEED-009
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="720" height="480" alt="image" src="https://github.com/user-attachments/assets/eba398fd-7e54-478e-8498-843f505ef82d" />

 
---
### FEED-06 · GET `/v1/feed?page=2&pageSize=2` — não repete itens da página 1
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; página 2
- **Observação do caso:** CT-FEED-009
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="711" height="515" alt="image" src="https://github.com/user-attachments/assets/6bb30b6e-5308-421e-852c-f9305ba7ff70" />

 
---
### FEED-07 · GET `/v1/feed?valorMin=50000&pageSize=100`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; só valores >= 50000 (edital sem valor não passa)
- **Observação do caso:** CT-FILT-001
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="711" height="519" alt="image" src="https://github.com/user-attachments/assets/422fc085-df76-4e11-a761-39ccb4d977c8" />

 
---
### FEED-08 · GET `/v1/feed?valorMin=1000000000` — (filtro impossível)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; lista vazia
- **Observação do caso:** CT-FILT-002
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="714" height="435" alt="image" src="https://github.com/user-attachments/assets/dc7c29ea-ed21-4401-9411-615ae1c54241" />

 
---
### FEED-09 · GET `/v1/feed?valorMin=100&valorMax=10` — com valorMin maior que valorMax
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO; aponta valorMax
- **Observação do caso:** CT-FILT-007
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="714" height="435" alt="image" src="https://github.com/user-attachments/assets/a221b429-7ce3-45af-b839-d2d384381bf6" />

 
---
### FEED-10 · GET `/v1/feed?valorMin=abc`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
- **Observação do caso:** CT-FILT-007
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="715" height="481" alt="image" src="https://github.com/user-attachments/assets/c8be07e0-e3ed-4034-8faa-d8c2009fda40" />

 
---
### FEED-11 · GET `/v1/feed?prazoDe=31-12-2026` — com data inválida
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
- **Observação do caso:** CT-FILT-007
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="717" height="482" alt="image" src="https://github.com/user-attachments/assets/80dfbdfb-6b3a-498a-bff6-a270aa368fb9" />

 
---
### FEED-12 · GET `/v1/feed?pageSize=100` — O filtro não recalcula o score
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; mesmos editais do PREP-01 (o feed não mudou desde a preparação)
- **Observação do caso:** Reexecuta o feed sem filtro e compara com o da preparação (estabilidade/determinismo).
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="718" height="527" alt="image" src="https://github.com/user-attachments/assets/9702db02-c2f6-447c-8f34-03063f70b5fc" />

 
---
### FEED-13 · GET `/v1/feed?pageSize=100` — (decisor B): cada empresa tem o seu feed
 
- **Quem chama:** decisor B (`decisor.b@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; o feed da B não é igual ao da A (perfis diferentes)
- **Observação do caso:** CT-FEED-003
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="719" height="520" alt="image" src="https://github.com/user-attachments/assets/81885b9e-a524-4d7f-8a5e-138afa7f6dcc" />

 
---
 
## 05 Certidões (só decisor)
 
### CERT-01 · GET `/v1/certidoes`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; mais urgente primeiro; cada certidão tem id, nome e validade; não traz empresaId nem caminho de arquivo
- **Observação do caso:** CT-CERT-002
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="710" height="520" alt="image" src="https://github.com/user-attachments/assets/dafdde1b-6fbb-47ee-9428-5c45aa1c7226" />

 
---
### CERT-02 · GET `/v1/certidoes/{{certidaoIdA}}`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; é a certidão pedida
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="715" height="414" alt="image" src="https://github.com/user-attachments/assets/1b2d9b41-0aa9-4e4e-a044-abd9832e6f8c" />

 
---
### CERT-03 · GET `/v1/certidoes/id-que-nao-existe`
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 404 Not Found · código de erro `NAO_ENCONTRADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 404; codigo NAO_ENCONTRADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 404 Not Found
 
<img width="713" height="378" alt="image" src="https://github.com/user-attachments/assets/edb8d2f2-9e5f-4838-bfeb-37f802261485" />

 
---
### CERT-04 · PUT `/v1/certidoes/{{certidaoIdA}}/renovacao` — PUT renovação sem arquivo
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO; aponta o campo arquivo
- **Observação do caso:** CT-CERT-007. Nada é gravado.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="719" height="498" alt="image" src="https://github.com/user-attachments/assets/e10ccc5f-fc3c-4ef5-abe0-1a09da58da0e" />

 
---
### CERT-05 · PUT `/v1/certidoes/{{certidaoIdA}}/renovacao` — PUT renovação com validade no passado
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO; aponta o campo validade
- **Observação do caso:** CT-CERT-006. Nada é gravado.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="718" height="516" alt="image" src="https://github.com/user-attachments/assets/e46fd15d-1045-4529-afee-5d1c3cca97a4" />

 
---
### CERT-06 · PUT `/v1/certidoes/{{certidaoIdA}}/renovacao` — PUT renovação sem multipart (JSON)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
- **Observação do caso:** A rota só aceita multipart/form-data.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="717" height="503" alt="image" src="https://github.com/user-attachments/assets/9df18a6b-d511-4789-9787-8bba15616b93" />

 
---
 
## 06 Kanban de acompanhamento (US20)
 
### KAN-01 · POST `/v1/acompanhamentos` — sem token
 
- **Quem chama:** sem token
- **Status esperado:** 401 Unauthorized · código de erro `NAO_AUTENTICADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 401; codigo NAO_AUTENTICADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized
 
<img width="716" height="382" alt="image" src="https://github.com/user-attachments/assets/31a39c68-fc27-4d21-aac4-638e47369744" />

 
---
### KAN-02 · POST `/v1/acompanhamentos` — POST com edital inexistente
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 404 Not Found · código de erro `NAO_ENCONTRADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 404; codigo NAO_ENCONTRADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 404 Not Found
 
<img width="711" height="375" alt="image" src="https://github.com/user-attachments/assets/01a35c66-21ef-40bc-9be1-e0d07decd2ce" />

 
---
### KAN-03 · POST `/v1/acompanhamentos` — POST com corpo inválido
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="711" height="481" alt="image" src="https://github.com/user-attachments/assets/889790be-caba-4020-b424-64bbaa967b60" />

 
---
### KAN-04 · POST `/v1/acompanhamentos` — POST cria o acompanhamento (decisor A)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 201 Created
- **Em *Test Results* deve aparecer (tudo verde):** status 201; começa em interessado; não vaza o empresaId
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 201 Created
 
<img width="721" height="523" alt="image" src="https://github.com/user-attachments/assets/0e356741-43c6-4959-a522-afafd4f5cf6c" />

 
---
### KAN-05 · POST `/v1/acompanhamentos` — POST repetido devolve o existente (200)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; mesmo id, sem duplicar
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="713" height="521" alt="image" src="https://github.com/user-attachments/assets/1fa3ccd3-b68f-41d2-9978-e8241215ce14" />

 
---
### KAN-06 · GET `/v1/acompanhamentos?pageSize=100` — GET lista da empresa A contém o registro
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; contém o acompanhamento criado
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="713" height="521" alt="image" src="https://github.com/user-attachments/assets/3bd4dbe5-41c9-45cc-b972-d03c32d99577" />

 
---
### KAN-07 · GET `/v1/acompanhamentos?pageSize=100` — GET lista da empresa B NÃO contém o registro da A
 
- **Quem chama:** decisor B (`decisor.b@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; não vê o registro da A
- **Observação do caso:** Isolamento entre empresas.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="717" height="394" alt="image" src="https://github.com/user-attachments/assets/1dbaede4-ab4a-408a-9017-2ecebca99a8c" />

 
---
### KAN-08 · PATCH `/v1/acompanhamentos/{{acompId}}` — PATCH do registro da A pela empresa B
 
- **Quem chama:** decisor B (`decisor.b@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden
 
<img width="714" height="362" alt="image" src="https://github.com/user-attachments/assets/350a5829-6afa-4a98-8f4d-2fdfea39df14" />

---
### KAN-09 · DELETE `/v1/acompanhamentos/{{acompId}}` — DELETE do registro da A pela empresa B
 
- **Quem chama:** decisor B (`decisor.b@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden
 
<img width="708" height="368" alt="image" src="https://github.com/user-attachments/assets/cd2a6302-3f67-4810-ba01-dd40a21661ea" />

 
---
### KAN-10 · GET `/v1/acompanhamentos?empresaId=emp-b` — GET com empresaId forjado na query
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 403 Forbidden · código de erro `ACESSO_NEGADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 403; codigo ACESSO_NEGADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 403 Forbidden
 
<img width="711" height="375" alt="image" src="https://github.com/user-attachments/assets/079f832b-f49b-4a3a-b4c1-271d05ef4645" />

 
---
### KAN-11 · PATCH `/v1/acompanhamentos/{{acompId}}` — PATCH pulando etapa (interessado → enviado)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 422 Unprocessable Entity · código de erro `REGRA_DE_NEGOCIO`
- **Em *Test Results* deve aparecer (tudo verde):** status 422; codigo REGRA_DE_NEGOCIO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 422 Unprocessable Entity
 
<img width="710" height="397" alt="image" src="https://github.com/user-attachments/assets/8e1735b4-e82b-4f54-8ded-095233d3ef29" />

 
---
### KAN-12 · PATCH `/v1/acompanhamentos/{{acompId}}` — PATCH resultado sem venceu/nao_venceu
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 400 Bad Request · código de erro `VALIDACAO`
- **Em *Test Results* deve aparecer (tudo verde):** status 400; codigo VALIDACAO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 400 Bad Request
 
<img width="716" height="473" alt="image" src="https://github.com/user-attachments/assets/13649b45-e2b7-4008-b96d-8956e0769ff1" />

 
---
### KAN-13 · PATCH `/v1/acompanhamentos/{{acompId}}` — PATCH interessado → preparando
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; etapa preparando
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="716" height="517" alt="image" src="https://github.com/user-attachments/assets/282c7c10-ec21-4637-8ff8-a166dbda26e4" />

---
### KAN-14 · PATCH `/v1/acompanhamentos/{{acompId}}` — PATCH preparando → interessado (a única volta permitida)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 200 OK
- **Em *Test Results* deve aparecer (tudo verde):** status 200; etapa interessado
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 200 ok
 
<img width="715" height="522" alt="image" src="https://github.com/user-attachments/assets/77d517cb-3cbd-404c-8af3-3f4958f9f81b" />

 
---
### KAN-15 · PATCH `/v1/acompanhamentos/id-que-nao-existe` — PATCH com id inexistente
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 404 Not Found · código de erro `NAO_ENCONTRADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 404; codigo NAO_ENCONTRADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 404 Not Found
 
<img width="717" height="377" alt="image" src="https://github.com/user-attachments/assets/5bf168fa-bccb-4141-95b8-d758220bb3b4" />

 
---
### KAN-16 · DELETE `/v1/acompanhamentos/{{acompId}}` — DELETE do registro criado (limpeza)
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 204 No Content
- **Em *Test Results* deve aparecer (tudo verde):** status 204
- **Observação do caso:** Só remove o que a própria coleção criou.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 204 No Content
 
<img width="715" height="345" alt="image" src="https://github.com/user-attachments/assets/fb38edd0-502b-4159-ac9e-004df98d78e1" />

 
---
### KAN-17 · DELETE `/v1/acompanhamentos/{{acompId}}` — DELETE de novo → 404
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 404 Not Found · código de erro `NAO_ENCONTRADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 404; codigo NAO_ENCONTRADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 404 Not Found
 
<img width="711" height="376" alt="image" src="https://github.com/user-attachments/assets/eb8b87e2-763f-4f41-9182-3eded32fbd6a" />

 
---
 
## 07 Job de ingestão (token do job, nunca o do Firebase)
 
### ING-01 · POST `/jobs/ingestao-pncp` — sem token
 
- **Quem chama:** sem token
- **Status esperado:** 401 Unauthorized · código de erro `NAO_AUTENTICADO`
- **Em *Test Results* deve aparecer (tudo verde):** status 401; codigo NAO_AUTENTICADO
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized
 
<img width="716" height="376" alt="image" src="https://github.com/user-attachments/assets/3bc1571c-4183-40bb-a94f-a9dee15af5a7" />

 
---
### ING-02 · POST `/jobs/ingestao-pncp` — com token do job errado
 
- **Quem chama:** token inválido (texto qualquer)
- **Status esperado:** 401 Unauthorized
- **Em *Test Results* deve aparecer (tudo verde):** status 401
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized
 
<img width="714" height="376" alt="image" src="https://github.com/user-attachments/assets/10f96ec2-c3bb-4d13-9a8b-8060b8968310" />

 
---
### ING-03 · POST `/jobs/ingestao-pncp` — com token de usuário do Firebase
 
- **Quem chama:** decisor A (`decisor.a@`)
- **Status esperado:** 401 Unauthorized
- **Em *Test Results* deve aparecer (tudo verde):** status 401
- **Observação do caso:** O token do usuário não vira token do job.
**Validado?**
 
- [X] Sim
- [ ] Não
      
Status recebido: 401 Unauthorized
 
<img width="708" height="397" alt="image" src="https://github.com/user-attachments/assets/8378f831-c9f8-45f3-bb34-b8eae768acfd" />

 
---
 
## Resumo da execução
 
| Item | Quantidade |
|---|---|
| Casos executados |65|
| Validados (Sim) |65|
| Reprovados (Não) |0|
| Não executados (motivo abaixo) |0|
 
### Casos reprovados
 
| ID | O que aconteceu | Issue aberta |
|---|---|---|
| | | |
 
### Casos não executados
 
| ID | Motivo |
|---|---|
| | |
 
### Conclusão
 
- [X] Aprovado: todos os casos validados.
- [ ] Aprovado com ressalvas: ____________________________________________
- [ ] Reprovado: ____________________________________________

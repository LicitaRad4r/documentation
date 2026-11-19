# Matriz de rastreabilidade
 
**Requisito → critério de aceite → caso de teste (CT) → resultado** · Última atualização: 19/11/2026
 
## Como ler
 
- Cada linha é um caso de teste (CT). O ID segue `CT-<MÓDULO>-<NNN>` (ver `estrategia_de_testes.md`, seção 6).
- Os valores-limite (BVA) e as classes de equivalência de cada CT estão em [`ep-bva-v1.md`](ep-bva-v1.md).
- A ordem de execução está em [`criterios-prioritarios.md`](criterios-prioritarios.md).
| Resultado | Significado |
| --- | --- |
| ✅ Passou | Executado e aprovado |
| ⚠️ Parcial | Executado, mas só parte do critério foi coberta (o detalhe está na seção "Execuções registradas") |
| ❌ Falhou | Executado e reprovado |
| ⬜ Pendente | Ainda sem teste executado |
 
**Base da atualização de 19/11/2026:** os testes unitários, de contrato e de cliente foram executados na `main` do repositório de código; o resultado de cada CT vem do teste que leva o ID no título. Os testes de integração com emulador e o E2E local não foram reexecutados nessa data (ver "Execuções registradas").
 
**Arquivos de teste:** `unit/…`, `contract/…` e `integration/…` ficam em `server/tests/`; "cliente" são os testes em `client/src/`; `api-real.spec.ts` e `api-real-kanban.spec.ts` ficam em `client/tests-e2e/`.
 
**Níveis:** Unitário · Integração · API · Regras (Firestore) · E2E.
 
## Sumário
 
- [Jornada 1: Triagem Inteligente no Feed](#jornada-1-triagem-inteligente-no-feed-ct-feed-001-a-009)
- [Jornada 2: Busca Direcionada e Filtros Dinâmicos](#jornada-2-busca-direcionada-e-filtros-dinâmicos-ct-filt-001-a-009)
- [Jornada 3: Monitorização de Risco Documental e Revalidação](#jornada-3-monitorização-de-risco-documental-e-revalidação-ct-cert-001-a-010)
- [Jornada 4: Barreira de Segurança e Acesso Cruzado](#jornada-4-barreira-de-segurança-e-acesso-cruzado-ct-seg-001-a-011)
- [Execuções registradas](#execuções-registradas)
---
 
## Jornada 1: Triagem Inteligente no Feed (CT-FEED-001 a 009)
 
**Rastreabilidade:** US07, US08, US09, US11, US12, RF04, RF06, ADR-0007 e ADR-0008
 
| CT | Cenário | Requisitos | Critério de aceite | Valores-limite (BVA) | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **CT-FEED-001** | Ordenação por Score | US09, US12, RF04, RF06 | Lista em ordem não crescente de Score | -1, 0, 1, 99, 100, 101 | Unitário e integração | `unit/feed.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FEED-002** | Desempate | US09, RF04 | Mesmo Score: prazo mais próximo, publicação mais recente, ID | Diferença de 1 min/1 dia no prazo; ID_A vs ID_B (alfabeto/numérico) | Unitário | `unit/feed.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FEED-003** | Isolamento de perfil | US08, US11, RF06, ADR-0008 | Edital A: 100 (Empresa A) e 75 (B); edital C: 75 (A) e 100 (B); D, E e F descartados | Matriz com 0 correspondências, 1 correspondência, N correspondências | Integração | `unit/feed.test.ts`; `unit/score.test.ts`; E2E-06 (`api-real.spec.ts`) | ✅ Passou (19/11/2026) |
| **CT-FEED-004** | Sem chamada ao PNCP | US07, US12, RF04, ADR-0007 | Nenhuma requisição externa ao abrir o feed | Fronteira entre 0 e 1 requisição externa | Integração | `unit/feed.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FEED-005** | Carregamento | US09, RF04 | Estado de loading aparece antes da lista | Tempo de resposta = 0ms, < 100ms (imediato), > 500ms (Loader visível) | E2E | — | ⬜ Pendente: o componente não tem lógica de tempo para testar |
| **CT-FEED-006** | Seed vazio | US09, RF04 | Mensagem "sem editais ingeridos" | Quantidade de editais na base = 0, 1, 2 | E2E | `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026): contrato da API |
| **CT-FEED-007** | Todos descartados | US09, US11, RF06 | Mensagem do perfil, com orientação | Editais compatíveis = 0, 1 | E2E | — (existe teste em `PaginaFeed.test.tsx` sem o ID no título) | ⚠️ Parcial: falta colocar o ID no título do teste |
| **CT-FEED-008** | API falha | US09, RF04 | Mensagem de erro com "Tentar novamente", sem tela em branco | Fronteira entre os limites de retentativas automáticas (Ex: 0, 3, 4) | E2E | cliente: `lib/cache.test.ts` | ⚠️ Parcial: retentativa e erro com "Tentar novamente" testados; falta E2E com falha real |
| **CT-FEED-009** | Paginação | US09, RF04, RNF05 | Página limitada ao pageSize, sem repetição entre páginas | pageSize = 0, 1, 2, 99, 100, 101; page = 0, 1 | Integração | `unit/feed.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
 
## Jornada 2: Busca Direcionada e Filtros Dinâmicos (CT-FILT-001 a 009)
 
**Rastreabilidade:** US10, US11, RF05, RF06, ADR-0003, ADR-0008 e página Filtros
 
| CT | Cenário | Requisitos | Critério de aceite | Valores-limite (BVA) | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **CT-FILT-001** | valorMin=50000 | US10, RF05 | valorMin=50000: só valor >= 50000; Score e ordem iguais aos do feed sem filtro | `-0.01`, `0`, `0.01`; `49999.99`, `50000.00`, `50000.01`; `min = max` | Integração | `unit/feed.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FILT-002** | Filtro impossível (valorMin de 1 bilhão) | US10, RF05 | "Sem resultados para estes filtros" e botão "Limpar filtros" | `valorMin = 1.000.000.000` → Mensagem *"Sem resultados para estes filtros"* e botão *"Limpar filtros"* | E2E | cliente: `PaginaFeed.complementos.test.tsx` | ⚠️ Parcial: componente com mock; o texto do vazio na tela difere do plano |
| **CT-FILT-003** | Clicar em "Limpar filtros" | US10, RF05 | Limpar filtros devolve o feed completo na ordem original | Transição: N filtros aplicados → 0 filtros (feed original completo) | E2E | cliente: `PaginaFeed.complementos.test.tsx` | ⚠️ Parcial: componente com mock |
| **CT-FILT-004** | Filtro não recupera descartado | US10, US11, RF05, RF06 | Edital descartado (caso D) não reaparece com valorMin/valorMax que o incluiriam | Parâmetros de busca englobando o escopo do edital D → permanência do descarte | Integração | `unit/feed.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FILT-005** | Filtros não recalculam | US10, US11, RF05, RF06, ADR-0008 | Score de cada edital igual com e sem filtros | Diferença de Score antes e depois do filtro = `0` | Unitário e integração | `unit/feed.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FILT-006** | Palavra-chave | US10, RF05, RF06, ADR-0008 | "NOTEBOOK" e "Notébook" encontram o mesmo edital | Termos com acento vs sem acento gerando idênticos resultados (`"NOTEBOOK"` e `"Notébook"`) | Unitário | `unit/feed.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FILT-007** | Parâmetro inválido | US10, RF05, RNF05 | 400 com mensagem; tela mantém a lista anterior | Envio de parâmetro inválido → HTTP `400` e preservação da lista anterior no tela | Integração | `unit/feed.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
| **CT-FILT-008** | Erro durante a pesquisa | US10, RF05 | Erro na pesquisa: mensagem com "Tentar novamente"; filtros mantidos | Estado dos inputs de filtro preservados intactos após a falha HTTP | E2E | cliente: `PaginaFeed.erro-filtros.test.tsx` | ✅ Passou (19/11/2026): componente com mock |
| **CT-FILT-009** | Desempenho | US10, RF05, RNF03 | p95 de leitura <= 800 ms com o seed | Limiar estrito: `799 ms` vs `801 ms` (calculado sobre 200 amostras) | Integração | `unit/desempenho.test.ts`; k6 (`k6/carga.js`) | ⚠️ Parcial: p95 medido em memória; no k6 local o maior p95 de leitura foi 46,1 ms (meta 800 ms, 16/11/2026); falta medir com o emulador |
 
## Jornada 3: Monitorização de Risco Documental e Revalidação (CT-CERT-001 a 010)
 
**Rastreabilidade:** US14, US15, US16, US03, RF08 a RF10 e RNF05
 
| CT | Cenário | Requisitos | Critério de aceite | Valores-limite (BVA) | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **CT-CERT-001** | Limites da classificação: -1, 0, 15, 16, 30 e 31 dias | US08, RF08 | -1, 0, 15, 16, 30 e 31 dias: Vencida, Urgente, Urgente, Atenção, Atenção e Em dia | -1 e 0 (Vencida → Urgente); 15 e 16(Urgente → Atenção); 30 e 31 (Atenção → Em dia) | Unitário | `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
| **CT-CERT-002** | Painel com o seed | US16, RF10 | Certidões em alerta, da mais urgente para a menos; a de 31 dias não aparece | Limiar estrito: 30 dias (aparece no painel) vs 31 dias (some do painel) | Integração e E2E | cliente: `PaginaCertidoes.fronteiras.test.tsx` | ⚠️ Parcial: a tela mostra a certidão de 31 dias no grupo "Em dia"; o plano diz que ela não aparece |
| **CT-CERT-003** | Renovar com dados válidos | US15, RF08, RF10 | Etiqueta recalculada na hora, sem avançar o relógio | Recálculo imediato do status no payload de resposta HTTP `200` | E2E | `unit/certidoes.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
| **CT-CERT-004** | Nova validade a mais de 30 dias | US15, US16, RF10 | Nova validade a mais de 30 dias: o alerta some | Fronteira exata: 30 dias (continua no painel) vs 31 dias (o alerta desaparece) | Integração e E2E | `unit/certidoes.test.ts` | ✅ Passou (19/11/2026) |
| **CT-CERT-005** | Nova validade a 20 dias | US15, US16, RF10 | Nova validade a 20 dias: o alerta continua como Atenção | Fronteiras da faixa de Atenção: 16 dias vs 30 dias | Integração | `unit/certidoes.test.ts` | ✅ Passou (19/11/2026) |
| **CT-CERT-006** | Data no passado, ou não posterior à atual | US14, US15, RF09, RNF05 | Data no passado ou não posterior à atual: 400 com o campo; nada gravado | `hoje`, `atual`, `atual + 1 dia` → HTTP 400 com erro no campo validade e base intacta | Integração | `unit/certidoes.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
| **CT-CERT-007** | Arquivo inválido (tipo ou mais de 5MB) | US14, US15, RF08, RNF05 | Arquivo inválido (tipo ou > 5MB): 400; certidão anterior inalterada | `5 MB` exatos (passa) vs `5 MB + 1 byte` ou formato inválido → HTTP `400` e arquivo anterior inalterado | Integração | `unit/certidoes.test.ts` | ✅ Passou (19/11/2026) |
| **CT-CERT-008** | Painel sem alertas | US16, RF10 | "Todas as certidões estão em dia" | `0` itens em risco no array de listagem de alertas | E2E | cliente: `PaginaCertidoes.falha-envio.test.tsx` (`it.todo`) | ⬜ Pendente: a mensagem "Todas as certidões estão em dia" não existe no código |
| **CT-CERT-009** | Falha de rede no envio | US15, RF08 | Falha de rede no envio: erro com "Tentar novamente"; certidão anterior mantida | Estado prévio do registro preservado após falha HTTP (ex: `500` / timeout) | E2E | cliente: `PaginaCertidoes.falha-envio.test.tsx` | ✅ Passou (19/11/2026): componente com mock |
| **CT-CERT-010** | Rotina das 00h00 | US16, RF10 | Com relógio controlado, as etiquetas mudam ao virar o dia | Instante `23:59:59` BRT (`02:59:59Z`) vs `00:00:00` BRT (`03:00:00Z`) alterando os dias restantes | Unitário | `unit/certidoes.test.ts` | ✅ Passou (19/11/2026) |
 
## Jornada 4: Barreira de Segurança e Acesso Cruzado (CT-SEG-001 a 011)
 
**Rastreabilidade:** US01 a US06, RF01 a RF03, RNF06 e ADR-0005
 
A jornada tem duas tabelas: primeiro **o que se verifica**; depois **a rastreabilidade e o resultado**.
 
### O que se verifica
 
| CT | Quem | Pedido | API esperada | Interface | Critério de aceite |
| --- | --- | --- | --- | --- | --- |
| **CT-SEG-001** | Sem sessão | GET /feed sem token | 401 | Redireciona ao login | GET /feed sem token: 401 e redireciona ao login |
| **CT-SEG-002** | Token adulterado | GET /feed com assinatura inválida | 401 | Redireciona ao login | Assinatura inválida: 401 e redireciona ao login |
| **CT-SEG-003** | Token expirado | GET /feed com token vencido | 401 | Login, com aviso de sessão expirada | Token expirado: 401 e login com aviso de sessão expirada |
| **CT-SEG-004** | Token válido sem orgId ou role | GET /feed | 403 | Acesso Negado | Token válido sem orgId ou role: 403 |
| **CT-SEG-005** | Analista | GET /certidoes | 403 | Página "Acesso Negado (403)"; menu sem a aba | Analista em GET /certidoes: 403, página Acesso Negado, menu sem a aba |
| **CT-SEG-006** | Analista | PUT /certidoes/{id}/renovacao | 403; nada gravado | Não aplicável | Analista em PUT renovacao: 403 e nada gravado |
| **CT-SEG-007** | Decisor da empresa A | GET /certidoes/{id da B} | 403 | Acesso Negado | Decisor da A lendo certidão da B: 403 |
| **CT-SEG-008** | Decisor da empresa A | PUT /certidoes/{id da B}/renovacao | 403; certidão da B inalterada | Não aplicável | Decisor da A renovando certidão da B: 403 e certidão da B inalterada |
| **CT-SEG-009** | Analista da empresa A | GET /perfil com orgId da B | 403 | Acesso Negado | Analista da A em GET /perfil com orgId da B: 403 |
| **CT-SEG-010** | Cliente do Firebase | Leitura e escrita direta no Firestore, sem passar pela API | Negado pelas regras | Não aplicável | Leitura e escrita direta no Firestore, sem a API: negado pelas regras |
| **CT-SEG-011** | Qualquer bloqueio | Verifica corpo e log | Sem dados do recurso nem pessoais | Não aplicável | Bloqueio sem dados do recurso nem pessoais (corpo e log) |
 
### Rastreabilidade e resultado
 
| CT | Requisitos | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- |
| **CT-SEG-001** | US02, RF01 | API e E2E | `unit/seguranca.test.ts`; `contract/openapi-contract.test.ts`; E2E-01; KAN-SEG-01 | ✅ Passou (19/11/2026) e Render (16/11/2026). Redirecionamento ao login não coberto |
| **CT-SEG-002** | US02, RF01 | API | `unit/seguranca.test.ts`; E2E-01; KAN-SEG-01 | ✅ Passou (19/11/2026) e Render (16/11/2026) |
| **CT-SEG-003** | US02, US03, RF01 | API e E2E | `unit/seguranca.test.ts`; KAN-SEG-01 | ✅ Passou (19/11/2026) e Render (16/11/2026), com token vencido simulado |
| **CT-SEG-004** | US01, US04, US05, RF01, RF02, RNF06 | API | `unit/seguranca.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
| **CT-SEG-005** | US06, RF03 | API e E2E | `unit/seguranca.test.ts`; `unit/certidoes.test.ts`; E2E-02 | ✅ Passou (19/11/2026) e Render (16/11/2026) |
| **CT-SEG-006** | US06, RF03 | API | `unit/seguranca.test.ts`; `unit/certidoes.test.ts` | ✅ Passou (19/11/2026); não coberto no Render |
| **CT-SEG-007** | RNF06 | API | `unit/seguranca.test.ts`; `contract/openapi-contract.test.ts`; E2E-03; KAN-SEG-04 | ✅ Passou (19/11/2026); Render parcial (ver seção B) |
| **CT-SEG-008** | RNF06 | API | `unit/seguranca.test.ts`; `unit/certidoes.test.ts`; KAN-SEG-05 | ✅ Passou (19/11/2026); Render parcial (ver seção B) |
| **CT-SEG-009** | RNF06 | API | `unit/seguranca.test.ts`; `contract/openapi-contract.test.ts`; E2E-03; KAN-SEG-06 | ✅ Passou (19/11/2026); Render parcial (ver seção B) |
| **CT-SEG-010** | RNF06 | Regras | `integration/firestore-deny.all.test.ts` (21 testes) | ✅ Passou: 21 de 21 (emulador, 28/10/2026; o CI roda no job `firestore rules`) |
| **CT-SEG-011** | RNF06 | API | `unit/seguranca.test.ts`; `contract/openapi-contract.test.ts` | ✅ Passou (19/11/2026) |
 
---
 
## Execuções registradas
 
### A. CT-SEG-010: regras do Firestore (emulador)
 
| Item | Detalhe |
| --- | --- |
| Ambiente | Emulador do Firestore em Docker; `npm run test:int` em `server/` |
| Data | 28/10/2026 |
| Resultado | ✅ 21 passaram, 0 falharam |
| Regra testada | `firestore.rules` em deny-all (`allow read, write: if false;`) |
 
Verifica, para sem login, logado sem claim `empresaId`, decisor da A e analista da A: não lê o próprio documento nem o de outra empresa, não lista a coleção, não cria/atualiza/apaga, não acessa `editais`, `scores` e `usuarios`; e os dados seguem intactos depois das tentativas (4 × 5 + 1 = 21).
 
### B. Autorização contra a API publicada (Render)
 
| Item | Detalhe |
| --- | --- |
| Ambiente | API no Render (`https://licitaradar-y2gp.onrender.com`), Firebase real (projeto `licitaradar-13047`) |
| Data | 16/11/2026 |
| Resultado | ✅ 13 passaram, 0 falharam (25,9 s): `api-real.spec.ts` (E2E-01, 02, 03, 11 e 12) e `api-real-kanban.spec.ts` (KAN-SEG-01 a 08), todos com a tag `@seguranca` |
| Dados | Só as 2 empresas e os 3 usuários de teste do seed de produção. Sem carga; a carga (k6) roda só no emulador |
 
**Resultado por CT**
 
| CT | Teste(s) no Render | Resultado | Observação |
| --- | --- | --- | --- |
| CT-SEG-001 | E2E-01; KAN-SEG-01 (rotas do kanban) | ✅ Passou na API | |
| CT-SEG-002 | E2E-01; KAN-SEG-01 | ✅ Passou na API | |
| CT-SEG-003 | KAN-SEG-01 (JWT com `exp` vencido e assinatura falsa) | ✅ Passou na API | Token vencido simulado |
| CT-SEG-005 | E2E-02 | ✅ Passou na API | |
| CT-SEG-007 | E2E-03; KAN-SEG-04 | ⚠️ Parcial | Listas isoladas entre A e B; `GET /certidoes/{id da B}` direto não coberto |
| CT-SEG-008 | KAN-SEG-05 | ⚠️ Parcial | Vale para o kanban (PATCH e DELETE → 403); `PUT /renovacao` não coberto |
| CT-SEG-009 | E2E-03; KAN-SEG-06 | ⚠️ Parcial | `empresaId` forjado → 403 no feed e no kanban; `GET /perfil` não coberto |
 
**Cobertura nova, sem CT na matriz:** job de ingestão com token inválido → 401 (E2E-12, KAN-SEG-08) e rotas do kanban `POST`, `GET`, `PATCH` e `DELETE /acompanhamentos` (KAN-SEG-01 a 07: login, empresa dona do registro, `empresaId` forjado, 404 e 400).
 
**Limitação:** o "token expirado" é um JWT bem formado com `exp` no passado e assinatura falsa; a API o recusa com 401, mas não é um token real do Firebase vencido.
 
### C. Testes automatizados na `main` (19/11/2026)
 
| Suíte | Comando | Resultado |
| --- | --- | --- |
| Servidor: unitários e de contrato | `npm run test:unit` (em `server/`) | ✅ 220 de 220 (15 arquivos) |
| Cliente | `npm run test:unit` (em `client/`) | ✅ 97 passaram + 1 `todo` (18 arquivos) |
| Servidor: integração com emulador | `npm run test:int` | Não reexecutado nesta data. São 27 testes: 21 do deny-all e 6 do kanban sobre o Firestore. O CI roda no job `firestore rules` |
| Carga (k6, emulador) | `CURTO=1 k6 run k6/carga.js` | ✅ 16/11/2026: 792 requisições, 0% de falha; maior p95 de leitura 46,1 ms (meta 800 ms) e de escrita 36,8 ms (meta 1500 ms). Detalhe em `docs/operacao/k6-p95.md` do repositório de código |

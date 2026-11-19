# LicitaRadar: Estratégia de Testes
 
**Documentos relacionados:** [Matriz de rastreabilidade](matriz-rastreabilidade.md) · [EP/BVA](ep-bva-v1.md) · [Critérios prioritários](criterios-prioritarios.md) · [Roteiro E2E](roteiro-e2e-api-real.md) · [Relatório CP2/CP3](relatoriocp2.md)
 
**Neste documento:** [1. Visão geral](#1-visão-geral) · [2. Riscos](#2-riscos-que-guiam-os-testes) · [3. Pirâmide](#3-pirâmide-de-testes) · [4. Camadas](#4-o-que-testar-em-cada-camada) · [5. Ambientes e mocks](#5-ambientes-dados-de-teste-e-mocks) · [6. Casos de teste](#6-padrão-de-casos-de-teste) · [7. Métrica de relevância](#7-métrica-de-relevância-do-feed-precisão) · [8. Integração contínua](#8-integração-contínua-github-actions) · [9. Entrada, saída e bugs](#9-critérios-de-entrada-e-saída-e-gestão-de-bugs) · [10. Situação atual](#10-situação-atual-19112026)
 
## 1. Visão geral
 
O LicitaRadar é testado em quatro camadas, com a maior parte das verificações nos testes unitários da lógica de negócio.
 
**O produto em uma frase.** Monitor diário de licitações que puxa os editais do PNCP, filtra pela UF e ramo da PME, ranqueia cada edital com nota de 0 a 100 por aderência, avisa 48 h antes de cada prazo e alerta quando uma certidão está perto de vencer.
 
**Para quem.** Diretores comerciais de PMEs de TI, material de escritório ou limpeza que vendem a órgãos públicos.
 
**Objetivo desta estratégia.** Garantir, com o menor custo de execução possível, que:
 
1. O usuário nunca perca um edital aderente por falha nossa (ingestão, filtro ou ranqueamento).
2. O usuário nunca seja inabilitado por um alerta de certidão que não chegou ou chegou errado.
3. A nota de aderência seja determinística: mesma entrada, mesma nota, sempre.
4. Os 10 primeiros resultados do feed sejam úteis para o usuário (métrica de relevância, seção 7).
### Escopo de testes
 
- Configuração de perfil (termos, UFs, modalidades, faixa de valor)
- Ingestão diária do PNCP, normalização e duplicação
- Score de aderência (0–100) e ordenação do feed
- Acompanhamento de edital e etapas de preparação
- Controle de validade das certidões
- Alertas de prazo (48 h) e de vencimento de certidão
- Marcações de relevante / irrelevante
- Regras de segurança do Firestore
## 2. Riscos que guiam os testes
 
Testamos primeiro e com mais profundidade o que, se falhar, faz a PME perder dinheiro. Cada risco abaixo aponta a camada que o cobre.
 
| # | Risco | Impacto para o usuário | Prioridade | Camada principal |
| --- | --- | --- | --- | --- |
| R1 | Score calcula nota errada ou não determinística | Editais bons ficam no fim do feed; perde oportunidade | Alta | Unitário |
| R2 | Cálculo de prazo erra fuso, dia útil ou janela de 48 h | Alerta chega tarde ou não chega | Alta | Unitário |
| R3 | Validade de certidão calculada errada | Inabilitação no último momento | Alta | Unitário |
| R4 | Ingestão duplica editais ou perde editais do dia | Feed poluído ou incompleto | Alta | Integração |
| R5 | PNCP fora do ar, lento ou com resposta mal formada | Quebra e ninguém fica sabendo | Alta | Integração |
| R6 | Normalização trata mal campos do PNCP (valores, datas, UF, modalidade) | Filtro exclui edital que deveria aparecer | Média | Unitário |
| R7 | Usuário lê ou altera dados de outra empresa | Vazamento de dados, quebra de confiança | Alta | Integração (regras Firestore) |
| R8 | API muda formato e quebra o front | Tela em branco ou erro | Média | API e contrato |
| R9 | Jornada principal quebra no navegador | Usuário não consegue usar o produto | Média | E2E |
| R10 | Feed não reflete as marcações de relevante / irrelevante | Produto não melhora; métrica cai | Média | Unitário + métrica |
 
## 3. Pirâmide de testes
 
Cerca de 85% dos testes ficam nas duas camadas de baixo (unitários e integração), todos rodando no Vitest; o topo existe só para confirmar que tudo funciona junto.
 
```text
                 /\
                /  \        E2E (~5%)           "o usuário consegue?"
               /----\
              /      \      API e contrato (~10%)  "a API cumpre o contrato?"
             /--------\
            /          \    Integração (~20%)    "as peças conversam?"
           /------------\
          /              \  Unitários (~65%)     "a regra está certa?"
         /----------------\
        mais rápidos e baratos (base)  →  mais lentos e caros (topo)
```
 
**Justificativa: custo e precisão.** Na base, os testes são rápidos e apontam o problema exato: se `calcularNota()` falha, sabemos que a falha está no score. No topo, são lentos e, quando falham, só sabemos que algo deu errado em algum lugar do caminho (front, API, banco ou ingestão). Por isso a maior parte das verificações fica embaixo, e o topo serve para confirmar que tudo funciona junto.
 
**Os percentuais são uma referência, não uma regra.** Eles indicam a proporção de *casos de teste* por camada. Se o time perceber que está escrevendo muitos E2E para cobrir regra de negócio, é sinal de que a regra deveria estar testada no unitário.
 
## 4. O que testar em cada camada
 
Cada camada responde a uma pergunta diferente; se um teste não responde à pergunta da sua camada, ele está no lugar errado.
 
### 4.1 Unitários (\~65%) — "a regra está certa?"
 
Testam funções puras (sem banco e sem rede) com o Vitest.
 
| Módulo | O que verificar | Exemplos de casos |
| --- | --- | --- |
| Normalização | Converter o JSON do PNCP no nosso modelo interno | Valor "1.234,56" vira 1234.56; data com fuso vira ISO em UTC; UF minúscula vira maiúscula; campo ausente vira `null` sem quebrar |
| Score (0–100) | Nota determinística por aderência ao perfil | Edital que bate termo + UF + modalidade + faixa recebe nota alta; nenhum critério recebe 0; nota nunca sai de 0–100; mesma entrada dá mesma nota |
| Ordenação do feed | Ordem por nota e critério de desempate | Duas notas iguais: desempata por prazo mais próximo; edital vencido sai do feed |
| Prazos | Janela de alerta de 48 h | Prazo em 49 h: sem alerta; em 47 h: alerta; prazo já passou: sem alerta |
| Certidões | Status de validade | Vence em 30 dias: "perto de vencer"; vence hoje: "vence hoje"; vencida: "vencida"; sem data: "pendente" |
| Duplicação | Identificar o mesmo edital vindo duas vezes | Mesmo número de controle PNCP gera uma única chave |
| Validação | Rejeitar entradas inválidas | Faixa de valor mínima maior que a máxima é rejeitada; UF inexistente é rejeitada |
| Feedback | Efeito das marcações relevante / irrelevante | Edital marcado irrelevante desce no feed; marcação não altera a nota de outro usuário |
 
### 4.2 Integração (\~20%) — "as peças conversam?"
 
Testes que sobem partes reais do sistema, mas com dependências externas simuladas. Também em Vitest.
 
- **Rotas Fastify com emuladores.** Usar `app.inject()` do Fastify (simula requisições HTTP às rotas da API, em memória) contra o Firestore do Firebase Emulator Suite (simula o banco na nuvem). A validação de entrada usa Zod.
- **Ingestão com PNCP mockado.** O cliente HTTP do PNCP é substituído por respostas gravadas (fixtures). Cobrir: página normal, várias páginas, resposta vazia, erro 500, timeout e JSON mal formado. Rodar a ingestão duas vezes com o mesmo lote e verificar que o número de editais não muda (idempotência).
- **Recuperação de falhas.** Ingestão interrompida no meio continua do ponto certo na execução seguinte, sem duplicar.
- **Regras do Firestore.** Com `@firebase/rules-unit-testing`: o usuário da empresa A lê e altera só os dados da empresa A; usuário não autenticado não lê nada.
### 4.3 API e contrato (\~10%) — "a API cumpre o que promete?"
 
- O Fastify gera a especificação **OpenAPI** (Swagger) a partir dos schemas Zod. A especificação é a fonte da verdade do contrato entre front e back.
- A coleção **Postman** é gerada do OpenAPI e roda no CI via **Newman** contra o back-end com emuladores.
- Cada rota tem pelo menos: um caso de sucesso, um de entrada inválida (400), um sem autenticação (401) e um de recurso de outra empresa (403 ou 404).
- Se a especificação mudar de forma incompatível (campo removido ou renomeado), o CI falha até o front ser atualizado.
### 4.4 E2E (\~5%) — "o usuário consegue fazer o que veio fazer?"
 
Somente as cinco jornadas do MVP, no Playwright, contra o front servido localmente e o back-end com emuladores e PNCP mockado:
 
1. Configurar perfil (termos, UFs, modalidades e faixa de valor) e salvar.
2. Abrir o feed ranqueado, aplicar filtros e ver a ordem correta.
3. Acompanhar um edital e marcar etapas de preparação.
4. Cadastrar uma certidão e ver o status de validade.
5. Ver os alertas de oportunidade e de vencimento.
Os testes E2E não verificam regra de cálculo (isso já está nos unitários). Eles verificam que a tela mostra o que o back-end devolveu e que o usuário consegue completar a jornada.
 
## 5. Ambientes, dados de teste e mocks
 
Nenhum teste automatizado toca o PNCP real nem o Firebase de produção: tudo roda local ou no CI, sem custo e sem risco.
 
| Ambiente | Onde roda | Banco | PNCP | Quem usa |
| --- | --- | --- | --- | --- |
| Local | Máquina local (Docker Compose) | Firebase Emulator Suite | Fixtures gravadas | Dev, a cada alteração |
| CI | GitHub Actions | Firebase Emulator Suite (job `firestore rules`) e testes em memória | Fixtures gravadas | Todo pull request |
| Produção | API no Render (Free) + Firebase Spark (Auth, Firestore, Hosting), projeto `licitaradar-13047` | Firestore de produção | PNCP real, 1 vez ao dia (workflow `Ingestão PNCP`) | Usuários; smoke test depois de cada deploy; testes de autorização sem carga, só com os dados do seed |
 
> [!NOTE]
> Este plano previa um ambiente de **staging** (Cloud Run + projeto Firebase separado). O projeto roda no plano gratuito (um serviço no Render e um projeto Firebase), então não há staging: o que valida o deploy é o smoke test e a suíte de autorização contra a URL publicada. Carga (k6) só no emulador.
 
### Dados de teste
 
- **Fixtures do PNCP** em `tests/fixtures/pncp/`: respostas reais gravadas uma vez da API pública e anonimizadas quando necessário. Incluir casos difíceis: valor nulo, edital sem UF, data em formato diferente, edital repetido em duas páginas.
- **Perfis de teste** (os mesmos da demo): "PME de TI no DF" e "PME de limpeza em GO", com termos, UFs, modalidades e faixa de valor definidos.
- **Certidões de teste** com datas relativas a um "hoje" fixo: vencida, vence hoje, vence em 7 dias, vence em 30 dias, válida por 6 meses.
- **Seed do emulador** em um script `npm run seed:emulator`, para qualquer pessoa do time ter o mesmo banco em segundos.
### Mocks
 
- **PNCP:** cliente HTTP isolado em um módulo (`pncpClient`) e substituído por fixtures nos testes. Opção: MSW (Mock Service Worker) para interceptar as chamadas.
- **Relógio:** `vi.useFakeTimers()` e `vi.setSystemTime()` no Vitest; `page.clock` no Playwright.
- **Envio de alertas (e-mail/push):** substituído por um coletor em memória; o teste verifica o que *seria* enviado.
- **Segredos:** no CI e local, variáveis falsas em `.env.test`; o Secret Manager só é usado em staging e produção.
## 6. Padrão de casos de teste
 
Todo caso de teste, automatizado ou manual, é documentado com os mesmos sete componentes, para qualquer pessoa do time conseguir executá-lo e rastreá-lo.
 
| Componente | Para que serve | Como preencher |
| --- | --- | --- |
| ID do caso de teste | Rastrear o teste ao longo do sprint e vincular a relatórios de bugs | `CT-<MÓDULO>-<NNN>`, ex.: `CT-SCORE-001`. Módulos: PERFIL, ING, NORM, SCORE, FEED, FILT, PRAZO, CERT, ALERTA, AUTH, SEG, E2E |
| Descrição do teste | Resumo simples do que está sendo testado | Uma frase com o comportamento: "Nota é 0 quando nenhum critério do perfil é atendido" |
| Pré-requisitos | Tudo que precisa estar pronto antes de começar | Dados de teste, contas de usuário, configurações do sistema (ex.: emulador com seed, perfil "PME de TI no DF") |
| Etapas do teste | Ações que o testador executa | Passos numerados e específicos, que qualquer testador repita igual |
| Resultados esperados | O que deve acontecer em cada etapa | Específico e mensurável: "nota = 0", "status HTTP 403", "alerta exibido" |
| Resultados reais | O que de fato aconteceu na execução | Preenchido na execução; em teste automatizado, o relatório do CI |
| Status | Resultado da execução | Passou, Parcial, Falhou ou Bloqueado (algum obstáculo impediu a execução). Mesmo vocabulário da matriz de rastreabilidade |
 
No código, o ID vai no nome do teste, para o relatório do CI apontar direto para o caso: `it('CT-PRAZO-002: gera alerta 47 h antes do prazo', …)`.
 
### Exemplos preenchidos
 
#### CT-SCORE-001: Nota é 0 quando nenhum critério é atendido (unitário)
 
- **Pré-requisitos:** perfil "PME de TI no DF" (termos: software, licença; UF: DF; modalidade: pregão eletrônico; faixa R$ 10 mil a R$ 500 mil); fixture de edital de limpeza em SP, concorrência, R$ 2 milhões.
- **Etapas:** 1. Normalizar o edital. 2. Calcular a nota com o perfil.
- **Resultados esperados:** 1. Edital normalizado sem erro. 2. Nota = 0.
- **Resultados reais:** preenchido na execução.
- **Status:** preenchido na execução.
#### CT-PRAZO-002: Alerta é gerado dentro da janela de 48 h (unitário)
 
- **Pré-requisitos:** relógio fixado em 13/10/2026 10:00 (Brasília); edital acompanhado com prazo em 15/10/2026 09:00.
- **Etapas:** 1. Executar a função de verificação de prazos.
- **Resultados esperados:** 1. Um alerta do tipo "prazo" para o edital, faltando 47 h.
- **Resultados reais e status:** preenchidos na execução.
#### CT-ING-003: Ingestão repetida não duplica editais (integração)
 
- **Pré-requisitos:** emulador do Firestore limpo; fixture com 25 editais do PNCP.
- **Etapas:** 1. Rodar a ingestão. 2. Contar editais. 3. Rodar a ingestão de novo com a mesma fixture. 4. Contar editais.
- **Resultados esperados:** 2. 25 editais. 4. Continua 25 editais.
- **Resultados reais e status:** preenchidos na execução.
#### CT-AUTH-001: Empresa não acessa certidões de outra empresa (integração, regras Firestore)
 
- **Pré-requisitos:** emulador com empresas A e B, cada uma com 1 certidão; usuário autenticado da empresa A.
- **Etapas:** 1. Ler as certidões da empresa A. 2. Ler as certidões da empresa B.
- **Resultados esperados:** 1. Leitura permitida. 2. Leitura negada.
- **Resultados reais e status:** preenchidos na execução.
#### CT-E2E-002: Feed mostra editais em ordem de nota (E2E)
 
- **Pré-requisitos:** front e back locais com seed; usuário logado com perfil "PME de TI no DF".
- **Etapas:** 1. Abrir o feed. 2. Ler as notas dos 10 primeiros cards. 3. Filtrar por modalidade "pregão eletrônico".
- **Resultados esperados:** 1. Feed carrega em menos de 3 s. 2. Notas em ordem decrescente. 3. Só aparecem pregões, ainda em ordem decrescente.
- **Resultados reais e status:** preenchidos na execução.
## 7. Métrica de relevância do feed: Precisão
 
O teste de qualidade do produto é: dos 10 primeiros editais do feed, quantos o usuário considera úteis. Essa métrica, chamada Precisão, é o que mostramos na demo.
 
$$
\text{Precisão} = \frac{\text{editais marcados como relevantes entre os 10 primeiros}}{10}
$$
 
**Como medir.**
 
1. Montar um **conjunto de referência** (golden set): para cada perfil da demo, uma lista fixa de editais (fixture) em que o time marca manualmente cada um como relevante ou irrelevante.
2. Rodar o score sobre esse conjunto e pegar os 10 primeiros.
3. Calcular a Precisão por perfil e a média entre perfis.
4. Repetir no CI a cada mudança no score: se a Precisão cair em relação à versão anterior, o pull request é sinalizado para revisão.
**Por que funciona como teste.** Como o score é determinístico, o mesmo golden set sempre gera o mesmo top 10. Qualquer mudança na Precisão vem de uma mudança no código, e não do acaso.
 
## 8. Integração contínua (GitHub Actions)
 
Nenhum código entra na branch `main` sem passar pelos testes; os mais rápidos rodam primeiro para dar resposta em poucos minutos.
 
**A cada pull request**, em ordem, parando na primeira falha:
 
1. **Lint e tipos:** ESLint + `tsc --noEmit` no front e no back.
2. **Unitários:** `vitest run --coverage` (front e back).
3. **Integração:** sobe o Firebase Emulator Suite (`firebase emulators:exec`) e roda os testes de rotas, ingestão e regras do Firestore.
4. **API e contrato:** gera o OpenAPI, roda a coleção Postman via Newman contra o back-end com emuladores.
5. **E2E:** Playwright com Chromium, apenas as cinco jornadas.
6. **Relevância:** calcula a Precisão no golden set e compara com a `main`.
**Deploy em produção:** automático depois do CI verde na `main` (veja a seção 10): o front, as regras e os índices do Firestore saem pelo workflow `Deploy`, e a API no Render é publicada pelo auto-deploy do próprio Render. O workflow `Smoke test` confere o front e a API logo depois.
 
### Critérios para aprovar um pull request
 
- Todos os passos acima verdes.
- Cobertura de linhas ≥ 80% nos módulos de normalização, score, prazos e certidões (cobertura geral não é meta, é sinal).
- Precisão não caiu em relação à `main` (ou a queda foi justificada no PR).
- Todo bug corrigido vem com um teste que falhava antes da correção.
- Pelo menos uma revisão de outra pessoa do time.
## 9. Critérios de entrada e saída, e gestão de bugs
 
Uma funcionalidade só é considerada pronta quando seus testes existem, passam no CI e cobrem os riscos da seção 2 que ela toca.
 
**Critérios de entrada** (para começar a testar uma funcionalidade):
 
- Requisito descrito com critério de aceite mensurável.
- Fixtures e dados de teste necessários disponíveis.
- Ambiente local com emuladores funcionando.
**Critérios de saída** (Definição de Pronto):
 
- Casos de teste documentados no padrão da seção 6.
- Testes unitários e de integração da funcionalidade passando no CI.
- Jornada E2E correspondente passando (quando houver).
- Nenhum bug de severidade Crítica ou Alta aberto.
### Gestão de bugs
 
Bugs são abertos como Issues no GitHub, com o rótulo `bug`, a severidade e o ID do caso de teste que falhou.
 
| Severidade | Definição | Exemplo no LicitaRadar |
| --- | --- | --- |
| Crítica | Usuário perde edital ou é inabilitado | Alerta de certidão não é gerado |
| Alta | Funcionalidade principal errada, com contorno difícil | Feed fora de ordem de nota |
| Média | Funciona, mas com erro visível | Filtro de UF não limpa ao trocar de perfil |
| Baixa | Cosmético | Texto desalinhado no card |
 
Cada issue de bug traz: passos para reproduzir, resultado esperado, resultado real, ambiente e evidência (print, log ou relatório do CI).
 
## 10. Situação atual (19/11/2026)
 
O que o plano prevê e o que existe hoje no repositório de código. As colunas foram conferidas lendo `.github/workflows/` e rodando os testes na `main`.
 
| Previsto neste plano | Situação | Observação |
| --- | --- | --- |
| Lint e tipos (ESLint + `tsc`) | ✅ Feito | Usa `oxlint` e `tsc` no `server` e no `client` |
| Unitários no CI | ✅ Feito | Servidor 220 testes, cliente 97 + 1 `todo` (19/11/2026) |
| Integração com emulador no CI | ✅ Feito | Job `firestore rules` roda `npm run test:int` (deny-all e kanban sobre o Firestore) |
| Cobertura ≥ 80% nos módulos críticos | ⚠️ Medida, não exigida | `npm run test:cov` dá 97,4% de instruções no servidor; o CI não falha por cobertura |
| Coleção Postman via Newman no CI | ⬜ Não feito | A coleção foi montada à parte e roda no Postman ou no Newman local; falta um passo no workflow |
| E2E (Playwright) no CI | ⚠️ Em parte | O CI roda o Playwright em modo mock (Chromium). Os E2E contra a API real (`api-real*.spec.ts`) rodam à mão, local e contra o Render |
| Precisão no golden set a cada PR | ⬜ Não feito | Existe o golden set de seed (`server/src/seed/golden.ts`, usado em CT-FEED-003), mas não há cálculo de Precisão |
| Staging | ❌ Não existe | Plano gratuito; ver nota na seção 5 |
| Deploy manual com aprovação | ✅ Substituído | Deploy automático depois do CI verde, com smoke test |
| Varredura de segredos | ✅ Extra | Job `gitleaks` em todo PR |
| Alertas de disponibilidade | ✅ Extra | UptimeRobot em `/readyz` e job diário de ingestão com issue em caso de falha (ver `docs/operacao/alertas.md` no repositório de código) |
| Teste de carga | ✅ Extra | k6 contra o emulador: maior p95 de leitura 46,1 ms (meta 800 ms) |
 
**Workflows do repositório de código:** `CI` (gitleaks, client, playwright mock, firestore rules, server, compose) · `Deploy` (Hosting, regras e índices) · `Smoke test` · `Hosting preview` (um canal por PR) · `Ingestão PNCP` (todo dia às 06:00 UTC).

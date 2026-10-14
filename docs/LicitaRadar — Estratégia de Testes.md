# LicitaRadar — Estratégia de Testes

## 1. Visão geral

O LicitaRadar será testado em quatro camadas, com a maior parte das verificações nos testes unitários da lógica de negócio

**O produto em uma frase.** Monitor diário de licitações que puxa os editais do PNCP, filtra pela UF e ramo da PME, ranqueia cada edital com nota de 0 a 100 por aderência, avisa 48 h antes de cada prazo e alerta quando uma certidão está perto de vencer.

**Para quem.** Diretores comerciais de PMEs de TI, material de escritório ou limpeza que vendem a órgãos públicos.

**Objetivo desta estratégia.** Garantir, com o menor custo de execução possível, que:

1. O usuário nunca perca um edital aderente por falha nossa (ingestão, filtro ou ranqueamento).
2. O usuário nunca seja inabilitado por um alerta de certidão que não chegou ou chegou errado.
3. A nota de aderência seja determinística: mesma entrada, mesma nota, sempre.
4. Os 10 primeiros resultados do feed sejam úteis para o usuário (métrica de relevância, seção 7).

### Escopo de Testes

|  |
| --- |
| Configuração de perfil (termos, UFs, modalidades, faixa de valor) |
| Ingestão diária do PNCP, normalização e duplicação |
| Score de aderência (0–100) e ordenação do feed |
| Acompanhamento de edital e etapas de preparação |
| Controle de validade das certidões |
| Alertas de prazo (48 h) e de vencimento de certidão |
| Marcações de relevante / irrelevante |
| Regras de segurança do Firestore |

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

&#91;embedded content: pirâmide de testes · 4 camadas\]

**Justificativa: custo e precisão.** Na base, os testes são rápidos e apontam o problema exato: se `calcularNota()` falha, sabemos que a falha está no score. No topo, são lentos e, quando falham, só sabemos que algo deu errado em algum lugar do caminho (front, API, banco ou ingestão). Por isso a maior parte das verificações fica embaixo, e o topo serve para confirmar que tudo funciona junto.

**Os percentuais são uma referência, não uma regra.** Eles indicam a proporção de *casos de teste* por camada. Se o time perceber que está escrevendo muitos E2E para cobrir regra de negócio, é sinal de que a regra deveria estar testada no unitário.

## 4. O que testar em cada camada

Cada camada responde a uma pergunta diferente; se um teste não responde à pergunta da sua camada, ele está no lugar errado.

### 4.1 Unitários (\~65%) — "a regra está certa?"

Funções (sem banco/sem rede).  Rodam com Vitest.

| Módulo | O que verificar | Exemplos de casos |
| --- | --- | --- |
| Normalização | Converter o JSON do PNCP no nosso modelo interno | Valor "1.234,56" vira 1234.56; data com fuso vira ISO em UTC; UF minúscula vira maiúscula; campo ausente vira `null` sem quebrar |
| Score (0–100) | Nota determinística por aderência ao perfil | Edital que bate termo + UF + modalidade + faixa recebe nota alta; nenhum critério recebe 0; nota nunca sai de 0–100; mesma entrada dá mesma nota |
| Ordenação do feed | Ordem por nota e critério de desempate | Duas notas iguais: desempata por prazo mais próximo; edital vencido sai do feed |
| Prazos | Janela de alerta de 48 h | Prazo em 49 h: sem alerta; em 47 h: alerta; prazo já passou: sem alerta |
| Certidões | Status de validade | Vence em 30 dias: "perto de vencer"; vence hoje: "vence hoje"; vencida: "vencida"; sem data: "pendente" |
| Duplicação | Identificar o mesmo edital vindo duas vezes | Mesmo número de controle PNCP gera uma única chave |
| Validação&#32; | Rejeitar entradas inválidas | Faixa de valor mínima maior que a máxima é rejeitada; UF inexistente é rejeitada |
| Feedback | Efeito das marcações relevante / irrelevante | Edital marcado irrelevante desce no feed; marcação não altera a nota de outro usuário |

### 4.2 Integração (\~20%) — "as peças conversam?"

Testes que sobem partes reais do sistema, mas com dependências externas simuladas. Também em Vitest.

- **Rotas Fastify com emuladores.** Usar `app.inject()` do Fastify (Simulando requisições HTTP e as rotas da API, Uso em memória)  contra o Firestore do Firebase Emulator Suite (Simulando Banco de dados na nuvem).  Zod para validação
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
| Local | Máquina local | Firebase Emulator Suite | Fixtures gravadas | Dev, a cada alteração |
| CI | GitHub Actions | Firebase Emulator Suite | Fixtures gravadas | Todo pull request |
| Staging | Cloud Run + Firebase (projeto separado) | Firestore de staging | PNCP real, 1 vez ao dia | Smoke test após deploy e testes manuais |
| Produção | Cloud Run + Firebase | Firestore de produção | PNCP real | Usuários; só smoke test de saúde |

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
| ID do caso de teste | Rastrear o teste ao longo do sprint e vincular a relatórios de bugs | `CT-<MÓDULO>-<NNN>`, ex.: `CT-SCORE-001`. Módulos: PERFIL, ING, NORM, SCORE, FEED, PRAZO, CERT, ALERTA, AUTH |
| Descrição do teste | Resumo simples do que está sendo testado | Uma frase com o comportamento: "Nota é 0 quando nenhum critério do perfil é atendido" |
| Pré-requisitos | Tudo que precisa estar pronto antes de começar | Dados de teste, contas de usuário, configurações do sistema (ex.: emulador com seed, perfil "PME de TI no DF") |
| Etapas do teste | Ações que o testador executa | Passos numerados e específicos, que qualquer testador repita igual |
| Resultados esperados | O que deve acontecer em cada etapa | Específico e mensurável: "nota = 0", "status HTTP 403", "alerta exibido" |
| Resultados reais | O que de fato aconteceu na execução | Preenchido na execução; em teste automatizado, o relatório do CI |
| Status | Resultado da execução | Aprovado, Reprovado ou Bloqueado (algum obstáculo impediu a execução) |

No código, o ID vai no nome do teste, para o relatório do CI apontar direto para o caso: `it('CT-PRAZO-002: gera alerta 47 h antes do prazo', …)`.

### Exemplos preenchidos

**CT-SCORE-001 — Nota é 0 quando nenhum critério é atendido** (unitário)

- **Pré-requisitos:** perfil "PME de TI no DF" (termos: software, licença; UF: DF; modalidade: pregão eletrônico; faixa R$ 10 mil a R$ 500 mil); fixture de edital de limpeza em SP, concorrência, R$ 2 milhões.
- **Etapas:** 1. Normalizar o edital. 2. Calcular a nota com o perfil.
- **Resultados esperados:** 1. Edital normalizado sem erro. 2. Nota = 0.
- **Resultados reais:** preenchido na execução.
- **Status:** preenchido na execução.

**CT-PRAZO-002 — Alerta é gerado dentro da janela de 48 h** (unitário)

- **Pré-requisitos:** relógio fixado em 13/10/2026 10:00 (Brasília); edital acompanhado com prazo em 15/10/2026 09:00.
- **Etapas:** 1. Executar a função de verificação de prazos.
- **Resultados esperados:** 1. Um alerta do tipo "prazo" para o edital, faltando 47 h.
- **Resultados reais / Status:** preenchidos na execução.

**CT-ING-003 — Ingestão repetida não duplica editais** (integração)

- **Pré-requisitos:** emulador do Firestore limpo; fixture com 25 editais do PNCP.
- **Etapas:** 1. Rodar a ingestão. 2. Contar editais. 3. Rodar a ingestão de novo com a mesma fixture. 4. Contar editais.
- **Resultados esperados:** 2. 25 editais. 4. Continua 25 editais.
- **Resultados reais / Status:** preenchidos na execução.

**CT-AUTH-001 — Empresa não acessa certidões de outra empresa** (integração, regras Firestore)

- **Pré-requisitos:** emulador com empresas A e B, cada uma com 1 certidão; usuário autenticado da empresa A.
- **Etapas:** 1. Ler as certidões da empresa A. 2. Ler as certidões da empresa B.
- **Resultados esperados:** 1. Leitura permitida. 2. Leitura negada.
- **Resultados reais / Status:** preenchidos na execução.

**CT-E2E-002 — Feed mostra editais em ordem de nota** (E2E)

- **Pré-requisitos:** front e back locais com seed; usuário logado com perfil "PME de TI no DF".
- **Etapas:** 1. Abrir o feed. 2. Ler as notas  -dos 10 primeiros cards. 3. Filtrar por modalidade "pregão eletrônico".
- **Resultados esperados:** 1. Feed carrega em menos de 3 s. 2. Notas em ordem decrescente. 3. Só aparecem pregões, ainda em ordem decrescente.
- **Resultados reais / Status:** preenchidos na execução.

## 7. Métrica de relevância do feed: Precisão

O teste de qualidade do produto é: dos 10 primeiros editais do feed, quantos o usuário considera úteis. Essa métrica, chamada Precisão, é o que mostramos na demo.

```latex
\text{Precisão} = \frac{\text{editais marcados como relevantes entre os 10 primeiros}}{10}
```

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

**Deploy em produção:** manual (aprovação no GitHub Actions), só depois do smoke test de staging passar.

### Critérios para aprovar um pull request

- Todos os passos acima verdes.
- Cobertura de linhas ≥ 80% nos módulos de normalização, score, prazos e certidões (cobertura geral não é meta, é sinal).
- Precisão não caiu em relação à `main` (ou a queda foi justificada no PR).
- Todo bug corrigido vem com um teste que falhava antes da correção.
- Pelo menos uma revisão de outra pessoa do time.

## 9. Critérios de entrada e saída, bugs&#32;

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

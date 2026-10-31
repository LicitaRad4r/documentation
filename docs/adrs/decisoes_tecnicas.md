# Licita Radar - Decisões Técnicas (ADRs)

**Tech Lead:** Júlia Neiva Lima  
**Projeto:** LicitaRadar  
**Versão:** 0.2 (31/10/2026)  

---

## Histórico de versões

| Versão | Data | Alteração |
| :--- | :--- | :--- |
| 0.1 | 17/10/2026 | Versão inicial, com orçamento de US$ 50 em créditos do Google Cloud. |
| 0.2 | 31/10/2026 | Fim do voucher de US$ 50. O projeto passa a ter **teto de custo de US$ 0**. Revisados os ADRs 0001, 0004, 0005, 0006, 0007, 0008 e 0010; ajustes menores nos ADRs 0003, 0009 e 0011. O ADR-0002 não mudou. |

## Como ler este documento
Cada ADR registra uma decisão técnica do LicitaRadar em três partes: contexto, decisão e consequências. Juntos, eles respondem como vamos construir e quais são as regras do time.

| ADR | Título |
| :--- | :--- |
| **0001** | Plataforma: stack 100% gratuita (Firebase Spark, Neon e Render) |
| **0002** | Backend em Node.js, TypeScript, Fastify e Zod |
| **0003** | Frontend em SPA (React e Vite) separado da API REST |
| **0004** | API em contêiner no Render (plano gratuito) |
| **0005** | Persistência com PostgreSQL (Neon Free) e Firestore (plano Spark) |
| **0006** | Migrations do PostgreSQL com Drizzle ORM e drizzle-kit |
| **0007** | Ingestão do PNCP idempotente, resiliente e agendada pelo GitHub Actions |
| **0008** | Score de Aderência determinístico e configurável |
| **0009** | Estratégia de testes em camadas (Vitest e Playwright) |
| **0010** | CI/CD com GitHub Actions |
| **0011** | Docs-as-Code com MkDocs Material |

---

## ADR-0001: Plataforma: stack 100% gratuita (Firebase Spark, Neon e Render)
- **Contexto:** O LicitaRadar precisa ir ao ar em um ciclo de aproximadamente 2 meses, agora **sem nenhum orçamento de nuvem (teto de US$ 0)**. Frontend, API, banco, agendamento e segredos precisam de hospedagem com pouca operação manual, e o time precisa rodar tudo localmente sem custo (RNF12). Os produtos que o ADR anterior usava (Cloud Run, Cloud SQL, Secret Manager e Cloud Scheduler) exigem o plano Blaze do Firebase/Google Cloud, que pede cartão de crédito e não tem limite rígido de gasto. O plano Spark, ao contrário, não gera cobrança: ao esgotar a quota de um produto, ele para de responder até a renovação.
- **Decisão:** Adotar somente serviços com plano gratuito permanente e sem cartão:
  * Firebase Hosting (frontend) e Firestore (dados por empresa), no **plano Spark**;
  * **Neon Free** (PostgreSQL gerenciado);
  * **Render Free** (API em contêiner), ver ADR-0004;
  * **GitHub Actions** para CI/CD e para tarefas agendadas (ingestão e recálculo), ver ADR-0007 e ADR-0010;
  * **GitHub Pages** para a documentação (ADR-0011).

  Segredos ficam em GitHub Actions Secrets (CI e deploy) e em variáveis de ambiente do Render (runtime); nunca no repositório. Nenhum projeto Google Cloud ou Firebase será migrado para o plano Blaze, e nenhum serviço pago será contratado sem decisão registrada em novo ADR. Em vez de alertas de orçamento, acompanhar semanalmente o consumo das quotas gratuitas (painéis do Firebase, Neon e Render), com meta de ficar abaixo de 60% de cada quota.
- **Consequências:**
  * *Positivas:* Custo zero e sem risco de cobrança surpresa; o Spark simplesmente bloqueia o excesso.
  * *Negativas ou trade-offs aceitos:* Quotas pequenas (ver ADR-0004, 0005 e 0007), partida a frio da API e do banco, e quatro fornecedores em vez de um, com painéis separados.
  * *Neutras ou a observar:* Os limites e as políticas dos planos gratuitos podem mudar; reconferir as páginas oficiais de preço no início de cada Checkpoint.
- **Impacto operacional:** O Tech Lead cria os projetos (Firebase em Spark, Neon e Render) e as contas de serviço no Checkpoint 1. Todos os deploys passam pelo GitHub Actions (ADR-0010). A conta de serviço do deploy do Firebase Hosting tem somente o papel necessário para publicar, sua chave fica em GitHub Secrets e é rotacionada a cada Checkpoint.
- **Referências:** Relacionados: ADR-0004, ADR-0005, ADR-0007, ADR-0010

---

## ADR-0002: Backend em Node.js, TypeScript, Fastify e Zod
- **Contexto:** O time é full stack e já usa TypeScript no frontend. O backend precisa de baixo overhead, validação rigorosa dos payloads externos (PNCP e formulários) e geração do contrato OpenAPI, que é a fonte da verdade entre frontend e backend.
- **Decisão:** Implementar a API em Node.js com TypeScript e Fastify (RNF03 e RNF08), com validação de entrada em Zod (RNF05). A estrutura segue as camadas controllers (rotas), services (regras de negócio) e repository (acesso ao banco). O OpenAPI é gerado a partir dos schemas Zod.
- **Consequências:**
  * *Positivas:* Uma linguagem em todo o código, com tipos compartilhados e menos erros de integração.
  * *Negativas ou trade-offs aceitos:* O ecossistema de plugins do Fastify é menor que o do Express.
  * *Neutras ou a observar:* Padronizar lint e tsconfig no frontend e no backend.
- **Impacto operacional:** O CI roda ESLint e tsc-noEmit em cada pull request (ADR-0010).
- **Referências:** Relacionados: ADR-0003, ADR-0004, ADR-0006

---

## ADR-0003: Frontend em SPA (React e Vite) separado da API REST
- **Contexto:** O Analista usa a plataforma todos os dias e precisa de filtros rápidos, com resposta em menos de 2 segundos. O frontend deve ser responsivo (RNF07) e distribuído com alta disponibilidade (RNF02). Toda a aplicação fica atrás de login. O Firebase Hosting no plano Spark tem quotas diárias de armazenamento e transferência.
- **Decisão:** Construir o frontend como SPA em React com Vite, TypeScript e Tailwind CSS, hospedada no Firebase Hosting (plano Spark) e separada da API REST. A comunicação passa por camadas services/ HTTP tipadas. A filtragem e a paginação do feed ocorrem no servidor. O frontend trata a partida a frio da API (ADR-0004) com estado de carregamento explícito e nova tentativa automática na primeira requisição.
- **Consequências:**
  * *Positivas:* Deploys independentes e contrato OpenAPI claro entre as partes.
  * *Negativas ou trade-offs aceitos:* Exige configurar CORS entre o frontend e a API. A quota diária de transferência do Hosting é pequena, então o bundle precisa ser enxuto (code splitting, compressão e cache longo para arquivos com hash).
  * *Neutras ou a observar:* Meta de Lighthouse de 90 ou mais no Checkpoint 4; acompanhar a transferência diária do Hosting.
- **Impacto operacional:** O deploy do frontend usa a Firebase CLI, orquestrada pelo GitHub Actions.
- **Referências:** Relacionados: ADR-0001, ADR-0002, ADR-0004, ADR-0010

---

## ADR-0004: API em contêiner no Render (plano gratuito)
- **Contexto:** O tráfego do Licita Radar é irregular, e a ingestão e o recálculo diário não precisam passar pela API (ADR-0007). O Cloud Run foi descartado porque exige o plano Blaze (cartão e sem limite rígido de gasto). O Render oferece web service gratuito, sem cartão, que roda contêiner Docker, mas fica em repouso após 15 minutos sem requisições e leva cerca de 1 minuto para voltar. As 750 horas gratuitas por mês bastam para um serviço, pois o serviço em repouso não consome horas. O RNF01 (escalabilidade automática) e o RNF02 (disponibilidade) não são plenamente atendidos nesse plano.
- **Decisão:** Executar a API Fastify em contêiner Docker no Render Free, com um único serviço de produção. A API é sem estado (o Render pode reiniciá-la a qualquer momento), com logs em JSON e as rotas `/healthz` e `/readyz`. Não usar pings periódicos para manter a API acordada, para não consumir as 750 horas como "keep-alive"; em vez disso, aquecer a API manualmente antes de demonstrações e testes E2E. O staging é um segundo serviço Render Free ligado a uma branch do Neon (ADR-0005) e a um segundo projeto Firebase Spark; só consome horas enquanto está acordado.
- **Consequências:**
  * *Positivas:* Custo zero, deploy simples por imagem de contêiner e integração direta com o GitHub.
  * *Negativas ou trade-offs aceitos:* A primeira requisição após inatividade pode levar cerca de 1 minuto, o que viola a meta de 2 segundos nesse caso; os 512 MB de RAM e a CPU reduzida limitam o desempenho; o RNF01 fica atendido só em parte (sem escala horizontal).
  * *Neutras ou a observar:* Usar a string de conexão com pool do Neon e um limite pequeno de conexões no Fastify. Se o Render suspender o serviço por excesso de horas ou tráfego, a alternativa é migrar o contêiner para outro host gratuito, sem mudar o código. Registrar no documento de requisitos a exceção de latência na partida a frio.
- **Impacto operacional:** Deploy automatizado pelo GitHub Actions, que aciona o deploy do Render (deploy hook) após o CI e a aprovação. Segredos como variáveis de ambiente do Render. Rollback ensaiado no Checkpoint 4 reimplantando a versão anterior pelo painel do Render.
- **Referências:** Relacionados: ADR-0001, ADR-0002, ADR-0005, ADR-0010

---

## ADR-0005: Persistência com PostgreSQL (Neon Free) e Firestore (plano Spark)
- **Contexto:** O modelo tem dados relacionais (Organizações, Usuários, Editais e Certidões) e dados de leitura intensa por empresa (scores do feed e alertas). É preciso consistência, isolamento entre organizações (RNF06), ingestão idempotente de editais, identificados pelo número de controle do PNCP, e um ambiente local sem custo (RNF12). Sem orçamento, o Cloud SQL não é possível (não tem plano gratuito). Os planos gratuitos têm limites: o Neon Free dá 0,5 GB de armazenamento e 100 horas de computação por mês por projeto, com o banco entrando em repouso após alguns minutos ociosos; o Firestore no Spark dá 1 GiB, 50 mil leituras, 20 mil escritas e 20 mil exclusões por dia.
- **Decisão:** Manter dois bancos, ambos gratuitos. O Firestore é mantido (em vez de mover os scores para o PostgreSQL) porque o Checkpoint 1 o prevê e o emulador já está configurado no ambiente local.
  * **PostgreSQL no Neon Free** (contêiner Docker via docker-compose no ambiente local) guarda os dados relacionais: organizações, usuários, editais e certidões. Retenção: manter só editais com prazo aberto e os encerrados há até 90 dias; PDFs e textos completos não são armazenados. A API usa a conexão com pool; as migrations usam a conexão direta (ADR-0006).
  * **Firestore no Spark** guarda scores do feed e alertas por empresa, protegidos por Security Rules `deny-all` (acesso só pela API). Para caber nas quotas, guarda-se apenas os N melhores scores de cada empresa (N inicial = 200, configurável) e só se grava um documento quando a nota ou o detalhamento mudou. Orçamento de escritas: empresas × N ≤ 12.000 por dia (60% da quota).
  * Localmente e no CI, o Firebase Emulator Suite simula o Firestore. DBeaver ou pgAdmin servem para inspecionar o PostgreSQL.
- **Consequências:**
  * *Positivas:* Custo zero; integridade referencial e unicidade de editais no PostgreSQL; Emulator Suite e Security Rules para testar o Firestore sem custo; o Spark nunca gera cobrança.
  * *Negativas ou trade-offs aceitos:* Dois bancos aumentam a complexidade. Ao esgotar a quota do Firestore, as operações são recusadas até o dia seguinte. O Neon Free tem só 0,5 GB e janela de restauração curta (cerca de 6 horas). Exportações do Firestore exigem o plano Blaze e não estão disponíveis; como scores e alertas derivam de dados do PostgreSQL e do perfil, podem ser recalculados.
  * *Neutras ou a observar:* Acompanhar semanalmente o armazenamento e as horas de computação do Neon e as leituras e escritas do Firestore. Atualizar a documentação de Arquitetura, que cita só o PostgreSQL, para incluir o Firestore e a nova hospedagem. Definir como manter a consistência entre os dois bancos (o PostgreSQL é a fonte da verdade; o Firestore é reconstruível).
- **Impacto operacional:** O Firebase Emulator Suite já está configurado no docker-compose desde o Checkpoint 1, e a decisão de manter o Firestore foi justamente para aproveitar essa infraestrutura. Script de seed para o emulador e para o PostgreSQL local. Credenciais em GitHub Secrets e no Render. Backup: um `pg_dump` semanal feito por um workflow do GitHub Actions, guardado como artefato do workflow com retenção de 30 dias (os dados de organizações e usuários exigem cuidado com a LGPD: dump criptografado e acesso restrito).
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0006, ADR-0007, ADR-0008

---

## ADR-0006: Migrations do PostgreSQL com Drizzle ORM e drizzle-kit
- **Contexto:** O esquema do PostgreSQL precisa evoluir de forma versionada e reproduzível, no contêiner Docker local, no CI e no Neon. O time escreve tudo em TypeScript, valida dados com Zod e usa a camada repository no backend (ADR-0002). O projeto precisa de uma ferramenta de migrations que se encaixe nessa stack e funcione com um serviço que pode reiniciar a qualquer momento.
- **Decisão:** Usar o Drizzle ORM com o drizzle-kit. O esquema é declarado em TypeScript, as migrations são geradas em SQL com `drizzle-kit generate`, versionadas no repositório e aplicadas com `drizzle-kit migrate` no ambiente local, no CI e no deploy, antes de cada release da API. No deploy, a migração roda no GitHub Actions contra o Neon, usando a conexão direta (sem pool) guardada em GitHub Secrets, e só depois o deploy do Render é acionado. Os schemas Zod podem ser derivados das tabelas com o `drizzle-zod`. Regras do time: toda migration passa por revisão no pull request, uma migration já aplicada nunca é editada, e mudanças destrutivas são feitas em duas etapas.
- **Consequências:**
  * *Positivas:* Um único esquema em TypeScript como fonte dos tipos e da validação. Migrations em SQL legível, fáceis de revisar. A migration não depende de a API estar acordada.
  * *Negativas ou trade-offs aceitos:* O Drizzle é mais novo que Prisma e Knex, e o time precisa aprender uma nova ferramenta. A restauração do Neon Free é curta, então toda migration destrutiva exige um `pg_dump` antes.
  * *Neutras ou a observar:* O Firestore não usa migrations; mudanças na estrutura dos documentos são tratadas no código e nas Security Rules. A reversão de uma migration é feita por uma nova migration corretiva.
- **Impacto operacional:** O CI aplica as migrations em um PostgreSQL em contêiner antes dos testes de integração. O deploy executa a migração no Neon antes de publicar a nova versão no Render.
- **Referências:** Relacionados: ADR-0002, ADR-0005, ADR-0010

---

## ADR-0007: Ingestão do PNCP idempotente, resiliente e agendada pelo GitHub Actions
- **Contexto:** A fonte de dados do Licita Radar é a API do PNCP, que recebe um grande volume de editais por dia e, como serviço governamental, pode ficar instável, lenta ou devolver respostas mal formadas. Falhas aqui fazem o feed ficar poluído ou incompleto, riscos tratados como altos na Estratégia de Testes (R4 e R5). Sem orçamento, não há Cloud Scheduler nem tarefas em nuvem; e o volume diário precisa caber nos 0,5 GB do Neon.
- **Decisão:** Coletar os editais uma vez por dia, de forma automática e idempotente, por um **workflow agendado do GitHub Actions** (`schedule`, com disparo manual por `workflow_dispatch`) que executa o script de coleta diretamente contra o Neon e o Firestore, sem passar pela API e portanto sem depender dela estar acordada. O número de controle do PNCP é a chave única de cada edital, e a gravação é um `upsert`. Falhas (timeout, erro 500, JSON mal formado) geram nova tentativa com reprocessamento, e as falhas de cada coleta são registradas. Uma coleta interrompida retoma do ponto certo, sem duplicar. Cada payload é validado com Zod e normalizado: valor como `1.234,56` vira `1234.56`, datas viram ISO em UTC, a UF vai para maiúsculas e campos ausentes viram nulo. Para conter o volume, coletam-se apenas editais com recebimento de propostas aberto, só os campos necessários, e uma rotina diária remove editais antigos conforme a retenção do ADR-0005. A base de editais é global e guardada uma única vez, para todas as empresas.
- **Consequências:**
  * *Positivas:* Custo zero; reexecutar a coleta é seguro; o feed não tem editais repetidos. A resiliência a falhas do PNCP é testável com respostas gravadas.
  * *Negativas ou trade-offs aceitos:* Mais lógica de coleta (retry, retomada e registro de falhas) para manter. O agendamento do GitHub Actions pode atrasar minutos e, em repositórios públicos, é pausado após 60 dias sem atividade no repositório. Em repositórios privados, consome os minutos gratuitos mensais.
  * *Neutras ou a observar:* Medir o tamanho médio diário dos editais no Checkpoint 2 e ajustar a retenção ou os filtros de coleta para o banco nunca passar de 70% dos 0,5 GB.
- **Impacto operacional:** Os testes usam fixtures gravadas do PNCP (página normal, várias páginas, resposta vazia, erro 500, timeout e JSON mal formado) e verificam que rodar a ingestão duas vezes não muda o número de editais. O workflow de coleta falha de forma visível (notificação do GitHub) quando uma coleta não termina.
- **Referências:** Relacionados: ADR-0005, ADR-0008, ADR-0009, ADR-0010

---

## ADR-0008: Score de Aderência determinístico e configurável
- **Contexto:** O Score de Aderência é o núcleo do produto: ranqueia os editais do PNCP pelo perfil da empresa. O MVP não usa IA nem leitura de PDFs, o custo de nuvem é zero e a relevância dos 10 primeiros resultados será medida na demonstração.
- **Decisão:** Calcular a nota de 0 a 100 de forma determinística: a mesma entrada (perfil, edital e data de referência) sempre gera a mesma nota. O cálculo tem três passos: um funil eliminatório (termos-chave, categoria, prazo, modalidade e valor), a pontuação por critério (termos 40, UF 25, valor 15, prazo 10 e modalidade 10) e a reescala quando critérios opcionais não estão configurados. Pesos, faixas e limiares ficam em constantes de configuração. O detalhamento por critério e a data de referência são gravados junto com o score de cada empresa. O refinamento dinâmico do feed não recalcula o score. O cálculo roda em memória, no workflow de ingestão e na API (ao editar o perfil), e **persiste no Firestore só os N melhores resultados de cada empresa e só os que mudaram** (ADR-0005).
- **Consequências:**
  * *Positivas:* Resultado explicável, testável e repetível, sem custo de IA.
  * *Negativas ou trade-offs aceitos:* O recálculo diário multiplica empresas por editais ativos; a quota de 20 mil escritas por dia do Firestore limita empresas × N. Como a pontuação de prazo muda todo dia, muitas notas mudam diariamente, então o limite de 12.000 escritas por dia pode ser atingido com mais empresas ou um N maior.
  * *Neutras ou a observar:* Calibrar os pesos depois da demonstração, medindo a Precisão dos 10 primeiros resultados. Se a quota apertar, reduzir N ou gravar só mudanças de faixa de nota.
- **Impacto operacional:** O score é recalculado ao coletar novos editais, ao editar o perfil e uma vez por dia, porque os pontos de prazo mudam com o calendário. A tabela de casos de borda e o exemplo resolvido viram testes automatizados. Um teste confirma que o número de escritas por recálculo respeita o orçamento do ADR-0005.
- **Referências:** Relacionados: ADR-0005, ADR-0007, ADR-0009

---

## ADR-0009: Estratégia de testes em camadas (Vitest e Playwright)
- **Contexto:** Os riscos mais caros são perder um edital aderente, errar prazos ou a validade de certidões e vazar dados entre empresas. Os testes devem custar pouco, nunca tocar o PNCP real nem a produção e apontar o problema exato quando falharem.
- **Decisão:** Adotar quatro camadas: unitários (cerca de 65%), integração (cerca de 20%), API e contrato (cerca de 10%) e E2E (cerca de 5%). O Vitest cobre unitários e integração, e o Playwright cobre as cinco jornadas do MVP. Os testes rodam sem custo, com Firebase Emulator Suite (Firestore e Security Rules) e PostgreSQL em contêiner Docker, nunca contra o Neon ou o Firestore de produção, para não gastar suas quotas. O PNCP é simulado com fixtures gravadas, o relógio é fixado nos testes e o envio de alertas é coletado em memória. Todo caso de teste segue o padrão de sete componentes (ID, descrição, pré-requisitos, etapas, resultados esperados, resultados reais e status). A Precisão dos 10 primeiros resultados é medida com um conjunto de referência (golden set).
- **Consequências:**
  * *Positivas:* Feedback rápido, rastreabilidade entre requisito e teste e consumo zero das quotas de produção.
  * *Negativas ou trade-offs aceitos:* Manter fixtures e o golden set exige trabalho contínuo.
  * *Neutras ou a observar:* Os percentuais são referência; muitos E2E para regra de negócio indicam teste na camada errada. Em repositório privado, os minutos do GitHub Actions são limitados, então E2E e Precisão rodam só em pull requests para a main.
- **Impacto operacional:** Metas do Checkpoint 4: cobertura de domínio de pelo menos 80%, zero vulnerabilidades críticas e Lighthouse de pelo menos 90. Bugs são abertos como Issues, com severidade e ID do caso de teste.
- **Referências:** Relacionados: ADR-0005, ADR-0007, ADR-0008, ADR-0010

---

## ADR-0010: CI/CD com GitHub Actions
- **Contexto:** O ciclo é curto e a equipe adota ScrumXP, que exige integração contínua, revisão de código e testes automatizados. O código está no GitHub e o deploy vai para o Firebase Hosting e o Render. O GitHub Actions também executa as tarefas agendadas (ADR-0007), sem custo em repositórios públicos e com cota mensal de minutos em repositórios privados.
- **Decisão:** Usar o GitHub Actions para CI/CD (RNF09). Em cada pull request, em ordem e parando na primeira falha: lint e tipos, testes unitários com cobertura, integração, contrato da API, E2E e Precisão do score. Nenhum código entra na main sem revisão de pelo menos uma pessoa diferente do autor. O deploy em produção é manual (com aprovação), depois que o smoke test de staging passar: aplica as migrations no Neon (ADR-0006), aciona o deploy do Render e publica o frontend no Firebase Hosting. A aprovação usa GitHub Environments com revisores obrigatórios quando disponível; caso contrário, o deploy é um workflow disparado manualmente (`workflow_dispatch`) por quem não foi o autor da mudança. O CI também faz varredura de segredos.
- **Consequências:**
  * *Positivas:* Qualidade verificada a cada mudança e deploys reproduzíveis, sem custo.
  * *Negativas ou trade-offs aceitos:* O pipeline completo leva mais tempo; os passos rápidos rodam primeiro. A federação de identidade sem chaves não faz mais parte da decisão: a chave da conta de serviço de deploy fica em GitHub Secrets, restrita ao necessário e rotacionada (ADR-0001).
  * *Neutras ou a observar:* Acompanhar os minutos consumidos se o repositório for privado; tornar o repositório público (sem segredos e sem dados reais) é a alternativa mais simples.
- **Impacto operacional:** Critérios para aprovar um pull request: todos os passos verdes, cobertura de linhas de pelo menos 80% nos módulos de normalização, score, prazos e certidões, e Precisão sem queda em relação à main.
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0006, ADR-0007, ADR-0009

---

## ADR-0011: Docs-as-Code com MkDocs Material
- **Contexto:** A documentação (visão, requisitos, arquitetura, testes e ADRs) precisa ser atualizada continuamente e revisada junto com o código. A metodologia ScrumXP prefere documentação leve e essencial.
- **Decisão:** Manter a documentação em Markdown no repositório e publicá-la com MkDocs Material no GitHub Pages (gratuito para repositórios públicos). Mudanças de documentação entram por pull request, como o código, e os ADRs ficam em uma seção própria do menu lateral.
- **Consequências:**
  * *Positivas:* Histórico versionado e revisão por pares, sem custo.
  * *Negativas ou trade-offs aceitos:* Quem não usa Git precisa de apoio para contribuir. Em repositório privado, o GitHub Pages pode exigir plano pago; nesse caso, publicar a documentação no Firebase Hosting (outro site do mesmo projeto Spark).
  * *Neutras ou a observar:* Registrar as alterações no histórico de versões de cada documento.
- **Impacto operacional:** O site é gerado e publicado automaticamente pelo CI.
- **Referências:** Relacionados: ADR-0010
# Licita Radar - Decisões Técnicas (ADRs)

**Tech Lead:** Júlia Neiva Lima  
**Projeto:** LicitaRadar  
**Versão:** 0.4 (14/11/2026)  

---

## Histórico de versões

| Versão | Data | Alteração |
| :--- | :--- | :--- |
| 0.1 | 17/10/2026 | Versão inicial, com orçamento de US$ 50 em créditos do Google Cloud. |
| 0.2 | 31/10/2026 | Fim do voucher de US$ 50. O projeto passa a ter **teto de custo de US$ 0**. Revisados os ADRs 0001, 0004, 0005, 0006, 0007, 0008 e 0010; ajustes menores nos ADRs 0003, 0009 e 0011. O ADR-0002 não mudou. |
| 0.3 | 05/11/2026 | Resposta ao feedback do tutor (Checkpoints 1 e 2). Incluídos os campos **Status** e **Data** em todos os ADRs; criada a seção "Decisões substituídas" nos ADRs afetados pelo Custo Zero (o que mudou deixa de ser reescrito sem rastro); registradas as divergências entre ADR e código (persistência, `/health` x `/healthz`, rota de ingestão, contratos OpenAPI); acrescentado o requisito dos 10 casos tabelados do score (ADR-0008); ampliadas as regras de CI e de proteção de branch (ADR-0009 e ADR-0010); criado o ADR-0012 (escopo do MVP), em estado **Proposto**. Marcadores `[cite: …]` removidos. |
| 0.4 | 14/11/2026 | Fechamento das exceções do Custo Zero (issue [#68](https://github.com/LicitaRad4r/documentation/issues/68)). **Neon/PostgreSQL removidos como decisão vigente:** o ADR-0005 passa a Firestore-only (decisão de Júlia Neiva e Giovanni Dornelas, com o texto anterior preservado em "Decisões substituídas"), o ADR-0006 passa a **Substituído**, e os ADRs 0001, 0004, 0007, 0009 e 0010 foram ajustados. O ADR-0004 foi alinhado ao Plano Custo Zero (UptimeRobot no `/readyz` a cada 5 minutos e um único serviço Render). ADR-0007 conferido contra a rota e o workflow de ingestão. Criados os ADRs 0013 (UptimeRobot e logs do Render), 0014 (arquivos das certidões) e 0015 (voucher x Custo Zero e caminho de volta). Referências cruzadas com as issues #41, #54, #55 e #56. A aprovação do professor continua **pendente de registro**. |

## Como ler este documento
Cada ADR registra uma decisão técnica do LicitaRadar em quatro partes: status, contexto, decisão e consequências. Juntos, eles respondem como vamos construir e quais são as regras do time.

**Valores de Status:** *Proposto* (escrito, ainda sem aprovação registrada), *Aceito* (aprovado, com data e quem aprovou) e *Substituído* (trocado por outro ADR, mantido apenas para histórico).

| ADR | Título | Status |
| :--- | :--- | :--- |
| **0001** | Plataforma: stack 100% gratuita (Firebase Spark, Render e GitHub) | Proposto (aguardando aprovação do professor) |
| **0002** | Backend em Node.js, TypeScript, Fastify e Zod | Aceito |
| **0003** | Frontend em SPA (React e Vite) separado da API REST | Aceito |
| **0004** | API em contêiner no Render (plano gratuito) | Proposto (aguardando aprovação do professor) |
| **0005** | Persistência somente com Firestore (plano Spark) | Proposto (decisão da equipe registrada; aguardando aprovação do professor) |
| **0006** | Migrations do PostgreSQL com Drizzle ORM e drizzle-kit | Substituído (pelo ADR-0005) |
| **0007** | Ingestão do PNCP idempotente, resiliente e agendada pelo GitHub Actions | Proposto (com pendência de decisão) |
| **0008** | Score de Aderência determinístico e configurável | Aceito |
| **0009** | Estratégia de testes em camadas (Vitest e Playwright) | Aceito |
| **0010** | CI/CD com GitHub Actions | Proposto (aguardando aprovação do professor) |
| **0011** | Docs-as-Code com MkDocs Material | Aceito |
| **0012** | Escopo do MVP: acompanhar edital x recomendação e aprovação | Proposto |
| **0013** | Monitoramento com UptimeRobot e logs do Render | Proposto |
| **0014** | Armazenamento dos arquivos das certidões em produção | Proposto (com pendência de decisão) |
| **0015** | Voucher x Plano Custo Zero e caminho de volta | Proposto |

---

## ADR-0001: Plataforma: stack 100% gratuita (Firebase Spark, Render e GitHub)
- **Status:** Proposto. Aprovação do Custo Zero: *pendente de registro* (quem, data, link).
- **Data:** 17/10/2026 (v0.1); revisado em 31/10/2026 (v0.2), 05/11/2026 (v0.3) e 14/11/2026 (v0.4).
- **Contexto:** O LicitaRadar precisa ir ao ar em um ciclo de aproximadamente 2 meses, agora **sem nenhum orçamento de nuvem (teto de US$ 0)**. Frontend, API, banco, agendamento e segredos precisam de hospedagem com pouca operação manual, e o time precisa rodar tudo localmente sem custo (RNF12). Os produtos que a v0.1 usava (Cloud Run, Cloud SQL, Secret Manager e Cloud Scheduler) exigem o plano Blaze do Firebase/Google Cloud, que pede cartão de crédito e não tem limite rígido de gasto. O plano Spark, ao contrário, não gera cobrança: ao esgotar a quota de um produto, ele para de responder até a renovação.
- **Decisão:** Adotar somente serviços com plano gratuito permanente e sem cartão:
  * Firebase Hosting (frontend), Firebase Auth e Firestore (todos os dados, ver ADR-0005), no **plano Spark**;
  * **Render Free** (API em contêiner), ver ADR-0004;
  * **GitHub Actions** para CI/CD e para tarefas agendadas (ingestão e recálculo), ver ADR-0007 e ADR-0010;
  * **GitHub Pages** para a documentação (ADR-0011);
  * **UptimeRobot** (plano gratuito) para monitoramento da API, ver ADR-0013.

  Segredos ficam em GitHub Actions Secrets (CI e deploy) e em variáveis de ambiente do Render (runtime); nunca no repositório. Nenhum projeto Google Cloud ou Firebase será migrado para o plano Blaze, e nenhum serviço pago será contratado sem decisão registrada em novo ADR. Em vez de alertas de orçamento, acompanhar semanalmente o consumo das quotas gratuitas (painéis do Firebase, do Render e dos minutos do GitHub Actions), com meta de ficar abaixo de 60% de cada quota.
- **Consequências:**
  * *Positivas:* Custo zero e sem risco de cobrança surpresa; o Spark simplesmente bloqueia o excesso.
  * *Negativas ou trade-offs aceitos:* Quotas pequenas (ver ADR-0004, 0005 e 0007), partida a frio da API depois de reinícios ou implantações, e quatro fornecedores em vez de um (Firebase, Render, GitHub e UptimeRobot), com painéis separados. Produtos que exigem o plano Blaze, como o Cloud Storage for Firebase, não estão disponíveis (ver ADR-0014).
  * *Neutras ou a observar:* Os limites e as políticas dos planos gratuitos podem mudar; reconferir as páginas oficiais de preço no início de cada Checkpoint.
- **Impacto operacional:** O Tech Lead cria os projetos (Firebase em Spark e Render) e as contas de serviço no Checkpoint 1. Todos os deploys passam pelo GitHub Actions (ADR-0010). A conta de serviço do deploy do Firebase Hosting tem somente o papel necessário para publicar, sua chave fica em GitHub Secrets e é rotacionada a cada Checkpoint.
- **Evidências de custo (FinOps):** como não há faturamento, a prova do gasto ≤ US$ 5 é o print do Firebase em Spark, sem conta de cobrança (`docs/finops/evidencias/`). Registrar também os prints do Render Free, do GitHub Actions e do UptimeRobot quando existirem.
- **Decisões substituídas (v0.2 → v0.3 → v0.4):**
  * **v0.4 (14/11/2026):** o **Neon Free (PostgreSQL gerenciado)**, que a v0.2 listava como parte da stack, foi retirado. O código usa somente o Firestore, e a equipe decidiu manter um único banco (ver ADR-0005). O UptimeRobot foi acrescentado à stack (ver ADR-0013). O texto da v0.2 dizia: "Firebase Hosting (frontend) e Firestore (dados por empresa), no plano Spark; Neon Free (PostgreSQL gerenciado); Render Free (API em contêiner)…", com acompanhamento semanal das quotas do "Firebase, Neon e Render" e "quatro fornecedores" (Firebase, Neon, Render e GitHub).
  * **v0.1 → v0.2:**
    * Cloud Run → Render Free (ver ADR-0004).
    * Cloud SQL → Neon Free (ver ADR-0005; o Neon foi retirado na v0.4).
    * Secret Manager → GitHub Actions Secrets e variáveis de ambiente do Render.
    * Cloud Scheduler → workflow agendado do GitHub Actions (ver ADR-0007).
    * Alertas de orçamento de 50/80/100% → acompanhamento semanal das quotas (abaixo de 60%).
    * Motivo comum: fim do voucher de US$ 50 em 31/10/2026 e teto de custo de US$ 0 (ver ADR-0015).
- **Referências:** Relacionados: ADR-0004, ADR-0005, ADR-0007, ADR-0010, ADR-0013, ADR-0015. Issues: [#41](https://github.com/LicitaRad4r/documentation/issues/41), [#54](https://github.com/LicitaRad4r/documentation/issues/54), [#55](https://github.com/LicitaRad4r/documentation/issues/55), [#68](https://github.com/LicitaRad4r/documentation/issues/68).

---

## ADR-0002: Backend em Node.js, TypeScript, Fastify e Zod
- **Status:** Aceito (não alterado desde a v0.1). Registrar quem aprovou.
- **Data:** 17/10/2026. Pendência editorial adicionada em 05/11/2026.
- **Contexto:** O time é full stack e já usa TypeScript no frontend. O backend precisa de baixo overhead, validação rigorosa dos payloads externos (PNCP e formulários) e geração do contrato OpenAPI, que é a fonte da verdade entre frontend e backend.
- **Decisão:** Implementar a API em Node.js com TypeScript e Fastify (RNF03 e RNF08), com validação de entrada em Zod (RNF05). A estrutura segue as camadas controllers (rotas), services (regras de negócio) e repository (acesso ao banco). O OpenAPI é gerado a partir dos schemas Zod.
- **Consequências:**
  * *Positivas:* Uma linguagem em todo o código, com tipos compartilhados e menos erros de integração.
  * *Negativas ou trade-offs aceitos:* O ecossistema de plugins do Fastify é menor que o do Express.
  * *Neutras ou a observar:* Padronizar lint e tsconfig no frontend e no backend.
- **Pendência (contrato único):** hoje existem dois contratos: `documentation/docs/api/openapi.yaml` (v0.1.0, mais de 20 endpoints) e `LicitaRadar/server/openapi.yaml` (v0.3.0, 6 rotas sob `/v1`); o `server/README.md` ainda cita a "versão 0.2". **Regra proposta:** a fonte da verdade é o `server/openapi.yaml` (gerado do código); o arquivo da documentação passa a apontar para ele e deixa de ser editado à mão. O restante do v0.1.0 (convites, etapas, recomendação, decisão) fica marcado como *visão pós-MVP* (ver ADR-0012). Confirmar esta regra com o Tech Lead e o tutor.
- **Impacto operacional:** O CI roda ESLint e tsc-noEmit em cada pull request (ADR-0010).
- **Referências:** Relacionados: ADR-0003, ADR-0004, ADR-0006

---

## ADR-0003: Frontend em SPA (React e Vite) separado da API REST
- **Status:** Aceito (ajustes menores na v0.2). Registrar quem aprovou.
- **Data:** 17/10/2026; ajustado em 31/10/2026 e 05/11/2026.
- **Contexto:** O Analista usa a plataforma todos os dias e precisa de filtros rápidos, com resposta em menos de 2 segundos. O frontend deve ser responsivo (RNF07) e distribuído com alta disponibilidade (RNF02). Toda a aplicação fica atrás de login. O Firebase Hosting no plano Spark tem quotas diárias de armazenamento e transferência.
- **Decisão:** Construir o frontend como SPA em React com Vite, TypeScript e Tailwind CSS, hospedada no Firebase Hosting (plano Spark) e separada da API REST. A comunicação passa por camadas services/ HTTP tipadas. A filtragem e a paginação do feed ocorrem no servidor. O frontend trata a partida a frio da API (ADR-0004) com estado de carregamento explícito e nova tentativa automática na primeira requisição. As telas usam os estados padronizados *carregando*, *vazio* e *erro* (`components/estado/`).
- **Modo de execução local:** o `docker compose up` deve subir o frontend **ligado à API real** (`VITE_API_MOCK=false` por padrão). O mock (MSW) fica como opção explícita (`VITE_API_MOCK=true`), para desenvolvimento isolado do frontend. O login usa o Firebase Auth no emulador, para que a API real receba um token válido.
- **Consequências:**
  * *Positivas:* Deploys independentes e contrato OpenAPI claro entre as partes.
  * *Negativas ou trade-offs aceitos:* Exige configurar CORS entre o frontend e a API. A quota diária de transferência do Hosting é pequena, então o bundle precisa ser enxuto (code splitting, compressão e cache longo para arquivos com hash).
  * *Neutras ou a observar:* Meta de Lighthouse de 90 ou mais no Checkpoint 4; acompanhar a transferência diária do Hosting.
- **Impacto operacional:** O deploy do frontend usa a Firebase CLI, orquestrada pelo GitHub Actions.
- **Referências:** Relacionados: ADR-0001, ADR-0002, ADR-0004, ADR-0010. Issue: [#41](https://github.com/LicitaRad4r/documentation/issues/41) (pipeline do front-end no Firebase Hosting).

---

## ADR-0004: API em contêiner no Render (plano gratuito)
- **Status:** Proposto (aguardando registro da aprovação do Custo Zero).
- **Data:** 17/10/2026 (v0.1); reescrito em 31/10/2026 (v0.2); revisado em 05/11/2026 (v0.3) e 14/11/2026 (v0.4).
- **Contexto:** O tráfego do Licita Radar é irregular, e a ingestão e o recálculo diário não precisam passar pela API (ADR-0007). O Cloud Run foi descartado porque exige o plano Blaze (cartão e sem limite rígido de gasto). O Render oferece web service gratuito, sem cartão, que roda contêiner Docker, mas fica em repouso após 15 minutos sem requisições e leva cerca de 1 minuto para voltar. As 750 horas gratuitas por mês dão para **um** serviço ligado o mês inteiro (31 dias = 744 horas), mas não para dois. O Plano Custo Zero prevê que o UptimeRobot consulte o `/readyz` a cada 5 minutos (ADR-0013), o que mantém o serviço acordado. O RNF01 (escalabilidade automática) e o RNF02 (disponibilidade) não são plenamente atendidos nesse plano.
- **Decisão:** Executar a API Fastify em contêiner Docker no Render Free, com **um único serviço** (produção) e **sem serviço de staging**. A imagem de produção usa um Dockerfile próprio (build em dois estágios com `node:22-bookworm-slim`, `npm ci --omit=dev` e `npm start`, escutando em `0.0.0.0` na porta definida pela variável `PORT` do Render); o Dockerfile de `server/` continua sendo o do `docker compose` (modo dev). A API é sem estado (o Render pode reiniciá-la a qualquer momento), com logs estruturados em JSON (com `requestId` em cada requisição) e as rotas `/healthz` (a API está de pé) e `/readyz` (as dependências respondem).
  * **Monitoramento e keep-alive:** o UptimeRobot consulta `/readyz` a cada 5 minutos (ADR-0013). Como o intervalo é menor que os 15 minutos de inatividade do Render, o serviço não entra em repouso e a partida a frio deixa de aparecer no uso normal. O custo é aceito: o serviço consome cerca de 720 a 744 horas por mês das 750 gratuitas. Por isso o workspace do Render **não pode ter outro web service gratuito ligado**, e as horas consumidas são conferidas toda semana (ADR-0001). Se o monitor for pausado, aquecer a API manualmente antes de demonstrações e testes E2E.
  * **Pré-produção:** em vez de um segundo serviço Render, o smoke test antes do deploy roda a **imagem de produção em contêiner, localmente ou no CI, ligada aos emuladores do Firebase** (ADR-0010). O segundo projeto Firebase Spark deixa de ser necessário; se um ambiente de pré-produção do frontend for desejado, deve ser registrado em novo ADR.
- **Consequências:**
  * *Positivas:* Custo zero, deploy simples por imagem de contêiner e integração direta com o GitHub. Com o monitor ativo, a meta de 2 segundos vale também para a primeira requisição do dia.
  * *Negativas ou trade-offs aceitos:* O serviço consome quase todas as 750 horas gratuitas (sobra uma margem de poucas horas), então não cabe um segundo serviço. Após reinício, implantação ou queda do monitor, a primeira requisição ainda pode levar cerca de 1 minuto, o que viola a meta de 2 segundos nesse caso; os 512 MB de RAM e a CPU reduzida limitam o desempenho; o RNF01 fica atendido só em parte (sem escala horizontal). Sem staging, erros de configuração de produção só aparecem depois do deploy; o rollback (abaixo) é a mitigação.
  * *Neutras ou a observar:* A verificação do `/readyz` deve ser leve (por exemplo, a leitura de um único documento do Firestore): com chamadas a cada 5 minutos são cerca de 288 leituras por dia, menos de 1% das 50 mil leituras gratuitas do Firestore. Se o Render suspender o serviço por excesso de horas ou tráfego, a alternativa é parar o keep-alive (aceitando a partida a frio) ou migrar o contêiner para outro host gratuito, sem mudar o código. Registrar no documento de requisitos a exceção de latência na partida a frio.
- **Divergência a corrigir no código:** a API atual expõe `/health`, mas o Caderno e este ADR pedem `/healthz` e `/readyz`. Ajustar o código e o `openapi.yaml` (ADR-0002) para `/healthz` e `/readyz`, e atualizar o healthcheck do `docker-compose.yml`. O monitor do UptimeRobot só pode ser ligado depois que o `/readyz` existir.
- **Impacto operacional:** Deploy automatizado pelo GitHub Actions, que aciona o deploy do Render (deploy hook) após o CI e a aprovação. Segredos (incluindo as credenciais do Firestore para a API) como variáveis de ambiente do Render. Rollback ensaiado no Checkpoint 4 reimplantando a versão anterior pelo painel do Render.
- **Decisões substituídas:**
  * **v0.2 → v0.3 → v0.4 (14/11/2026), alinhamento ao Plano Custo Zero:** o texto anterior dizia: "Não usar pings periódicos para manter a API acordada, para não consumir as 750 horas como 'keep-alive'; em vez disso, aquecer a API manualmente antes de demonstrações e testes E2E. O staging é um segundo serviço Render Free ligado a uma branch do Neon (ADR-0005) e a um segundo projeto Firebase Spark; só consome horas enquanto está acordado." Também previa "usar a string de conexão com pool do Neon e um limite pequeno de conexões no Fastify". Substituído porque o Plano Custo Zero usa o UptimeRobot no `/readyz` a cada 5 minutos e um único serviço (750 h/mês), e porque o Neon foi retirado (ADR-0005). Com ping a cada 5 minutos o serviço não dorme, então um segundo serviço Render (staging) não caberia nas horas gratuitas.
  * **v0.1 → v0.2:** a API rodava no Cloud Run, com escala automática (RNF01 atendido). Substituído pelo Render Free por exigir o plano Blaze (cartão e sem limite rígido de gasto). Perda aceita: partida a frio de cerca de 1 minuto e ausência de escala horizontal.
- **Referências:** Relacionados: ADR-0001, ADR-0002, ADR-0005, ADR-0010, ADR-0013. Issues: [#54](https://github.com/LicitaRad4r/documentation/issues/54) (API no Render e projeto Firebase real) e [#55](https://github.com/LicitaRad4r/documentation/issues/55) (monitoramento e alertas).

---

## ADR-0005: Persistência somente com Firestore (plano Spark)
- **Status:** Proposto. Decisão técnica da equipe registrada (Opção A, Firestore-only); aprovação do professor para a troca de stack: *pendente de registro* (quem, data, link).
- **Decidido por:** Júlia Neiva Lima (Tech Lead) e Giovanni Dornelas (DevOps).
- **Data:** 17/10/2026 (v0.1); reescrito em 31/10/2026 (v0.2); revisado em 05/11/2026 (v0.3); decisão Firestore-only registrada em 14/11/2026 (v0.4).
- **Contexto:** O modelo tem dados de cadastro (Organizações, Usuários, Perfil, Certidões), a base global de editais do PNCP e dados de leitura intensa por empresa (scores do feed e alertas). É preciso isolamento entre organizações (RNF06), ingestão idempotente de editais, identificados pelo número de controle do PNCP, e um ambiente local sem custo (RNF12). Sem orçamento, o Cloud SQL não é possível (não tem plano gratuito). O código atual já usa **somente o Firestore** (certidões, perfil e seed golden), e o Firebase Emulator Suite já está configurado no ambiente local e no CI. O feedback do tutor (Checkpoints 1 e 2) aceitou que isso é razoável e mais barato para o MVP, mas pediu que ADR e código dissessem o mesmo. O Firestore no Spark dá 1 GiB de armazenamento, 50 mil leituras, 20 mil escritas e 20 mil exclusões por dia.
- **Decisão:** Usar **um único banco, o Firestore no plano Spark**, para todos os dados. O PostgreSQL (Neon), o Drizzle e as migrations foram adiados (ADR-0006 *Substituído*). Em detalhe:
  * **Base global de editais:** guardada uma única vez, para todas as empresas. O ID do documento é o **número de controle do PNCP**, e a gravação é um upsert idempotente (ADR-0007), o que garante a unicidade sem banco relacional. Retenção inicial mantida: editais com prazo aberto e os encerrados há até 90 dias; PDFs e textos completos não são armazenados. A retenção será revista depois de medir o tamanho médio diário dos editais (ADR-0007) contra a quota de 1 GiB, e a rotina de remoção respeita o limite de 20 mil exclusões por dia.
  * **Dados por empresa:** perfil, certidões, scores do feed e alertas, sempre com a empresa vindo do token (`empresaId`), nunca do corpo da requisição (RNF06).
  * **Acesso:** Security Rules `deny-all`; o navegador nunca lê o Firestore diretamente, e todo acesso passa pela API (Firebase Admin SDK).
  * **Scores:** guarda-se apenas os N melhores scores de cada empresa (N inicial = 200, configurável) e só se grava um documento quando a nota ou o detalhamento mudou. Orçamento de escritas dos scores: empresas × N ≤ 12.000 por dia (60% da quota de 20 mil).
  * **Integridade:** sem joins nem chaves estrangeiras; a consistência entre documentos relacionados é responsabilidade das camadas services e repository (ADR-0002), com transações do Firestore onde houver mais de um documento envolvido.
  * **Local e CI:** o Firebase Emulator Suite simula o Firestore e as Security Rules; o Emulator UI serve para inspecionar os dados. Script de seed para o emulador.
  * **Backup (proposta desta revisão, a confirmar com o Tech Lead):** exportações e backups gerenciados do Firestore exigem o plano Blaze e não estão disponíveis. Como agora o Firestore é a única fonte de dados, um workflow semanal do GitHub Actions lê com o Admin SDK somente o que **não é reconstruível** (organizações, usuários, perfis e certidões) e guarda um arquivo JSON criptografado como artefato do workflow, com retenção de 30 dias e acesso restrito (LGPD). Editais (reconstruíveis a partir do PNCP) e scores (recalculáveis a partir de editais e perfil) ficam fora do backup. A leitura do backup conta nas 50 mil leituras diárias.
- **Alternativas consideradas:**
  * **Firestore + PostgreSQL no Neon Free (Opção B, a decisão da v0.2):** descartada. Dois bancos aumentam a complexidade e o número de fornecedores; o código não tem nada em PostgreSQL; o Neon Free tem só 0,5 GB e restauração de cerca de 6 horas; o Drizzle e as migrations seriam mais uma ferramenta para aprender em um ciclo curto. O que se perde: integridade referencial e consultas relacionais.
  * **Somente PostgreSQL (Neon), movendo os scores para ele:** descartada, porque obrigaria a reescrever o que já existe e a abandonar o emulador do Firestore, já configurado.
  * **Somente Firestore (escolhida):** o menor caminho entre o código atual e o Custo Zero, com um painel de quotas a acompanhar em vez de dois.
- **Consequências:**
  * *Positivas:* Custo zero; um único banco, um único conjunto de quotas e nenhuma ferramenta nova; ADR e código passam a dizer o mesmo; Emulator Suite e Security Rules testáveis sem custo; o Spark nunca gera cobrança.
  * *Negativas ou trade-offs aceitos:* Sem consultas relacionais, os filtros do feed dependem de índices e de consultas simples do Firestore (e de filtragem no servidor). As quotas diárias são **compartilhadas por todo o sistema**: os 20 mil de escritas incluem scores (até 12.000), editais coletados, perfis e certidões, então o volume diário de editais precisa ser medido (ADR-0007) e, se necessário, N ou os filtros de coleta são reduzidos. Ao esgotar a quota, as operações são recusadas até o dia seguinte. Não há backup gerenciado nem exportação (ver Backup acima).
  * *Neutras ou a observar:* Acompanhar semanalmente o armazenamento e as leituras, escritas e exclusões do Firestore. Atualizar a documentação de Arquitetura, que ainda cita só o PostgreSQL, para descrever o Firestore e a nova hospedagem. Se o produto crescer além das quotas do Spark, reabrir esta decisão em novo ADR (ver caminho de volta no ADR-0015).
- **Impacto operacional:** O Firebase Emulator Suite já está configurado no docker-compose desde o Checkpoint 1. Credenciais da conta de serviço em GitHub Secrets (workflows de ingestão e backup) e nas variáveis de ambiente do Render (API). Os ADRs 0006 (migrations) e o trecho de migrations do ADR-0010 deixam de valer.
- **Decisões substituídas:**
  * **v0.2 → v0.4 (14/11/2026), PostgreSQL no Neon Free e Firestore:** o time escolheu a Opção A ("Firestore-only no MVP") da pendência aberta na v0.3. O texto anterior da Decisão dizia: "Manter dois bancos, ambos gratuitos. O Firestore é mantido (em vez de mover os scores para o PostgreSQL) porque o Checkpoint 1 o prevê e o emulador já está configurado no ambiente local. **PostgreSQL no Neon Free** (contêiner Docker via docker-compose no ambiente local) guarda os dados relacionais: organizações, usuários, editais e certidões. Retenção: manter só editais com prazo aberto e os encerrados há até 90 dias; PDFs e textos completos não são armazenados. A API usa a conexão com pool; as migrations usam a conexão direta (ADR-0006). **Firestore no Spark** guarda scores do feed e alertas por empresa, protegidos por Security Rules `deny-all` (acesso só pela API). Para caber nas quotas, guarda-se apenas os N melhores scores de cada empresa (N inicial = 200, configurável) e só se grava um documento quando a nota ou o detalhamento mudou. Orçamento de escritas: empresas × N ≤ 12.000 por dia (60% da quota). Localmente e no CI, o Firebase Emulator Suite simula o Firestore. DBeaver ou pgAdmin servem para inspecionar o PostgreSQL."
  * As consequências da v0.2 citavam: integridade referencial e unicidade de editais no PostgreSQL; o Neon Free com 0,5 GB, 100 horas de computação por mês, repouso após alguns minutos ociosos e restauração de cerca de 6 horas; scores e alertas recalculáveis a partir do PostgreSQL ("o PostgreSQL é a fonte da verdade; o Firestore é reconstruível"); e backup por `pg_dump` semanal em workflow do GitHub Actions, com retenção de 30 dias. Todos foram substituídos pelo texto acima (a fonte da verdade agora é o Firestore, e o backup passa a ler dele).
  * **v0.1 → v0.2:** o PostgreSQL rodava no Cloud SQL, com backup gerenciado. Substituído pelo Neon Free e por `pg_dump` semanal via GitHub Actions, porque o Cloud SQL não tem plano gratuito.
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0006 (substituído), ADR-0007, ADR-0008, ADR-0015. Issue: [#54](https://github.com/LicitaRad4r/documentation/issues/54) (projeto Firebase real).

---

## ADR-0006: Migrations do PostgreSQL com Drizzle ORM e drizzle-kit
- **Status:** **Substituído** pelo ADR-0005 (Firestore-only, Opção A, decidido por Júlia Neiva Lima e Giovanni Dornelas). Mantido apenas para histórico; **não aplicar** o que está descrito abaixo.
- **Data:** 17/10/2026 (v0.1); revisado em 31/10/2026 (v0.2) e 05/11/2026 (v0.3); marcado como Substituído em 14/11/2026 (v0.4).
- **Motivo da substituição:** o código usa somente o Firestore, e a equipe adiou o PostgreSQL (Neon). Sem banco relacional não há esquema nem migrations para versionar. O Firestore não usa migrations: mudanças na estrutura dos documentos são tratadas no código, nos testes e nas Security Rules. Se o PostgreSQL voltar, este ADR pode ser retomado em um novo ADR que o referencie.
- **Contexto:** O esquema do PostgreSQL precisa evoluir de forma versionada e reproduzível, no contêiner Docker local, no CI e no Neon. O time escreve tudo em TypeScript, valida dados com Zod e usa a camada repository no backend (ADR-0002). O projeto precisa de uma ferramenta de migrations que se encaixe nessa stack e funcione com um serviço que pode reiniciar a qualquer momento.
- **Decisão:** Usar o Drizzle ORM com o drizzle-kit. O esquema é declarado em TypeScript, as migrations são geradas em SQL com `drizzle-kit generate`, versionadas no repositório e aplicadas com `drizzle-kit migrate` no ambiente local, no CI e no deploy, antes de cada release da API. No deploy, a migração roda no GitHub Actions contra o Neon, usando a conexão direta (sem pool) guardada em GitHub Secrets, e só depois o deploy do Render é acionado. Os schemas Zod podem ser derivados das tabelas com o `drizzle-zod`. Regras do time: toda migration passa por revisão no pull request, uma migration já aplicada nunca é editada, e mudanças destrutivas são feitas em duas etapas.
- **Consequências:**
  * *Positivas:* Um único esquema em TypeScript como fonte dos tipos e da validação. Migrations em SQL legível, fáceis de revisar. A migration não depende de a API estar acordada.
  * *Negativas ou trade-offs aceitos:* O Drizzle é mais novo que Prisma e Knex, e o time precisa aprender uma nova ferramenta. A restauração do Neon Free é curta, então toda migration destrutiva exige um `pg_dump` antes.
  * *Neutras ou a observar:* O Firestore não usa migrations; mudanças na estrutura dos documentos são tratadas no código e nas Security Rules. A reversão de uma migration é feita por uma nova migration corretiva.
- **Impacto operacional:** O CI aplica as migrations em um PostgreSQL em contêiner antes dos testes de integração. O deploy executa a migração no Neon antes de publicar a nova versão no Render. (Nada disso é executado: ver o status acima.)
- **Decisões substituídas (v0.1 → v0.2):** o alvo das migrations era o Cloud SQL; passou a ser o Neon (conexão direta, sem pool), executado pelo GitHub Actions. Conferir o texto exato da v0.1 com `git log -p` neste arquivo antes de fechar este registro.
- **Referências:** Relacionados: ADR-0002, ADR-0005 (que o substitui), ADR-0010

---

## ADR-0007: Ingestão do PNCP idempotente, resiliente e agendada pelo GitHub Actions
- **Status:** Proposto, **com pendência de decisão** (forma de disparo da ingestão) e **pendência de evidência** (contrato real do PNCP).
- **Data:** 17/10/2026 (v0.1); revisado em 31/10/2026 (v0.2), 05/11/2026 (v0.3) e 14/11/2026 (v0.4).
- **Contexto:** A fonte de dados do Licita Radar é a API do PNCP, que recebe um grande volume de editais por dia e, como serviço governamental, pode ficar instável, lenta ou devolver respostas mal formadas. Falhas aqui fazem o feed ficar poluído ou incompleto, riscos tratados como altos na Estratégia de Testes (R4 e R5). Sem orçamento, não há Cloud Scheduler nem tarefas em nuvem; e o volume diário precisa caber na quota do Firestore no Spark (1 GiB de armazenamento e 20 mil escritas por dia, compartilhadas com o restante do sistema, ver ADR-0005).
- **Decisão:** Coletar os editais uma vez por dia, de forma automática e idempotente, por um **workflow agendado do GitHub Actions** (`schedule`, com disparo manual por `workflow_dispatch`) que executa o script de coleta diretamente contra o Firestore (credenciais da conta de serviço em GitHub Secrets), sem passar pela API e portanto sem depender dela estar acordada. O número de controle do PNCP é a chave única de cada edital (é o ID do documento no Firestore), e a gravação é um `upsert`. Falhas (timeout, erro 500, JSON mal formado) geram nova tentativa com reprocessamento, e as falhas de cada coleta são registradas. Uma coleta interrompida retoma do ponto certo (checkpoint), sem duplicar, com teto de documentos por execução. Cada payload é validado com Zod e normalizado: valor como `1.234,56` vira `1234.56`, datas viram ISO em UTC, a UF vai para maiúsculas e campos ausentes viram nulo. Para conter o volume, coletam-se apenas editais com recebimento de propostas aberto, só os campos necessários, e uma rotina diária remove editais antigos conforme a retenção do ADR-0005. A base de editais é global e guardada uma única vez, para todas as empresas.
- **Contrato do PNCP (evidência obrigatória):** antes de implementar o adapter, um script pequeno chama a API pública do PNCP, salva de 20 a 50 contratações reais (anonimizáveis) como fixtures e registra em `docs/pncp/contrato.md` os campos que existem de verdade (número de controle, objeto, UF, modalidade, valor, datas, paginação e limite de requisições). Esse documento é a camada anticorrupção do adapter. Até ele existir, o modelo de dados e a fixture golden são considerados *hipótese*, pois foram escritos a partir do que se esperava da API.
- **Conferência com o código (14/11/2026):** com base no feedback do tutor de 03/11/2026, a rota `POST /jobs/ingestao-pncp` **não existe** na API, e o workflow `ingestao-pncp.yml` a chama e está desligado até lá. Quem fechar esta pendência deve reconferir o estado atual no repositório `LicitaRadar` antes de registrar a decisão:
  ```bash
  git grep -n "jobs/ingestao-pncp"
  cat .github/workflows/ingestao-pncp.yml
  ```
- **Divergência a resolver (como a ingestão é disparada):** este ADR diz que o workflow executa o script direto contra o banco, sem passar pela API. Mas o workflow atual chama uma rota que não existe. Escolher e registrar (opção, quem decidiu e data):
  * **Opção A (script direto, como neste ADR):** ajustar o workflow para rodar o script; não existe rota nem segredo de rota; o workflow precisa das credenciais do Firestore em GitHub Secrets.
  * **Opção B (rota protegida):** criar `POST /jobs/ingestao-pncp` na API, protegida por segredo (documentar nome do segredo e rotação); a ingestão passa a depender da API acordada (com o UptimeRobot ativo isso deixa de ser um problema na prática, mas continua sendo uma dependência, ver ADR-0004).
  * A Opção A é coerente com o Custo Zero e com o restante deste ADR; a Opção B exige reescrever o parágrafo de decisão. **Decisão:** *pendente*.
- **Consequências:**
  * *Positivas:* Custo zero; reexecutar a coleta é seguro; o feed não tem editais repetidos. A resiliência a falhas do PNCP é testável com respostas gravadas.
  * *Negativas ou trade-offs aceitos:* Mais lógica de coleta (retry, retomada e registro de falhas) para manter. O agendamento do GitHub Actions pode atrasar minutos e, em repositórios públicos, é pausado após 60 dias sem atividade no repositório. Em repositórios privados, consome os minutos gratuitos mensais. Como o banco é o Firestore, cada edital novo ou alterado custa uma escrita da quota diária de 20 mil.
  * *Neutras ou a observar:* Medir o tamanho médio e o número diário de editais no Checkpoint 2 e ajustar a retenção ou os filtros de coleta para o armazenamento nunca passar de 70% da quota e para as escritas diárias totais (editais, scores e demais) ficarem dentro de 20 mil. **Estado em 03/11/2026:** a ingestão ainda não existe; o feed usa o seed golden de seis editais.
- **Impacto operacional:** Os testes usam fixtures gravadas do PNCP (página normal, várias páginas, resposta vazia, erro 500, timeout e JSON mal formado) e verificam que rodar a ingestão duas vezes não muda o número de editais (**duas execuções = 0 duplicados**). Também testar o feed com a fonte fora do ar: o feed mostra o último snapshot. O workflow de coleta falha de forma visível (notificação do GitHub) quando uma coleta não termina.
- **Decisões substituídas:**
  * **v0.2 → v0.4:** o contexto dizia que o volume diário precisava caber "nos 0,5 GB do Neon (ou na quota do Firestore, conforme a decisão do ADR-0005)", e o script gravava "diretamente contra o banco" (o Neon). Substituído pelo Firestore, com a retirada do Neon (ADR-0005).
  * **v0.1 → v0.2:** o agendamento era feito pelo Cloud Scheduler chamando a API. Substituído pelo workflow agendado do GitHub Actions, por exigir o plano Blaze.
- **Referências:** Relacionados: ADR-0005, ADR-0008, ADR-0009, ADR-0010. Issue: [#56](https://github.com/LicitaRad4r/documentation/issues/56) (ingestão do PNCP no GitHub Actions).

---

## ADR-0008: Score de Aderência determinístico e configurável
- **Status:** Aceito (fórmula v0). Registrar quem aprovou. Os pesos próprios (40/25/15/10/10) diferem da fórmula do Caderno (50/20/10/20), o que o Caderno permite desde que versionado em ADR, como aqui.
- **Data:** 17/10/2026; revisado em 31/10/2026 (v0.2) e 05/11/2026 (v0.3).
- **Contexto:** O Score de Aderência é o núcleo do produto: ranqueia os editais do PNCP pelo perfil da empresa. O MVP não usa IA nem leitura de PDFs, o custo de nuvem é zero e a relevância dos 10 primeiros resultados será medida na demonstração (precisão@10 com 2 perfis).
- **Decisão:** Calcular a nota de 0 a 100 (inteira) de forma determinística: a mesma entrada (perfil, edital e data de referência) sempre gera a mesma nota. O cálculo tem três passos: um funil eliminatório (termos-chave, categoria, prazo, modalidade e valor), a pontuação por critério (termos 40, UF 25, valor 15, prazo 10 e modalidade 10) e a reescala quando critérios opcionais não estão configurados. Pesos, faixas e limiares ficam em constantes de configuração. O detalhamento por critério e a data de referência são gravados junto com o score de cada empresa. O refinamento dinâmico do feed não recalcula o score. O cálculo é uma função pura (a data de referência entra por parâmetro), roda em memória, no workflow de ingestão e na API (ao editar o perfil), e **persiste no Firestore só os N melhores resultados de cada empresa e só os que mudaram** (ADR-0005).
- **Casos tabelados (critério de aceite do Caderno):** o score precisa ter pelo menos **10 casos de teste tabelados**, incluindo obrigatoriamente: (a) valor **igual ao limite** da faixa; (b) valor **igual ao limite + R$ 0,01**; (c) nota mínima 0 e máxima 100; (d) critério opcional ausente com reescala. A tabela fica em `docs/` e vira teste automatizado, com o ID do caso (CT-xx) no nome do teste.
- **Consequências:**
  * *Positivas:* Resultado explicável, testável e repetível, sem custo de IA.
  * *Negativas ou trade-offs aceitos:* O recálculo diário multiplica empresas por editais ativos; a quota de 20 mil escritas por dia do Firestore limita empresas × N. Como a pontuação de prazo muda todo dia, muitas notas mudam diariamente, então o limite de 12.000 escritas por dia pode ser atingido com mais empresas ou um N maior.
  * *Neutras ou a observar:* Calibrar os pesos depois da demonstração, medindo a Precisão dos 10 primeiros resultados. Se a quota apertar, reduzir N ou gravar só mudanças de faixa de nota.
- **Defensibilidade:** a hipótese de defensibilidade do produto (custo de mudança pelo histórico de decisões e pelo repositório de certidões) é validada na demonstração pela precisão@10 sobre o golden set de dois perfis. Esta métrica é a mesma citada na seção de defensibilidade da Visão e no ADR-0009.
- **Impacto operacional:** O score é recalculado ao coletar novos editais, ao editar o perfil e uma vez por dia, porque os pontos de prazo mudam com o calendário. A tabela de casos de borda e o exemplo resolvido viram testes automatizados. Um teste confirma que o número de escritas por recálculo respeita o orçamento do ADR-0005.
- **Referências:** Relacionados: ADR-0005, ADR-0007, ADR-0009

---

## ADR-0009: Estratégia de testes em camadas (Vitest e Playwright)
- **Status:** Aceito (ajustes menores na v0.2). Registrar quem aprovou.
- **Data:** 17/10/2026; ajustado em 31/10/2026, 05/11/2026 e 14/11/2026 (retirada do PostgreSQL).
- **Contexto:** Os riscos mais caros são perder um edital aderente, errar prazos ou a validade de certidões e vazar dados entre empresas. Os testes devem custar pouco, nunca tocar o PNCP real nem a produção e apontar o problema exato quando falharem.
- **Decisão:** Adotar quatro camadas: unitários (cerca de 65%), integração (cerca de 20%), API e contrato (cerca de 10%) e E2E (cerca de 5%). O Vitest cobre unitários e integração, e o Playwright cobre as cinco jornadas do MVP. Os testes rodam sem custo, com Firebase Emulator Suite (Firestore e Security Rules), nunca contra o Firestore de produção, para não gastar suas quotas. O PNCP é simulado com fixtures gravadas, o relógio é fixado nos testes e o envio de alertas é coletado em memória. Todo caso de teste segue o padrão de sete componentes (ID, descrição, pré-requisitos, etapas, resultados esperados, resultados reais e status). A Precisão dos 10 primeiros resultados é medida com um conjunto de referência (golden set).
- **Rastreabilidade (regra nova):** todo teste automatizado referencia o ID do caso (CT-xx) no nome ou no `describe`; a matriz de rastreabilidade só tem campo de resultado preenchido quando existe teste ligado. Caso planejado sem teste continua marcado como "não executado". O relatório de testes informa sempre: total por camada, passados, falhos, cobertura e bugs abertos (BUG-xxx).
- **Consequências:**
  * *Positivas:* Feedback rápido, rastreabilidade entre requisito e teste e consumo zero das quotas de produção.
  * *Negativas ou trade-offs aceitos:* Manter fixtures e o golden set exige trabalho contínuo.
  * *Neutras ou a observar:* Os percentuais são referência; muitos E2E para regra de negócio indicam teste na camada errada. Em repositório privado, os minutos do GitHub Actions são limitados, então E2E e Precisão rodam só em pull requests para a main.
- **Testes exigidos pelo Caderno e ainda inexistentes (03/11/2026):** idempotência da ingestão, fixture malformada, circuit breaker e feed com fonte fora do ar (dependem do ADR-0007).
- **Convenção de nomes:** arquivos de teste sem ponto no meio do nome (renomear `firestore-deny.all.test.ts` para `firestore-deny-all.test.ts` e atualizar a referência de CT-SEG-010).
- **Impacto operacional:** Metas do Checkpoint 4: cobertura de domínio de pelo menos 80%, zero vulnerabilidades críticas e Lighthouse de pelo menos 90. Bugs são abertos como Issues, com severidade e ID do caso de teste.
- **Decisões substituídas (v0.3 → v0.4):** o texto anterior listava, além do emulador, "PostgreSQL em contêiner Docker (se mantido, conforme o ADR-0005)" e "nunca contra o Neon ou o Firestore de produção". Com o ADR-0005 Firestore-only, o PostgreSQL deixou de fazer parte dos testes.
- **Referências:** Relacionados: ADR-0005, ADR-0007, ADR-0008, ADR-0010

---

## ADR-0010: CI/CD com GitHub Actions
- **Status:** Proposto (aguardando registro da aprovação do Custo Zero).
- **Data:** 17/10/2026 (v0.1); revisado em 31/10/2026 (v0.2), 05/11/2026 (v0.3) e 14/11/2026 (v0.4).
- **Contexto:** O ciclo é curto e a equipe adota ScrumXP, que exige integração contínua, revisão de código e testes automatizados. O código está no GitHub e o deploy vai para o Firebase Hosting e o Render. O GitHub Actions também executa as tarefas agendadas (ADR-0007), sem custo em repositórios públicos e com cota mensal de minutos em repositórios privados.
- **Decisão:** Usar o GitHub Actions para CI/CD (RNF09). Em cada pull request, em ordem e parando na primeira falha: lint e tipos, testes unitários com cobertura, integração, contrato da API, E2E e Precisão do score. Nenhum código entra na main sem revisão de pelo menos uma pessoa diferente do autor. O deploy em produção é manual (com aprovação), depois que o smoke test de pré-produção passar (a imagem de produção em contêiner, ligada aos emuladores do Firebase, ver ADR-0004): aciona o deploy do Render e publica o frontend no Firebase Hosting. A aprovação usa GitHub Environments com revisores obrigatórios quando disponível; caso contrário, o deploy é um workflow disparado manualmente (`workflow_dispatch`) por quem não foi o autor da mudança. O CI também faz varredura de segredos.
- **Cobertura mínima do CI (o que cada job roda):**
  * job **client:** lint, build, **vitest** e Playwright mínimo;
  * job **server:** lint, tsc, `test:unit` (unitários e contrato) e **`test:int`** (testes com o emulador, incluindo o deny-all);
  * gitleaks e `docker compose config`.
- **Proteção de branch (nos dois repositórios, `LicitaRadar` e `documentation`):** pull request obrigatório, pelo menos 1 aprovação de pessoa diferente do autor, **sem push direto na main**, **sem bypass para administradores**, e os checks acima marcados como **obrigatórios** para o merge. O PR do Tech Lead é revisado por outra pessoa do time. O repositório `documentation` também recebe `CODEOWNERS` e o template de PR. No repositório `documentation`, o check obrigatório inclui `mkdocs build --strict`.
- **Consequências:**
  * *Positivas:* Qualidade verificada a cada mudança e deploys reproduzíveis, sem custo.
  * *Negativas ou trade-offs aceitos:* O pipeline completo leva mais tempo; os passos rápidos rodam primeiro. A federação de identidade sem chaves não faz mais parte da decisão: a chave da conta de serviço de deploy fica em GitHub Secrets, restrita ao necessário e rotacionada (ADR-0001). Sem staging no Render, o smoke test de pré-produção é local e o rollback pelo painel do Render (ADR-0004) é a rede de segurança.
  * *Neutras ou a observar:* Acompanhar os minutos consumidos se o repositório for privado; tornar o repositório público (sem segredos e sem dados reais) é a alternativa mais simples.
- **Impacto operacional:** Critérios para aprovar um pull request: todos os passos verdes, cobertura de linhas de pelo menos 80% nos módulos de normalização, score, prazos e certidões, e Precisão sem queda em relação à main.
- **Decisões substituídas:**
  * **v0.3 → v0.4 (14/11/2026):** o texto anterior dizia que o deploy em produção acontecia "depois que o smoke test de staging passar: aplica as migrations no Neon (ADR-0006, se mantido), aciona o deploy do Render e publica o frontend no Firebase Hosting". Substituído: não há migrations (ADR-0005 e ADR-0006) nem serviço de staging no Render (ADR-0004).
  * **v0.1 → v0.2:** a autenticação do deploy no Google Cloud usava federação de identidade sem chaves; substituída por chave de conta de serviço do Firebase em GitHub Secrets, rotacionada a cada Checkpoint. O deploy para o Cloud Run foi substituído pelo deploy hook do Render.
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0006 (substituído), ADR-0007, ADR-0009. Issues: [#41](https://github.com/LicitaRad4r/documentation/issues/41) (pipeline do front-end), [#54](https://github.com/LicitaRad4r/documentation/issues/54) (API no Render e Firebase real) e [#56](https://github.com/LicitaRad4r/documentation/issues/56) (ingestão no GitHub Actions).

---

## ADR-0011: Docs-as-Code com MkDocs Material
- **Status:** Aceito (ajustes menores na v0.2). Registrar quem aprovou.
- **Data:** 17/10/2026; ajustado em 31/10/2026 e 05/11/2026.
- **Contexto:** A documentação (visão, requisitos, arquitetura, testes e ADRs) precisa ser atualizada continuamente e revisada junto com o código. A metodologia ScrumXP prefere documentação leve e essencial.
- **Decisão:** Manter a documentação em Markdown no repositório e publicá-la com MkDocs Material no GitHub Pages (gratuito para repositórios públicos). Mudanças de documentação entram por pull request, como o código (com a proteção de branch do ADR-0010), e os ADRs ficam em uma seção própria do menu lateral. **Cada documento tem uma única cópia:** a Definition of Done, por exemplo, hoje existe em `gestao_metodologia/` e em `requirements/.../Requisitos/`; manter uma e apontar a outra para ela.
- **Consequências:**
  * *Positivas:* Histórico versionado e revisão por pares, sem custo.
  * *Negativas ou trade-offs aceitos:* Quem não usa Git precisa de apoio para contribuir. Em repositório privado, o GitHub Pages pode exigir plano pago; nesse caso, publicar a documentação no Firebase Hosting (outro site do mesmo projeto Spark).
  * *Neutras ou a observar:* Registrar as alterações no histórico de versões de cada documento. Manter datadas as revisões do backlog (o que mudou e por quê).
- **Impacto operacional:** O site é gerado e publicado automaticamente pelo CI.
- **Referências:** Relacionados: ADR-0010

---

## ADR-0012: Escopo do MVP: acompanhar edital x recomendação e aprovação
- **Status:** **Proposto.** Precisa de decisão do PO e do Tech Lead e de **aprovação do tutor**. Não marcar como Aceito antes disso.
- **Data:** 05/11/2026.
- **Contexto:** O Caderno define cinco jornadas para o MVP: perfil, feed ranqueado, **acompanhar edital** (interessado → preparando → enviado → resultado), checklist de certidões e alertas. A documentação do projeto (US07, US11, US12 e US17 a US19) trocou o acompanhamento por um fluxo em que a Analista recomenda o edital e o Decisor aprova ou descarta, que o Caderno trata como *Could Have* ("marcar edital como relevante"). O feedback do tutor aceita mudanças de escopo, mas exige decisão registrada e aprovação; hoje a mudança aparece como fato consumado. O fluxo de aprovação (estado "Em Disputa") também não existe em nenhum lado do código.
- **Opções:**
  * **Opção 1 (voltar ao Caderno):** o MVP implementa o acompanhamento do edital com as quatro etapas; o fluxo de recomendação e aprovação vira visão pós-MVP.
  * **Opção 2 (manter o fluxo de recomendação e aprovação):** substitui o acompanhamento; exige aprovação do tutor e a atualização de jornadas, MoSCoW e OpenAPI.
- **Decisão:** *Pendente.* Preencher com a opção escolhida, quem decidiu, a data e o link da aprovação do tutor.
- **Consequências (válidas em qualquer opção):**
  * As jornadas documentadas (`jornadas.md`), a matriz MoSCoW, a Visão ("Fora do escopo") e o OpenAPI passam a marcar explicitamente o que é **MVP** e o que é **visão**.
  * Os critérios de aceite das histórias do MVP ganham número ou resultado exato (por exemplo: "primeira página com 20 itens e p95 ≤ 1 s", "nota inteira entre 0 e 100", "certidão a 30 dias = Atenção; a 31 dias = Em dia"), e nenhum critério é marcado como concluído antes de existir teste que o comprove.
  * O vocabulário de perfis é unificado em toda a documentação (Admin e Analista, como no Caderno, ou Decisor e Analista, conforme a opção); incluir o texto de LGPD.
- **Referências:** Relacionados: ADR-0002, ADR-0008, ADR-0009

---

## ADR-0013: Monitoramento com UptimeRobot e logs do Render
- **Status:** Proposto (aguardando registro da aprovação do Custo Zero).
- **Data:** 14/11/2026.
- **Contexto:** A v0.1 previa o Cloud Monitoring e o Cloud Logging, que exigem um projeto Google Cloud com faturamento (plano Blaze) e, portanto, estão fora do teto de custo de US$ 0 (ADR-0001). A API roda no Render Free (ADR-0004), que não traz alertas de disponibilidade próprios e entra em repouso depois de 15 minutos sem requisições. O time precisa saber, sem custo, se a API está de pé e conseguir investigar falhas pelos logs.
- **Decisão:**
  * **Disponibilidade:** o **UptimeRobot** (plano gratuito) faz uma verificação HTTP de `/readyz` a cada 5 minutos e envia alerta por e-mail à equipe quando a API fica indisponível. O `/readyz` só é monitorado depois de existir no código (divergência do ADR-0004).
  * **Logs:** a API escreve logs estruturados em JSON, com `requestId` em cada requisição, na saída padrão; o Render os coleta e os exibe em seu painel. Os logs de 401 e 403 não trazem dados pessoais (LGPD). O `requestId` também volta na resposta de erro, para o usuário informar ao suporte.
  * **Falhas de tarefas agendadas:** o workflow de ingestão falha de forma visível, com a notificação do próprio GitHub (ADR-0007).
  * **Quotas:** o acompanhamento semanal dos painéis do Firebase, do Render e do GitHub Actions continua conforme o ADR-0001 (meta abaixo de 60%).
- **Alternativas consideradas:**
  * **Cloud Monitoring e Cloud Logging:** descartadas, pois exigem o plano Blaze.
  * **Outro serviço gratuito de monitoramento e logs (por exemplo, Better Stack ou Grafana Cloud):** não adotado agora, por acrescentar um quinto fornecedor; pode ser reavaliado em novo ADR se a retenção de logs do Render se mostrar insuficiente. A confirmar nos planos oficiais.
  * **Sem monitoramento:** descartada, porque a queda da API passaria despercebida até alguém reclamar.
- **Consequências:**
  * *Positivas:* Custo zero, configuração simples e aviso por e-mail. O ping periódico também mantém a API acordada (ADR-0004).
  * *Negativas ou trade-offs aceitos:* A retenção de logs do Render Free é curta (conferir no painel), então incidentes antigos podem não ser investigáveis. O UptimeRobot só mede a disponibilidade do `/readyz` de fora; não há métricas de negócio nem rastreamento detalhado. Um novo fornecedor para acompanhar. A partida a frio logo após uma implantação pode gerar um alerta falso.
  * *Neutras ou a observar:* O ping consome horas do Render (cerca de 720 a 744 por mês, ver ADR-0004) e leituras do Firestore (cerca de 288 por dia se o `/readyz` ler um documento, ver ADR-0005). Os limites do plano gratuito do UptimeRobot (intervalo mínimo e número de monitores) devem ser reconferidos na página oficial no início de cada Checkpoint.
- **Impacto operacional:** O Giovanni Dornelas (DevOps) cria o monitor e a lista de e-mails de alerta, e guarda o print da configuração e de um alerta de teste em `docs/finops/evidencias/`. Os procedimentos de resposta a incidentes entram na documentação de operação (issue #55).
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0005, ADR-0007. Issues: [#54](https://github.com/LicitaRad4r/documentation/issues/54) e [#55](https://github.com/LicitaRad4r/documentation/issues/55) (monitoramento e alertas).

---

## ADR-0014: Armazenamento dos arquivos das certidões em produção
- **Status:** Proposto, **com pendência de decisão** (a decisão é tomada na issue "[CP3] Decidir onde ficam os arquivos das certidões" e deve ser copiada para cá).
- **Data:** 14/11/2026.
- **Contexto:** O checklist de certidões permite anexar o arquivo da certidão (o critério de aceite limita o upload a 5 MB). O Firestore guarda os metadados (tipo, emissão e validade), mas não é um bom lugar para arquivos: cada documento tem limite de 1 MiB, e o armazenamento total é de 1 GiB, compartilhado com todos os outros dados (ADR-0005). O produto natural do Firebase para arquivos, o **Cloud Storage for Firebase**, exige o plano Blaze, segundo a documentação oficial do Firebase: em projetos Spark, o acesso aos buckets deixa de funcionar (erros 402 e 403). Isso contraria o ADR-0001 (nenhum projeto migra para o Blaze). As certidões são dados de empresas, com isolamento obrigatório entre organizações (RNF06) e tratamento conforme a LGPD.
- **Critérios para a decisão:** custo US$ 0 e sem cartão (ADR-0001); arquivos de até 5 MB; acesso somente via API, com a empresa vinda do token (RNF06); eliminação dos dados conforme a política de LGPD da Visão (por exemplo, até 30 dias após o fim da assinatura); poucas peças novas para operar.
- **Alternativas consideradas** (os limites de cada plano gratuito devem ser confirmados nas páginas oficiais antes da decisão):
  * **A. Cloud Storage for Firebase:** integra-se bem ao projeto e às regras de segurança, mas exige o Blaze. Só seria possível com uma exceção registrada em novo ADR e aprovada pelo professor; **contraria o Custo Zero**.
  * **B. Serviço gratuito de armazenamento de objetos que não exige cartão (por exemplo, o Supabase Storage):** custo zero e URLs assinadas de curta duração, mas é um **novo fornecedor**, com cota pequena e políticas próprias (como a pausa de projetos inativos), a confirmar.
  * **C. Serviço de objetos com camada gratuita que exige meio de pagamento (por exemplo, o Cloudflare R2):** descartável pelo critério "sem cartão", salvo exceção aprovada.
  * **D. Guardar o arquivo dentro do Firestore (por exemplo, em pedaços):** inviável na prática para 5 MB e consome o 1 GiB gratuito rapidamente.
  * **E. Disco do contêiner no Render Free:** o plano gratuito não tem disco persistente (a confirmar); o arquivo se perderia a cada reinício. Descartada.
  * **F. Sem upload no MVP: só metadados e um link informado pela empresa (por exemplo, o Drive da própria empresa):** custo zero e nenhuma peça nova, mas muda o critério de aceite de upload e transfere a guarda do arquivo à empresa; exige decisão do PO.
- **Decisão:** *Pendente.* Preencher com a alternativa escolhida, quem decidiu, a data e o link da issue. A alternativa A só vale com exceção aprovada e novo ADR; a F exige ajuste do critério de aceite.
- **Consequências (a completar depois da decisão):**
  * *Positivas:* a definir.
  * *Negativas ou trade-offs aceitos:* a definir. Em qualquer opção externa ao Firebase, as chaves ficam em GitHub Secrets e nas variáveis do Render (ADR-0001), e o navegador nunca recebe acesso direto ao bucket: o download passa por URL assinada de curta duração emitida pela API depois de validar a empresa.
  * *Neutras ou a observar:* Reconferir os limites oficiais no início de cada Checkpoint (ADR-0001). Definir a rotina de remoção dos arquivos quando a empresa encerrar a assinatura.
- **Impacto operacional:** Depois da decisão: atualizar o contrato OpenAPI do upload e do download, os testes de isolamento de tenant (CT-SEG) para incluir os arquivos e a documentação de LGPD.
- **Referências:** Relacionados: ADR-0001, ADR-0005. Issues: [#68](https://github.com/LicitaRad4r/documentation/issues/68) e "[CP3] Decidir onde ficam os arquivos das certidões".

---

## ADR-0015: Voucher x Plano Custo Zero e caminho de volta
- **Status:** Proposto (aguardando registro da aprovação do Custo Zero).
- **Data:** 14/11/2026. Registro formal de uma decisão que vale desde 31/10/2026 (v0.2); o prazo original para decidir era 01/11/2026.
- **Contexto:** A v0.1 (17/10/2026) foi escrita com US$ 50 em créditos (voucher) do Google Cloud, que sustentavam o Cloud Run, o Cloud SQL, o Secret Manager e o Cloud Scheduler. O voucher terminou em 31/10/2026, e o Caderno exige gasto de no máximo US$ 5 por projeto. Era preciso decidir entre depender de um novo voucher ou adotar o Plano Custo Zero. O Plano Custo Zero exige um ADR e a aprovação do professor para cada exceção ao Caderno, e o Definition of Done exige um ADR para qualquer recurso novo na nuvem.
- **Decisão:** Adotar o **Plano Custo Zero** (teto de custo de US$ 0), como já descrito nos ADRs 0001, 0004, 0005, 0007, 0010 e 0013: Firebase em Spark, Render Free, GitHub Actions e UptimeRobot, sem plano Blaze e sem cartão. A decisão não depende de um novo voucher chegar.
- **Alternativas consideradas:**
  * **Continuar na v0.1 à espera de um novo voucher:** descartada, porque deixaria o CP3 (nuvem, deploy e observabilidade) sem base certa de custo.
  * **Plano Blaze com alertas de orçamento (50/80/100%):** descartada. Pede cartão de crédito e não tem limite rígido de gasto; o orçamento é só um alerta, não um bloqueio.
  * **Plano Custo Zero (escolhida):** o Spark não gera cobrança, e as quotas são conhecidas e acompanhadas toda semana (ADR-0001).
- **Consequências:**
  * *Positivas:* Sem risco de cobrança; a prova de custo é o print do Firebase em Spark (`docs/finops/evidencias/`).
  * *Negativas ou trade-offs aceitos:* As perdas listadas nos ADRs 0001 e 0004 (quotas pequenas, sem escala horizontal, sem o Cloud Monitoring e o Cloud Logging, sem Cloud Storage for Firebase, ver ADR-0014).
- **Caminho de volta (se o voucher chegar):**
  1. **Gatilho:** confirmação por escrito do professor (e-mail ou mensagem) com o valor e a validade dos novos créditos. Sem isso, nada muda.
  2. **Texto da v0.1:** recuperar pelo histórico do Git (`git log -p -- docs/architecture/decisoes_tecnicas.md`). Para facilitar, criar uma tag no commit da v0.1 (por exemplo, `adr-v0.1`).
  3. **Novo ADR, sem apagar os atuais:** escrever um ADR que *substitui parcialmente* os ADRs 0001, 0004, 0007 e 0010 (Cloud Run no lugar do Render, Cloud Scheduler no lugar do workflow agendado, Secret Manager no lugar dos segredos do GitHub e do Render, alertas de orçamento de 50/80/100% no lugar do acompanhamento semanal), marcando-os como *Substituído* e preservando o texto.
  4. **Banco de dados:** o ADR-0005 (Firestore-only) **não** é revertido automaticamente. O voucher não faz o PostgreSQL voltar; isso exigiria uma decisão própria, que reabriria o ADR-0006.
  5. **Evidências:** o plano Blaze exige alertas de orçamento configurados e o print do painel de faturamento com gasto ≤ US$ 5 (`docs/finops/evidencias/`). O ADR-0014 também pode ser reaberto, já que o Cloud Storage for Firebase passa a ficar disponível.
  6. **Aprovação:** o novo ADR só passa a *Aceito* com a aprovação do professor registrada.
- **Impacto operacional:** Nenhuma ação enquanto o voucher não chegar. O Tech Lead guarda o link da aprovação do professor para o Custo Zero neste ADR e nos ADRs 0001, 0004, 0005, 0007 e 0010.
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0005, ADR-0007, ADR-0010, ADR-0013, ADR-0014. Issue: [#68](https://github.com/LicitaRad4r/documentation/issues/68).
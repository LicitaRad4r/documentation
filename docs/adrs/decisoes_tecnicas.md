# Licita Radar - Decisões Técnicas (ADRs)

**Tech Lead:** Júlia Neiva Lima  
**Projeto:** LicitaRadar  
**Versão:** 0.2 (31/10/2026)  

---

## Como ler este documento
Cada ADR registra uma decisão técnica do LicitaRadar em três partes: contexto, decisão e consequências. Juntos, eles respondem como vamos construir e quais são as regras do time.

| ADR | Título |
| :--- | :--- |
| **0001** | Plataforma de nuvem: Google Cloud e Firebase |
| **0002** | Backend em Node.js, TypeScript, Fastify e Zod |
| **0003** | Frontend em SPA (React e Vite) separado da API REST |
| **0004** | Google Cloud Run (serverless) para a API |
| **0005** | Persistência com PostgreSQL e Firestore (Firebase Emulator Suite) |
| **0006** | Migrations do PostgreSQL com Drizzle ORM e drizzle-kit |
| **0007** | Ingestão do PNCP idempotente e resiliente |
| **0008** | Score de Aderência determinístico e configurável |
| **0009** | Estratégia de testes em camadas (Vitest e Playwright) |
| **0010** | CI/CD com GitHub Actions |
| **0011** | Docs-as-Code com MkDocs Material |
| **0012** | Plano custo zero (sem voucher GCP) — **substitui partes de 0001 e 0004** |

---

## ADR-0001: Plataforma de nuvem: Google Cloud e Firebase
- **Status:** Parcialmente substituído pelo **ADR-0012** (plano custo zero). Continua válido para Auth, Firestore e Hosting no Firebase; deixam de valer Blaze, alertas de orçamento GCP, Cloud Run, Cloud SQL e Secret Manager enquanto não houver voucher.
- **Contexto:** O LicitaRadar precisa ir ao ar em um ciclo de aproximadamente 2 meses, com teto de custo de US$ 50 por equipe (estimativa de US$ 10 a US$ 20). Frontend, API, banco e segredos precisam de hospedagem com pouca operação manual, e o time precisa rodar tudo localmente sem custo (RNF12).
- **Decisão:** Adotar o Google Cloud e o Firebase como plataforma única: Firebase Hosting (frontend), Cloud Run (API), Cloud SQL (PostgreSQL), Firestore e Secret Manager (segredos). Configurar alertas de orçamento em 50% (US$ 25), 80% (US$ 40) e 100% (US$ 50) e acompanhar o gasto semanalmente. A meta é gasto acumulado de até US$ 12 no Checkpoint 3.
- **Consequências:**
  * *Positivas:* Um só provedor, um só painel de custos e de permissões.
  * *Negativas ou trade-offs aceitos:* Dependência de um fornecedor (lock-in moderado).
  * *Neutras ou a observar:* O gasto precisa ser conferido toda semana contra o teto de US$ 50.
- **Impacto operacional:** O Tech Lead configura projeto, permissões e alertas de orçamento no Checkpoint 1. Todos os deploys passam pelo GitHub Actions (ADR-0010), autenticado no Google Cloud por federação de identidade, sem chaves de longa duração.
- **Referências:** Relacionados: ADR-0004, ADR-0005, ADR-0010

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
- **Contexto:** O Analista usa a plataforma todos os dias e precisa de filtros rápidos, com resposta em menos de 2 segundos. O frontend deve ser responsivo (RNF07) e distribuído com alta disponibilidade (RNF02). Toda a aplicação fica atrás de login.
- **Decisão:** Construir o frontend como SPA em React com Vite, TypeScript e Tailwind CSS, hospedada no Firebase Hosting e separada da API REST. A comunicação passa por camadas services/ HTTP tipadas. A filtragem e a paginação do feed ocorrem no servidor.
- **Consequências:**
  * *Positivas:* Deploys independentes e contrato OpenAPI claro entre as partes.
  * *Negativas ou trade-offs aceitos:* Exige configurar CORS entre o frontend e a API.
  * *Neutras ou a observar:* Meta de Lighthouse de 90 ou mais no Checkpoint 4.
- **Impacto operacional:** O deploy do frontend usa a Firebase CLI, orquestrada pelo GitHub Actions.
- **Referências:** Relacionados: ADR-0002, ADR-0010

---

## ADR-0004: Google Cloud Run (serverless) para a API
- **Status:** Substituído pelo **ADR-0012** enquanto vigorar o plano custo zero. A API vai para o Render (plano gratuito). Se o voucher GCP chegar, este ADR pode ser reativado.
- **Contexto:** O tráfego do Licita Radar é irregular: a ingestão do PNCP e o recálculo diário do score geram picos, e no resto do tempo o sistema fica ocioso. O custo de nuvem precisa ficar abaixo de US$ 50 e o RNF01 exige escalabilidade automática.
- **Decisão:** Executar a API Fastify em contêiner no Google Cloud Run, com escala automática e escala a zero quando ocioso. A observabilidade usa logs em JSON e as rotas `/healthz` e `/readyz`.
- **Consequências:**
  * *Positivas:* Custo baixo em ociosidade e deploy simples por imagem de contêiner.
  * *Negativas ou trade-offs aceitos:* A partida a frio pode aumentar a latência da primeira requisição.
  * *Neutras ou a observar:* As conexões com o PostgreSQL precisam de limite (pool) compatível com várias instâncias.
- **Impacto operacional:** Deploy automatizado pelo GitHub Actions, segredos injetados pelo Secret Manager e rollback ensaiado no Checkpoint 4.
- **Referências:** Relacionados: ADR-0001, ADR-0002, ADR-0010

---

## ADR-0005: Persistência com PostgreSQL e Firestore (Firebase Emulator Suite)
- **Status:** Em revisão sob o **ADR-0012**. Em produção no plano Spark, o caminho sem cartão é o **Firestore**; Cloud SQL (PostgreSQL) exige faturamento e fica fora do custo zero. Localmente o Emulator Suite de Firestore permanece obrigatório. O uso de PostgreSQL no Compose, se mantido, é só desenvolvimento e precisa de ADR próprio alinhado ao 0012.
- **Contexto:** O modelo tem dados relacionais (Organizações, Usuários, Editais e Certidões) e dados de leitura intensa por empresa (scores do feed e alertas). É preciso consistência, isolamento entre organizações (RNF06), ingestão idempotente de editais, identificados pelo número de controle do PNCP, e um ambiente local sem custo na nuvem (RNF12).
- **Decisão:** Usar dois bancos, cada um onde faz mais sentido. O PostgreSQL (Cloud SQL em produção e contêiner Docker via docker-compose no ambiente local) guarda os dados relacionais: organizações, usuários, editais e certidões. O Firestore guarda os dados por empresa de leitura intensa, como scores do feed e alertas, protegidos por Security Rules `deny-all`. Localmente e no CI, o Firebase Emulator Suite simula o Firestore. DBeaver ou pgAdmin servem para inspecionar o PostgreSQL.
- **Consequências:**
  * *Positivas:* Integridade referencial e unicidade de editais no PostgreSQL; Emulator Suite e Security Rules para testar o Firestore sem custo.
  * *Negativas ou trade-offs aceitos:* Dois bancos aumentam a complexidade; o Cloud SQL tem custo fixo mensal e o Firestore cobra por leitura e escrita, ambos a monitorar contra o teto de US$ 50.
  * *Neutras ou a observar:* A documentação de Arquitetura, que hoje cita só o PostgreSQL, precisa incluir o Firestore. É preciso definir como manter a consistência entre os dois bancos.
- **Impacto operacional:** Script de seed para o emulador e para o PostgreSQL local, credenciais no Secret Manager, backups conforme o plano do Cloud SQL e exportações do Firestore.
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0006, ADR-0008

---

## ADR-0006: Migrations do PostgreSQL com Drizzle ORM e drizzle-kit
- **Contexto:** O esquema do PostgreSQL precisa evoluir de forma versionada e reproduzível, no contêiner Docker local, no CI e no Cloud SQL. O time escreve tudo em TypeScript, valida dados com Zod e usa a camada repository no backend (ADR-0002). O projeto precisa de uma ferramenta de migrations que se encaixe nessa stack e rode bem em um serviço serverless.
- **Decisão:** Usar o Drizzle ORM com o drizzle-kit. O esquema é declarado em TypeScript, as migrations são geradas em SQL com `drizzle-kit generate`, versionadas no repositório e aplicadas com `drizzle-kit migrate` no ambiente local, no CI e no deploy, antes de cada release da API. Os schemas Zod podem ser derivados das tabelas com o `drizzle-zod`, mantendo tipos e validação coerentes. Regras do time: toda migration passa por revisão no pull request, uma migration já aplicada nunca é editada, e mudanças destrutivas são feitas em duas etapas.
- **Consequências:**
  * *Positivas:* Um único esquema em TypeScript como fonte dos tipos e da validação. Migrations em SQL legível, fáceis de revisar.
  * *Negativas ou trade-offs aceitos:* O Drizzle é mais novo que Prisma e Knex, e o time precisa aprender uma nova ferramenta.
  * *Neutras ou a observar:* O Firestore não usa migrations; mudanças na estrutura dos documentos são tratadas no código e nas Security Rules. A reversão de uma migration é feita por uma nova migration corretiva.
- **Impacto operacional:** O CI aplica as migrations em um PostgreSQL em contêiner antes dos testes de integração. No deploy, a migração roda com as credenciais do Secret Manager antes de publicar a nova versão no Cloud Run.
- **Referências:** Relacionados: ADR-0002, ADR-0005, ADR-0010

---

## ADR-0007: Ingestão do PNCP idempotente e resiliente
- **Status:** Complementado pelo **ADR-0012**: o agendamento diário sai do Cloud Scheduler e passa a ser um workflow do GitHub Actions com `schedule`, chamando rota protegida da API. Respeitar o teto de escritas do Firestore Spark (20 mil/dia): filtrar, upsert e teto por execução.
- **Contexto:** A fonte de dados do Licita Radar é a API do PNCP, que recebe um grande volume de editais por dia e, como serviço governamental, pode ficar instável, lenta ou devolver respostas mal formadas. Falhas aqui fazem o feed ficar poluído ou incompleto, riscos tratados como altos na Estratégia de Testes (R4 e R5).
- **Decisão:** Coletar os editais uma vez por dia, de forma automática e idempotente. O número de controle do PNCP é a chave única de cada edital, e a gravação é um `upsert`. Falhas (timeout, erro 500, JSON mal formado) geram nova tentativa com reprocessamento, e as falhas de cada coleta são registradas. Uma coleta interrompida retoma do ponto certo, sem duplicar. Cada payload é validado com Zod e normalizado: valor como `1.234,56` vira `1234.56`, datas viram ISO em UTC, a UF vai para maiúsculas e campos ausentes viram nulo. A base de editais é global e guardada uma única vez, para todas as empresas.
- **Consequências:**
  * *Positivas:* Reexecutar a coleta é seguro; o feed não tem editais repetidos. A resiliência a falhas do PNCP é testável com respostas gravadas.
  * *Negativas ou trade-offs aceitos:* Mais lógica de coleta (retry, retomada e registro de falhas) para manter.
  * *Neutras ou a observar:* O volume diário de dados precisa ser acompanhado contra o teto de custo.
- **Impacto operacional:** A coleta roda como tarefa diária agendada. Os testes usam fixtures gravadas do PNCP (página normal, várias páginas, resposta vazia, erro 500, timeout e JSON mal formado) e verificam que rodar a ingestão duas vezes não muda o número de editais.
- **Referências:** Relacionados: ADR-0005, ADR-0008, ADR-0009

---

## ADR-0008: Score de Aderência determinístico e configurável
- **Contexto:** O Score de Aderência é o núcleo do produto: ranqueia os editais do PNCP pelo perfil da empresa. O MVP não usa IA nem leitura de PDFs, o custo de nuvem tem teto e a relevância dos 10 primeiros resultados será medida na demonstração.
- **Decisão:** Calcular a nota de 0 a 100 de forma determinística: a mesma entrada (perfil, edital e data de referência) sempre gera a mesma nota. O cálculo tem três passos: um funil eliminatório (termos-chave, categoria, prazo, modalidade e valor), a pontuação por critério (termos 40, UF 25, valor 15, prazo 10 e modalidade 10) e a reescala quando critérios opcionais não estão configurados. Pesos, faixas e limiares ficam em constantes de configuração. O detalhamento por critério e a data de referência são gravados junto com o score de cada empresa. O refinamento dinâmico do feed não recalcula o score.
- **Consequências:**
  * *Positivas:* Resultado explicável, testável e repetível.
  * *Negativas ou trade-offs aceitos:* O recálculo diário multiplica empresas por editais ativos, e o Firestore cobra por leitura e escrita; o custo precisa ser acompanhado.
  * *Neutras ou a observar:* Calibrar os pesos depois da demonstração, medindo a Precisão dos 10 primeiros resultados.
- **Impacto operacional:** O score é recalculado ao coletar novos editais, ao editar o perfil e uma vez por dia, porque os pontos de prazo mudam com o calendário. A tabela de casos de borda e o exemplo resolvido viram testes automatizados.
- **Referências:** Relacionados: ADR-0005, ADR-0007, ADR-0009

---

## ADR-0009: Estratégia de testes em camadas (Vitest e Playwright)
- **Contexto:** Os riscos mais caros são perder um edital aderente, errar prazos ou a validade de certidões e vazar dados entre empresas. Os testes devem custar pouco, nunca tocar o PNCP real nem a produção e apontar o problema exato quando falharem.
- **Decisão:** Adotar quatro camadas: unitários (cerca de 65%), integração (cerca de 20%), API e contrato (cerca de 10%) e E2E (cerca de 5%). O Vitest cobre unitários e integração, e o Playwright cobre as cinco jornadas do MVP. Os testes rodam sem custo, com Firebase Emulator Suite (Firestore e Security Rules) e PostgreSQL em contêiner Docker. O PNCP é simulado com fixtures gravadas, o relógio é fixado nos testes e o envio de alertas é coletado em memória. Todo caso de teste segue o padrão de sete componentes (ID, descrição, pré-requisitos, etapas, resultados esperados, resultados reais e status). A Precisão dos 10 primeiros resultados é medida com um conjunto de referência (golden set).
- **Consequências:**
  * *Positivas:* Feedback rápido e rastreabilidade entre requisito e teste.
  * *Negativas ou trade-offs aceitos:* Manter fixtures e o golden set exige trabalho contínuo.
  * *Neutras ou a observar:* Os percentuais são referência; muitos E2E para regra de negócio indicam teste na camada errada.
- **Impacto operacional:** Metas do Checkpoint 4: cobertura de domínio de pelo menos 80%, zero vulnerabilidades críticas e Lighthouse de pelo menos 90. Bugs são abertos como Issues, com severidade e ID do caso de teste.
- **Referências:** Relacionados: ADR-0005, ADR-0007, ADR-0008, ADR-0010

---

## ADR-0010: CI/CD com GitHub Actions
- **Status:** Complementado pelo **ADR-0012**. Em cada PR continua o CI (lint, build, testes, **gitleaks**). Deploy do frontend segue no Firebase Hosting; deploy da API no plano custo zero é o Render (merge na `main` após CI). O schedule de ingestão PNCP também vive no Actions.
- **Contexto:** O ciclo é curto e a equipe adota ScrumXP, que exige integração contínua, revisão de código e testes automatizados. O código está no GitHub e o deploy vai para o Firebase Hosting e o Cloud Run.
- **Decisão:** Usar o GitHub Actions para CI/CD (RNF09). Em cada pull request, em ordem e parando na primeira falha: lint e tipos, testes unitários com cobertura, integração, contrato da API, E2E e Precisão do score. Nenhum código entra na main sem revisão de pelo menos uma pessoa diferente do autor. O deploy em produção é manual (com aprovação), depois que o smoke test de staging passar. O CI também faz varredura de segredos.
- **Consequências:**
  * *Positivas:* Qualidade verificada a cada mudança e deploys reproduzíveis.
  * *Negativas ou trade-offs aceitos:* O pipeline completo leva mais tempo; os passos rápidos rodam primeiro.
  * *Neutras ou a observar:* A autenticação com o Google Cloud usa federação de identidade, sem chaves de longa duração.
- **Impacto operacional:** Critérios para aprovar um pull request: todos os passos verdes, cobertura de linhas de pelo menos 80% nos módulos de normalização, score, prazos e certidões, e Precisão sem queda em relação à main.
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0006, ADR-0009

---

## ADR-0011: Docs-as-Code com MkDocs Material
- **Contexto:** A documentação (visão, requisitos, arquitetura, testes e ADRs) precisa ser atualizada continuamente e revisada junto com o código. A metodologia ScrumXP prefere documentação leve e essencial.
- **Decisão:** Manter a documentação em Markdown no repositório e publicá-la com MkDocs Material no GitHub Pages. Mudanças de documentação entram por pull request, como o código, e os ADRs ficam em uma seção própria do menu lateral.
- **Consequências:**
  * *Positivas:* Histórico versionado e revisão por pares.
  * *Negativas ou trade-offs aceitos:* Quem não usa Git precisa de apoio para contribuir.
  * *Neutras ou a observar:* Registrar as alterações no histórico de versões de cada documento.
- **Impacto operacional:** O site é gerado e publicado automaticamente pelo CI.
- **Referências:** Relacionados: ADR-0010

---

## ADR-0012: Plano custo zero (sem voucher GCP)
- **Status:** Proposto — aguardando aprovação do professor e do Tech Lead (prazo Checkpoint 2: 01/11/2026).
- **Contexto:** O Caderno B2B assume plano Blaze (Cloud Run, Cloud Scheduler, Workload Identity, Secret Manager e alertas de orçamento). A equipe LicitaRadar não tem voucher GCP. Cadastrar cartão de estudante está fora de questão. O documento "Plano Custo Zero — sem o voucher do GCP" (14/10/2026) define substitutos gratuitos sem cartão. Até a Semana 4 tudo continua no Docker Compose e emuladores; a diferença aparece na Semana 5.
- **Decisão:** Adotar o plano custo zero para o LicitaRadar:

  | Peça do Caderno | Substituição |
  | :--- | :--- |
  | Firebase Auth + Firestore + Hosting | Firebase **Spark** (sem Blaze / sem faturamento) |
  | Cloud Run (API) | **Render** (web service gratuito) |
  | Cloud Scheduler (ingestão PNCP) | **GitHub Actions** com `schedule` (cron) |
  | Secret Manager | Secrets do GitHub + variáveis de ambiente do Render |
  | Workload Identity | Chave da conta de serviço Admin em secret (exceção; detalhar na Semana 5) |
  | Alertas de orçamento 50/80/100% | Prints em `docs/finops/` (prova de “sem forma de pagamento”) |
  | Cloud Monitoring | Logs do Render + UptimeRobot (Semana 5) |

  **Não se aplica ao LicitaRadar:** Supabase Storage (só Habilita e EntregaProva).

  **Regras de ouro:** (1) nunca cadastrar cartão; (2) nunca Upgrade/Blaze; (3) testes pesados e seed grande só no emulador; (4) um projeto Firebase por equipe; (5) chave de serviço com papel mínimo só em secrets; gitleaks no CI; (6) evidências em FinOps; (7) serviço novo = ADR + ok do Tech Lead.

  **Ingestão PNCP:** filtrar por UF/modalidade/janela dos perfis; upsert sem regravar igual; teto de editais por execução (alinhar ao ADR-0007).
- **Consequências:**
  * *Positivas:* Gasto na nuvem = US$ 0; sem risco de cobrança em cartão de estudante.
  * *Negativas ou trade-offs aceitos:* Cold start do Render (~1 min); cotas diárias do Spark (ex.: 20 mil escritas Firestore); exceções ao Caderno precisam de aprovação do professor.
  * *Neutras ou a observar:* Se o voucher chegar, Auth/Firestore/Hosting permanecem; basta migrar a API para Cloud Run e reativar o ADR-0004.
- **Impacto operacional:** DevOps mantém Compose + emuladores até a Semana 4; registra evidências em FinOps; ativa o workflow de ingestão e o Render na Semana 5. Tech Lead aprova este ADR com o professor até 01/11.
- **Referências:** Relacionados: ADR-0001, ADR-0004, ADR-0005, ADR-0007, ADR-0010. Fonte: Plano Custo Zero — sem o voucher do GCP (14/10/2026). FinOps: `docs/finops/`.


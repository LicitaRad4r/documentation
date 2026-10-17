## Requisitos Não Funcionais (RNF)

Enquanto os Requisitos Funcionais descrevem *o que* o sistema faz, os Requisitos Não Funcionais (RNF) definem *como* o sistema se comporta, estabelecendo as métricas de qualidade, segurança e desempenho.

### Desempenho e Escalabilidade
*   **RNF01 - Escalabilidade Automática:** A arquitetura do *backend* deve suportar o aumento dinâmico de tráfego, utilizando o ambiente *serverless* do Google Cloud Run para garantir a elasticidade dos serviços.
*   **RNF02 - Entrega de Conteúdo:** O *frontend* deve garantir um carregamento de página rápido e de alta disponibilidade, sendo distribuído através do Firebase Hosting.
*   **RNF03 - Eficiência de Processamento:** O *backend* deve apresentar baixo *overhead* no processamento de requisições, utilizando o *framework* Fastify.

### Segurança e Conformidade
*   **RNF04 - Proteção de Credenciais:** Todas as chaves de API, variáveis de ambiente e credenciais de acesso aos bancos de dados devem ser geridas e protegidas de forma centralizada pelo Google Secret Manager.
*   **RNF05 - Validação de Dados:** O sistema deve garantir a integridade e validação rigorosa dos esquemas de dados recebidos (ex: *payloads* do PNCP) utilizando a biblioteca Zod.
*   **RNF06 - Isolamento de Dados:** A base de dados PostgreSQL deve garantir a segregação lógica das informações, impedindo que utilizadores de uma organização (PME) tenham acesso a contratos e certidões de terceiros.

### Usabilidade e Interface
*   **RNF07 - Responsividade:** A interface de utilizador deve adaptar-se adequadamente a diferentes tamanhos de ecrã (computadores, *tablets* e *smartphones*), utilizando a *framework* Tailwind CSS para a estilização.
*   **RNF08 - Tipagem Estática:** Todo o código fonte da aplicação (tanto *frontend* em React quanto *backend* em Node.js) deve ser escrito em TypeScript para minimizar erros em tempo de execução e melhorar a experiência de desenvolvimento.

### Manutenibilidade e Qualidade de Software
*   **RNF09 - Automação de Entregas (CI/CD):** O sistema deve possuir *pipelines* de Integração e Entrega Contínuas orquestrados via GitHub Actions, automatizando os *deploys* para o Cloud Run e Firebase.
*   **RNF10 - Cobertura de Testes Unitários:** A lógica de negócio e os componentes isolados devem ser cobertos por testes automatizados executados com o Vitest.
*   **RNF11 - Testes Ponta-a-Ponta (E2E):** Os fluxos principais dos utilizadores na interface devem ser validados através de testes automatizados utilizando o Playwright.
*   **RNF12 - Simulação Local:** A equipa de desenvolvimento deve ser capaz de correr o ambiente sem custos na nuvem, utilizando o Firebase Emulator Suite para simular serviços de base de dados durante a construção.
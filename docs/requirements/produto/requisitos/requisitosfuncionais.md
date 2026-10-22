# Requisitos Funcionais do Produto (MVP)

Os Requisitos Funcionais (RF) definem as funcionalidades e os comportamentos essenciais que o LicitaRadar deve ter para entregar valor às nossas Personas principais (o Decisor e a Analista). 

Para garantir o foco do Produto Mínimo Viável (MVP), os requisitos foram divididos em quatro módulos estratégicos, priorizando a descoberta de editais e a conformidade documental em detrimento de automações de lances.

---

## Módulo 1: Gestão de Acessos e Hierarquia
Garante a segurança dos dados da organização e a separação de responsabilidades.

*   **RF01 - Autenticação de Utilizadores:** O sistema deve permitir o login, registo e recuperação de palavra-passe através de e-mail corporativo.
*   **RF02 - Gestão de Convites (Hierarquia):** O sistema deve permitir que um utilizador com o perfil de "Administrador/Decisor" envie convites de acesso para utilizadores com o perfil de "Analista/Convidado" para a sua organização.
*   **RF03 - Restrição de Acessos Sensíveis:** O sistema deve bloquear o acesso do perfil "Analista" às áreas de *upload* de documentos legais, certidões e dados financeiros da organização.

---

## Módulo 2: Motor de Busca e Triagem (Integração PNCP)
Focado em otimizar o tempo operacional na procura de oportunidades.

*   **RF04 - Feed de Oportunidades:** O sistema deve exibir uma lista de editais e licitações ativas, consumidos e atualizados a partir do Portal Nacional de Contratações Públicas (PNCP).
*   **RF05 - Filtros Dinâmicos:** O sistema deve permitir filtrar os editais por palavras-chave, localização (UF/Município), modalidade, valor estimado e datas de abertura/fecho.
*   **RF06 - Score de Aderência:** O sistema deve calcular e exibir uma pontuação de compatibilidade (Score) para cada edital, baseando-se no perfil e no histórico de preferências configurados pela organização.
*   **RF07 - Sinalização de Oportunidades:** O sistema deve permitir que o perfil "Analista" marque um edital específico como "Recomendado" ou "Favorito", encaminhando-o para a fila de visualização do "Administrador".

---

## Módulo 3: Gestão Documental e Conformidade
Focado em mitigar o risco de inabilitação por perda de prazos de documentos.

*   **RF08 - Repositório de Certidões:** O sistema deve permitir que o perfil "Administrador" faça o *upload* de documentos e certidões em formato PDF ou imagem.
*   **RF09 - Registo de Validade:** O sistema deve exigir a introdução da data de validade de cada certidão carregada na plataforma.
*   **RF10 - Alerta de Vencimento:** O sistema deve gerar alertas visuais (e/ou enviar notificações) para os documentos que estejam a menos de uma quantidade configurável de dias do seu vencimento ou que já se encontrem expirados.

---

## Módulo 4: Fluxo de Decisão
Focado na tomada de decisão ágil pela gestão.

*   **RF11 - Dashboard de Decisão:** O sistema deve apresentar ao perfil "Administrador" um painel consolidado exclusivamente com os editais previamente recomendados pela "Analista".
*   **RF12 - Aprovação e Descarte:** O sistema deve permitir que o "Administrador" altere o estado de um edital recomendado para "Aprovado para Disputa" (iniciando o processo legal externo) ou "Descartado" (removendo a oportunidade do funil).
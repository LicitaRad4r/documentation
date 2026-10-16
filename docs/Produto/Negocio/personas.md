# Personas e Anti-Personas do Usuário

O LicitaRadar foi desenhado para PMEs fornecedoras do setor público. O sistema divide a operação entre o decisor (dono dos dados e do risco legal) e o operacional (convidado para a triagem). 

Abaixo detalhamos as personas que norteiam o desenvolvimento do produto e as anti-personas, que delimitam o escopo técnico do MVP.

### "Persona é hipótese"
Nomes, idades e falas são ilustrativos. O modelo de permissões e as dores partem do problema definido para o projeto.

---

## 1. O Decisor e Dono da Conta: Bruno Cruvinel

> "A responsabilidade legal e financeira da empresa é minha. Eu preciso de uma plataforma onde os meus dados estejam seguros, centralizados e que me mostre apenas o que vale a pena disputar, sem que eu perca horas a procurar."

| Perfil | Detalhes |
| :--- | :--- |
| **Idade** | 47 anos |
| **Cargo** | Sócio e Diretor Comercial |
| **Empresa** | PME de tecnologia que presta serviços a órgãos públicos, 25 funcionários |
| **Responsabilidade** | Decide as licitações que a empresa vai disputar e conduz os trâmites legais de inscrição nos portais governamentais externos. |
| **Acessos no Sistema** | Administrador total (Dono da conta e da organização). |

### Papel e Permissões no LicitaRadar
O Bruno é o topo da hierarquia no sistema. O seu perfil possui os seguintes privilégios e responsabilidades exclusivos:
- **Gestão de Acessos:** É o único com permissão para convidar a Analista (a sua assistente) para acessar à organização no LicitaRadar.
- **Controlo de Dados Sensíveis:** Tem acesso exclusivo aos dados da empresa, contratos públicos e documentos oficiais.
- **Gestão de Certidões:** É o único responsável por anexar, revalidar e fazer o *upload* de certidões atualizadas no sistema.
- **Triagem e Decisão:** Pode fazer as próprias filtragens, mas o seu fluxo principal é receber os editais pré-filtrados pela Analista, decidir e realizar o trâmite oficial de disputa.

### Dores e Necessidades
- **Segurança da Informação:** Não pode partilhar o controle dos documentos legais com perfis estritamente operacionais.
- **Risco de Inabilitação:** Teme ser desclassificado de um edital ganho porque um documento venceu sem que ele soubesse.
- **Custo de Oportunidade:** Precisa de ter a certeza de que a equipa não está a deixar passar editais valiosos por causa de filtros manuais mal feitos.

### "Critério de Sucesso do Bruno"
Ter a certeza de que o ambiente documental da empresa está seguro e em conformidade (alertas de certidões) e receber, via sistema, uma lista curada pela sua analista para tomar decisões rápidas e assertivas.

---

## 2. A Analista Convidada: Mariana Costa

> "O meu trabalho é encontrar editais manualmente em que a empresa consiga se encaixar. O LicitaRadar ajuda-me a limpar o ruído do PNCP e a entregar oportunidades altamente qualificadas para a aprovação do meu diretor."

| Perfil | Detalhes |
| :--- | :--- |
| **Idade** | 29 anos |
| **Cargo** | Analista de Licitações (Assistente) |
| **Empresa** | PME de tecnologia que presta serviços a órgãos públicos, 25 funcionários |
| **Responsabilidade** | Filtra  os editais mais relevantes e encaminha-os para o diretor. |
| **Acessos no Sistema** | Perfil estritamente operacional e restrito. Só existe via convite do Bruno. |

### Papel e Permissões no LicitaRadar
A Mariana é o "motor" da pesquisa no dia a dia. A sua visão do sistema é limpa e voltada exclusivamente para a oportunidade de negócio:
- **Ausência de Controlo Sensível:** Não tem acesso ao *upload* de certidões, contratos guardados ou dados críticos da empresa.
- **Pesquisa e Mineração:** A sua principal ferramenta é o Feed e os Filtros Dinâmicos do LicitaRadar, aplicando a estratégia da empresa para encontrar oportunidades de nicho.
- **Curadoria e Envio:** A sua principal responsabilidade é utilizar o Score de Aderência para separar os melhores editais e encaminhá-los/sinalizá-los no sistema para a aprovação do Bruno.

### Dores e Necessidades
- **Exaustão Operacional:** Perde horas a ler PDFs de dezenas de páginas só para descobrir que a empresa não atende aos requisitos logísticos de um edital no PNCP.
- **Dependência de Sistemas Lentos:** Precisa de filtros rápidos e dinâmicos, que destaquem visualmente valores e UFs.

### "Critério de Sucesso da Mariana"
Parar de perder tempo a descartar editais inúteis. Ela entra no LicitaRadar, encontra rapidamente as cinco melhores licitações da semana e envia-as para o Bruno aprovar com confiança.

---

## 3. Anti-Personas (Quem o LicitaRadar NÃO atende)

Para garantir o sucesso do MVP (Produto Mínimo Viável) e manter o foco na resolução de dores das PMEs, o sistema restringe explicitamente as funcionalidades que não geram valor direto para as personas principais. 

Os perfis abaixo **não serão contemplados** pelas decisões de arquitetura e design do LicitaRadar:

### Thiago, O "Operador de Robôs de Lance" (Foco em Automação de Disputa)
- **Quem é:** Um representante comercial que participa em dezenas de pregões por dia usando "robôs de lances". O seu foco é o volume e o ganho por exaustão (disparando cêntimos a menos automaticamente).
- **Por que é uma anti-persona:** O LicitaRadar é uma ferramenta de inteligência de dados, prospeção e conformidade documental. O MVP não inclui envio de propostas, integração com salas de disputa governamentais ou automação de lances. 

### Roberto, A "Grande Corporação" (Foco em Complexidade de Software)
- **Quem é:** Gerente de Contratos de uma multinacional com 800 funcionários, múltiplos diretores e um departamento jurídico completo.
- **Por que é uma anti-persona:** A corporação exige fluxos de aprovação em dezenas de níveis hierárquicos e integrações profundas com ERPs (ex: SAP). O LicitaRadar é intencionalmente restrito e enxuto (Dono da conta + Convidado), pensado para não burocratizar o processo ágil das PMEs.
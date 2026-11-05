# Interação entre Equipe e Cliente

## Composição da Equipe

A estrutura da equipe do LicitaRadar foi definida para cobrir todas as frentes de engenharia, produto e qualidade, garantindo fluidez no desenvolvimento ágil:

| Papel | Responsável | Responsabilidades |
| :--- | :--- | :--- |
| **Product Owner (PO)** | Maria Júlia Sena | Representa o usuário final (sócio e analista), prioriza o *backlog* (MoSCoW), garante o alinhamento com a Visão do Produto e valida os critérios de aceite. |
| **Tech Lead** | Júlia Neiva | Lidera as decisões de arquitetura (ADRs), a modelagem de dados e a infraestrutura. |
| **Desenvolvedores Full Stack** | João Paulo Barbosa, Luis Felipe Albuquerque, Kauan Tarchetti e Giovanni Dornelas | Atuam em todas as camadas: front-end (React), back-end (Fastify), testes automatizados e pipeline de CI/CD. |

---

## Comunicação e Interação

Para garantir transparência e fluidez no processo híbrido (ScrumXP), a equipe interage com os tutores e stakeholders do AI Lab, que fazem o papel de cliente do ciclo B2B e validam as entregas nos checkpoints. Internamente, a PO assume a representação constante do usuário final nas decisões de priorização diárias.

### Ferramentas de Comunicação

| Ferramenta | Uso |
| :--- | :--- |
| **WhatsApp** | Canal oficial da comunicação diária assíncrona da equipe, resolução rápida de bloqueios técnicos e avisos de *deploys*. |
| **Google Meet** | Cerimônias síncronas do Scrum (planejamento, revisão e retrospectiva) e reuniões formais de validação nos checkpoints. |
| **GitHub (Projects/Issues)** | Ferramenta central para planejamento das sprints, acompanhamento do Kanban, gestão do *backlog* de histórias de usuário e revisão de código (*Pull Requests*). |

### Métodos e Frequência de Interação

A cadência de trabalho e feedback baseia-se nos seguintes rituais:

*   **Reuniões de alinhamento semanal:** Reuniões curtas (20 a 30 minutos) para avaliar o que foi concluído, registrar bloqueios, gerir riscos e acompanhar o orçamento de nuvem.
*   **Revisões de checkpoint (quinzenais):** Coincidem com as datas de entrega dos checkpoints do ciclo B2B, momento em que a equipe apresenta as jornadas funcionais desenvolvidas para a validação externa do cliente/tutores.
*   **Comunicação técnica contínua:** A equipe técnica interage diariamente por meio de *code reviews* obrigatórios no GitHub antes de qualquer integração de código na branch principal (`main`).
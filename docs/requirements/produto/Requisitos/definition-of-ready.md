# LicitaRadar — Definition of Ready (DoR)
**Tech Lead:** Júlia Neiva Lima  
**Versão:** 1.0  
**Data:** 31/10/2026  

---

## Histórico de versões

| Versão | Data | Alteração |
| :--- | :--- | :--- |
| 1.0 | 31/10/2026 | Documento próprio do DoR, na seção de Requisitos, com os 6 itens detalhados e checklist. |

---

## 1. O que é

A **Definition of Ready (DoR)** é a lista de condições que uma história de usuário ou tarefa precisa cumprir **antes** de poder ser puxada para o sprint e iniciar o desenvolvimento. O objetivo é garantir que o escopo está claro, validado e que o time tem todas as informações necessárias para implementar sem bloqueios.

O DoR é um artefato de engenharia de requisitos e vem antes da [Definition of Done (DoD)](definition-of-done.md): o DoR diz quando a tarefa **pode começar**; a DoD diz quando ela **está pronta**.

**Quando é verificado:** no refinamento do backlog e, uma última vez, no planejamento do sprint. A PO (Maria Júlia Sena) confirma os itens 2 e 3; o time confirma os itens 4, 5 e 6. A história que não cumpre o DoR volta ao refinamento e não entra no sprint. A exceção é um bug crítico, que o Tech Lead pode autorizar, registrando o motivo na issue.

---

## 2. Os 6 Itens Obrigatórios do DoR

| # | Item Obrigatório do DoR | Como verificar / Critério |
| :---: | :--- | :--- |
| **1** | **História escrita no formato padrão** | Descrição clara no padrão de História de Usuário (*Como [persona], eu quero [ação], para que [benefício]*). |
| **2** | **Critérios de aceite mensuráveis e claros** | A história possui critérios de aceite específicos, testáveis e sem ambiguidades definidos ou validados pela PO. |
| **3** | **Prioridade e valor de negócio validados** | A tarefa está priorizada no backlog e alinhada com os objetivos do produto e prazos dos Checkpoints. |
| **4** | **Dependências técnicas mapeadas** | APIs externas (ex: PNCP), componentes de banco, dependências de outras tarefas ou restrições de arquitetura identificadas. |
| **5** | **Mockups de tela ou wireframes (se aplicável)** | Se a tarefa envolver interface, há telas ou rascunhos definindo os estados de carregamento, vazio e erro. |
| **6** | **Estimativa de esforço realizada** | O time pontuou/estimou a complexidade da tarefa durante o planejamento. |

---

## 3. Detalhamento dos Itens

### 1. História escrita no formato padrão
- A história segue o modelo *Como [persona], eu quero [ação], para que [benefício]*. Exemplo: *"Como Analista, eu quero filtrar o feed por UF, para ver apenas os editais da minha região."*
- Cada história entrega valor completo e cabe em um sprint. Se não couber, é dividida.
- A história cita o requisito (RF ou RNF) de que se origina.
- Tarefas técnicas sem persona (por exemplo, configurar o CI) descrevem o objetivo e quem se beneficia.

### 2. Critérios de aceite mensuráveis e claros
- Cada critério pode ser respondido com "passou" ou "não passou". Evitar termos vagos como "rápido" ou "amigável"; usar números (por exemplo, "resposta em até 2 segundos").
- Formato sugerido: *Dado [contexto], quando [ação], então [resultado esperado]*.
- Os critérios incluem os casos de borda e de erro mais importantes (resposta vazia, falha do PNCP, perfil incompleto).
- Cada critério deve poder virar pelo menos um caso de teste com ID (por exemplo, `CT-SCORE-001`), o que é cobrado no item 2 da [DoD](definition-of-done.md).
- A PO valida os critérios, e o registro fica na issue.

### 3. Prioridade e valor de negócio validados
- A história está ordenada no backlog e ligada ao Checkpoint em que será entregue.
- A PO confirma que a história continua alinhada aos objetivos do produto e aos prazos.
- Quando o prazo aperta, o que está mais acima no backlog entra primeiro; mudanças de prioridade são registradas na issue.

### 4. Dependências técnicas mapeadas
- Estão listados: a API do PNCP e suas fixtures de teste, tabelas e migrations do PostgreSQL, coleções e Security Rules do Firestore, rotas e contrato OpenAPI, e outras histórias que precisam ser entregues antes.
- **Custo e quotas:** como o teto de custo é de US$ 0, a história diz se aumenta o consumo das quotas gratuitas (escritas e leituras do Firestore, armazenamento ou horas do Neon, horas do Render, minutos do GitHub Actions) e estima esse aumento. Se exigir um serviço novo ou um plano pago, precisa de ADR ou de aprovação do Tech Lead antes de entrar no sprint.
- Dependências ainda não resolvidas são registradas como bloqueio, com responsável.

### 5. Mockups de tela ou wireframes (se aplicável)
- Se a história envolve interface, há tela, wireframe ou rascunho anexado à issue.
- O mockup mostra os três estados que a DoD exige: carregando, vazio (com texto que explica o que fazer) e erro (com forma de tentar de novo).
- Como a API pode demorar para acordar após inatividade (ADR-0004), o estado de carregando mostra uma mensagem adequada para esse caso.
- Se a história não tem interface, o item é marcado como "não se aplica" na issue.

### 6. Estimativa de esforço realizada
- O time estimou a história em pontos, no planejamento, por consenso (por exemplo, Planning Poker).
- Histórias muito grandes (sugestão: acima de 8 pontos) são divididas antes de entrar no sprint.
- A estimativa considera o desenvolvimento, os testes e a documentação, não só o código.

---

## 4. Ligação com a DoD

| Do DoR | Vira, na [DoD](definition-of-done.md) |
| :--- | :--- |
| Item 2: critérios de aceite mensuráveis | Item 2: teste novo com o ID do caso de teste, citando o critério no PR. |
| Item 4: dependências técnicas e impacto nas quotas | Item 6: nenhum recurso novo ou plano pago sem ADR ou aprovação. |
| Item 5: mockups com os três estados | Item 4: telas com carregando, vazio e erro, com print ou teste. |

---

## 5. Checklist para colar na issue ou história

```markdown
- [ ] História no formato "Como [persona], eu quero [ação], para que [benefício]"
- [ ] Critérios de aceite mensuráveis, validados pela PO
- [ ] Prioridade e valor de negócio validados (ligada a um Checkpoint)
- [ ] Dependências técnicas mapeadas (PNCP, bancos, rotas, outras tarefas)
- [ ] Impacto nas quotas gratuitas estimado; nenhum serviço novo ou plano pago sem ADR
- [ ] Mockups com estados de carregando, vazio e erro (ou "não se aplica" com motivo)
- [ ] Estimativa de esforço feita pelo time
```
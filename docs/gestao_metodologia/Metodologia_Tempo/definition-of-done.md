# LicitaRadar — Definition of Done (DoD)
**Tech Lead:** Júlia Neiva Lima  
**Versão:** 1.2  
**Data:** 31/10/2026  

---

## Histórico de versões

| Versão | Data | Alteração |
| :--- | :--- | :--- |
| 1.1 | 18/10/2026 | Versão com DoR (tabela) e DoD detalhada no mesmo documento. |
| 1.2 | 31/10/2026 | Documento movido para a seção de Requisitos; o DoR passou a ter documento próprio. DoD atualizada para o teto de custo de US$ 0 (itens 3 e 6), conforme as ADRs v0.2. |

---

## 1. O que é

A **Definition of Done (DoD)** é a lista de condições que toda entrega precisa cumprir para ser considerada pronta. Ela vale para qualquer pull request que entre na branch `main`, seja código, teste, configuração ou documentação. Se um item não se aplica ao PR, o autor escreve no PR o motivo; não basta deixar em branco.

A DoD é um artefato de engenharia de requisitos e vem depois da [Definition of Ready (DoR)](definition-of-ready.md): o DoR diz quando a tarefa **pode começar**; a DoD diz quando ela **está pronta**.

---

## 2. Os 6 Itens Obrigatórios da DoD (Caderno - Seção 1.6)

| # | Item Obrigatório da DoD | Como verificar |
| :---: | :--- | :--- |
| **1** | **PR revisado por alguém diferente do autor e CI verde** | Aprovação no GitHub e todos os checks do GitHub Actions passando. |
| **2** | **Teste novo ligado a um critério de aceite** | O ID do caso de teste aparece no nome do teste e o critério de aceite está citado no PR. |
| **3** | **Nenhum segredo (senha, chave) no código** | Varredura de segredos do CI verde e revisão do diff. |
| **4** | **Telas com estado de carregando, vazio e erro** | Os três estados aparecem na tela, com print ou teste no PR. |
| **5** | **Logs sem dados pessoais** | Revisão dos logs novos ou alterados. |
| **6** | **Nenhum recurso novo de nuvem nem plano pago sem ADR ou aprovação do Tech Lead** | Link do ADR ou aprovação registrada no PR. |

---

## 3. Detalhamento dos Itens

### 1. PR revisado por alguém diferente do autor e CI verde
- Toda mudança entra por pull request. Ninguém faz commit direto na `main`.
- Quem revisa é sempre outra pessoa que não o autor. As revisões do time são feitas pela Júlia Neiva (Tech Lead) e pela Maria Júlia Sena (PO).
- O CI precisa estar verde: lint e tipos, testes unitários, integração, contrato da API, E2E e varredura de segredos. Um PR com check vermelho não é aprovado.
- O autor responde aos comentários da revisão antes do merge.

### 2. Teste novo ligado a um critério de aceite
- Cada funcionalidade nova ou bug corrigido vem com pelo menos um teste novo.
- O teste está ligado a um critério de aceite da história de usuário (definido no item 2 do [DoR](definition-of-ready.md)). O ID do caso de teste vai no nome, por exemplo `CT-SCORE-001`: nota é 0 quando nenhum critério é atendido.
- Em correção de bug, o teste precisa falhar antes da correção e passar depois.
- A descrição do PR cita a história e o critério de aceite que o teste cobre.

### 3. Nenhum segredo (senha, chave) no código
- Senhas, chaves de API, tokens e strings de conexão ficam fora do código, dos testes, dos arquivos de configuração versionados e dos logs.
- No ambiente local, usar variáveis em `.env` (o repositório só tem o `.env.example`, sem valores reais). No CI e nos deploys, os segredos ficam no GitHub Actions Secrets; em runtime, nas variáveis de ambiente do Render (ADR-0001).
- Se um segredo for commitado por engano, ele é considerado vazado: avise o Tech Lead e troque a credencial. Apagar o commit não basta.

### 4. Telas com estado de carregando, vazio e erro
- Toda tela que busca dados da API mostra os três estados: carregando (enquanto a resposta não chega), vazio (a consulta funcionou e não há resultados) e erro (a chamada falhou, com mensagem compreensível e forma de tentar de novo).
- O estado vazio explica o que fazer, por exemplo *"Nenhum edital com esses filtros. Ajuste os filtros ou o perfil"*.
- O PR traz prints dos três estados ou um teste que os exercita.

### 5. Logs sem dados pessoais
- Os logs (em JSON) não registram dados pessoais nem sensíveis: nome, e-mail, senha, tokens, conteúdo de certidões e dados de contratos da empresa.
- Para rastrear um problema, registre identificadores internos (por exemplo, o id do usuário ou da organização) e o ID da requisição.
- Quem revisa o PR confere os logs novos ou alterados.

### 6. Nenhum recurso novo de nuvem nem plano pago sem ADR ou aprovação do Tech Lead
- O teto de custo do projeto é de **US$ 0** (ADR-0001). Ninguém ativa o plano Blaze, cadastra cartão de crédito ou contrata um plano pago.
- Criar qualquer serviço ou recurso novo exige um ADR que o descreva ou a aprovação do Tech Lead, registrada no PR. Isso inclui projetos Firebase, projetos ou branches do Neon, serviços do Render e workflows agendados do GitHub Actions.
- Mudanças que aumentem de forma relevante o uso das quotas gratuitas (escritas e leituras do Firestore, armazenamento do Neon, horas do Render, minutos do GitHub Actions) seguem a mesma regra, e o PR informa o impacto estimado.
- O motivo é que, ao esgotar uma quota gratuita, o serviço deixa de responder até a renovação; o consumo é conferido toda semana, com meta de ficar abaixo de 60% de cada quota.

---

## 4. Complementos do projeto
Os itens abaixo já fazem parte do processo de qualidade do LicitaRadar e valem junto com os critérios obrigatórios:
- Cobertura de linhas de pelo menos 80% nos módulos de normalização, score, prazos e certidões.
- A Precisão dos 10 primeiros resultados do feed não caiu em relação à `main`, ou a queda foi justificada no PR.
- A tarefa cumpriu os critérios do [DoR](definition-of-ready.md) (com critérios de aceite mensuráveis e prioridade validada pela PO antes de entrar em desenvolvimento).
- Nenhum bug de severidade Crítica ou Alta aberto na funcionalidade.
- Contrato da API (OpenAPI) atualizado quando a rota mudar.
- Documentação e ADRs atualizados quando a mudança alterar uma decisão técnica.

---

## 5. Checklist para colar no pull request

```markdown
- [ ] Tarefa validada previamente na Definition of Ready (DoR)
- [ ] Revisado por alguém diferente do autor e CI verde
- [ ] Teste novo ligado a um critério de aceite (ID do caso de teste citado)
- [ ] Nenhum segredo no código
- [ ] Telas com estado de carregando, vazio e erro
- [ ] Logs sem dados pessoais
- [ ] Nenhum recurso novo de nuvem nem plano pago sem ADR ou aprovação do Tech Lead
- [ ] Complementos do projeto conferidos, ou motivo de não se aplicar descrito no PR
```
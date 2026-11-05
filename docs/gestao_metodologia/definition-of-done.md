# LicitaRadar — Definition of Done

**Tech Lead:** Júlia Neiva Lima  
**Versão:** 1.0  
**Data:** 18/10/2026  

---

## O que é a Definition of Done
A Definition of Done (DoD) é a lista de condições que toda entrega precisa cumprir para ser considerada pronta. Ela vale para qualquer pull request que entre na branch `main`, seja código, teste, configuração ou documentação. Se um item não se aplica ao PR, o autor escreve no PR o motivo; não basta deixar em branco.

---

## Os 6 Itens Obrigatórios (Caderno - Seção 1.6)

| # | Item Obrigatório | Como verificar |
| :---: | :--- | :--- |
| **1** | **PR revisado por alguém diferente do autor e CI verde** | Aprovação no GitHub e todos os checks do GitHub Actions passando. |
| **2** | **Teste novo ligado a um critério de aceite** | O ID do caso de teste aparece no nome do teste e o critério de aceite está citado no PR. |
| **3** | **Nenhum segredo (senha, chave) no código** | Varredura de segredos do CI verde e revisão do diff. |
| **4** | **Telas com estado de carregando, vazio e erro** | Os três estados aparecem na tela, com print ou teste no PR. |
| **5** | **Logs sem dados pessoais** | Revisão dos logs novos ou alterados. |
| **6** | **Nenhum recurso novo no GCP sem ADR ou aprovação do Tech Lead** | Link do ADR ou aprovação registrada no PR. |

---

### Detalhamento dos Itens

#### 1. PR revisado por alguém diferente do autor e CI verde
- Toda mudança entra por pull request. Ninguém faz commit direto na `main`.
- Quem revisa é sempre outra pessoa que não o autor. As revisões do time são feitas pela Júlia Neiva (Tech Lead) e pela Maria Júlia Sena (PO).
- O CI precisa estar verde: lint e tipos, testes unitários, integração, contrato da API, E2E e varredura de segredos. Um PR com check vermelho não é aprovado.
- O autor responde aos comentários da revisão antes do merge.

#### 2. Teste novo ligado a um critério de aceite
- Cada funcionalidade nova ou bug corrigido vem com pelo menos um teste novo.
- O teste está ligado a um critério de aceite da história de usuário. O ID do caso de teste vai no nome, por exemplo `CT-SCORE-001`: nota é 0 quando nenhum critério é atendido.
- Em correção de bug, o teste precisa falhar antes da correção e passar depois.
- A descrição do PR cita a história e o critério de aceite que o teste cobre.

#### 3. Nenhum segredo (senha, chave) no código
- Senhas, chaves de API, tokens e strings de conexão ficam fora do código, dos testes, dos arquivos de configuração versionados e dos logs.
- No ambiente local e no CI, usar variáveis em `.env` (o repositório só tem o `.env.example`, sem valores reais). Em staging e produção, os segredos ficam no Google Secret Manager.
- Se um segredo for commitado por engano, ele é considerado vazado: avise o Tech Lead e troque a credencial. Apagar o commit não basta.

#### 4. Telas com estado de carregando, vazio e erro
- Toda tela que busca dados da API mostra os três estados: carregando (enquanto a resposta não chega), vazio (a consulta funcionou e não há resultados) e erro (a chamada falhou, com mensagem compreensível e forma de tentar de novo).
- O estado vazio explica o que fazer, por exemplo *"Nenhum edital com esses filtros. Ajuste os filtros ou o perfil"*.
- O PR traz prints dos três estados ou um teste que os exercita.

#### 5. Logs sem dados pessoais
- Os logs (em JSON) não registram dados pessoais nem sensíveis: nome, e-mail, senha, tokens, conteúdo de certidões e dados de contratos da empresa.
- Para rastrear um problema, registre identificadores internos (por exemplo, o id do usuário ou da organização) e o ID da requisição.
- Quem revisa o PR confere os logs novos ou alterados.

#### 6. Nenhum recurso novo no GCP sem ADR ou aprovação do Tech Lead
- Criar um serviço, banco, bucket, fila, agendamento ou qualquer recurso novo no Google Cloud exige um ADR que o descreva ou a aprovação do Tech Lead, registrada no PR.
- O motivo é o teto de custo de US$ 50 por equipe e os alertas de orçamento em 50%, 80% e 100%.
- Mudar o tamanho ou a configuração de um recurso existente que possa aumentar o custo segue a mesma regra.

---

## Complementos do projeto
Os itens abaixo já fazem parte do processo de qualidade do LicitaRadar e valem junto com os seis obrigatórios:
- Cobertura de linhas de pelo menos 80% nos módulos de normalização, score, prazos e certidões.
- A Precisão dos 10 primeiros resultados do feed não caiu em relação à `main`, ou a queda foi justificada no PR.
- A tarefa tinha critérios de aceite mensuráveis e prioridade validada pela PO antes de entrar em desenvolvimento (Definition of Ready).
- Nenhum bug de severidade Crítica ou Alta aberto na funcionalidade.
- Contrato da API (OpenAPI) atualizado quando a rota mudar.
- Documentação e ADRs atualizados quando a mudança alterar uma decisão técnica.

---

## Checklist para colar no pull request

```markdown
- [ ] Revisado por alguém diferente do autor e CI verde
- [ ] Teste novo ligado a um critério de aceite (ID do caso de teste citado)
- [ ] Nenhum segredo no código
- [ ] Telas com estado de carregando, vazio e erro
- [ ] Logs sem dados pessoais
- [ ] Nenhum recurso novo no GCP sem ADR ou aprovação do Tech Lead
- [ ] Complementos do projeto conferidos, ou motivo de não se aplicar descrito no PR
```
# Product Backlog — Histórias de Utilizador (MVP)

Este documento traduz os Requisitos Funcionais do LicitaRadar em Histórias de Utilizador granulares. Seguindo a abordagem ScrumXP, os épicos foram desdobrados em histórias menores e testáveis (formato BDD) para facilitar o planeamento das *Sprints* e a automação de testes.

!!! info "Como ler os critérios"
    - Cada critério tem um número ou um resultado exato, para que o QA consiga escrever o teste a partir da frase.
    - A caixa só é marcada `[x]` quando existe teste ligado ao critério na [Matriz de Rastreabilidade](../../../Testes/matriz-rastreabilidade.md) **e** o teste passa na `main`. Caixa vazia significa "ainda não comprovado", não "não feito".
    - Os papéis do sistema são **Decisor** e **Analista** (os mesmos `role` do token: `decisor` e `analista`).
    - Os valores abaixo foram alinhados ao código em 03/11/2026 (status de certidão, faixas do score, limite de upload e paginação).
    - Histórias marcadas com **Após o MVP** estão fora do [Escopo do MVP](../negocio/visao/escopo.md) e ficam como *Could have*.

---

## Épico 1: Gestão de Acessos e Hierarquia

### US01 - Registo da Empresa (Onboarding)
> **Como** Decisor, **eu quero** registar a minha empresa na plataforma, **para que** possa criar a minha conta e iniciar o uso do sistema.

!!! note "Após o MVP"
    No MVP, empresas e usuários são criados por script de seed.

**Rastreabilidade:** RF01
**Critérios de Aceitação:**

- [ ] **Dado que** preencho CNPJ válido, Nome, E-mail corporativo e Senha, **quando** submeto o formulário, **então** o sistema cria o meu utilizador com `role = decisor` e uma `empresaId` nova.
- [ ] **Dado que** informo um CNPJ com dígito verificador inválido, **quando** submeto, **então** o cadastro é recusado com a mensagem "CNPJ inválido" e nenhum registo é criado.

### US02 - Login no Sistema
> **Como** Utilizador, **eu quero** fazer login com e-mail e palavra-passe, **para que** consiga aceder à minha área de trabalho segura.

**Rastreabilidade:** RF01
**Critérios de Aceitação:**

- [ ] **Dado que** insiro credenciais incorretas, **quando** clico em Entrar, **então** o sistema exibe "E-mail ou palavra-passe incorretos", sem indicar qual dos dois está errado.
- [ ] **Dado que** fiz login com sucesso, **quando** fico 4 horas sem atividade, **então** sou redirecionado para o ecrã de login.
- [ ] **Dado que** chamo a API sem token ou com token expirado, **quando** a requisição chega, **então** a resposta é `401`.

### US03 - Recuperação de Palavra-passe
> **Como** Utilizador, **eu quero** solicitar a recuperação da minha palavra-passe, **para que** possa voltar a aceder à conta caso a esqueça.

!!! note "Após o MVP"
    No MVP, usa a recuperação de senha padrão do Firebase Auth.

**Rastreabilidade:** RF01
**Critérios de Aceitação:**

- [ ] **Dado que** insiro o meu e-mail na recuperação, **quando** submeto, **então** recebo um link de redefinição válido por 1 hora; depois de 1 hora, o link mostra "Link expirado".

### US04 - Envio de Convites para a Equipa
> **Como** Decisor, **eu quero** enviar um convite de acesso para o e-mail da minha Analista, **para que** ela integre o ambiente da empresa.

!!! note "Após o MVP"
    No MVP, a Analista é criada pelo script de seed.

**Rastreabilidade:** RF02
**Critérios de Aceitação:**

- [ ] **Dado que** insiro um e-mail válido, **quando** envio o convite, **então** o sistema gera 1 link de uso único atrelado à `empresaId` da minha empresa, válido por 7 dias.
- [ ] **Dado que** o convite já foi aceite ou passaram 7 dias, **quando** alguém abre o mesmo link, **então** vê "Convite inválido ou expirado" e nenhum utilizador é criado.
- [ ] **Dado que** sou Analista, **quando** tento enviar um convite, **então** a resposta é `403`.

### US05 - Aceitação do Convite
> **Como** Analista, **eu quero** aceitar o convite recebido por e-mail, **para que** crie a minha palavra-passe e aceda à plataforma.

!!! note "Após o MVP"
    No MVP, a Analista é criada pelo script de seed.

**Rastreabilidade:** RF02
**Critérios de Aceitação:**

- [ ] **Dado que** clico num link de convite válido, **quando** defino a minha palavra-passe, **então** o meu utilizador é criado com `role = analista` e a mesma `empresaId` de quem me convidou.

### US06 - Restrição de Acessos Sensíveis (Backend e Frontend)
> **Como** Sistema, **eu quero** bloquear a área de certidões para o perfil de Analista, **para que** o sigilo estratégico da empresa seja garantido.

**Rastreabilidade:** RF03, RNF06, ADR-0005
**Critérios de Aceitação:**

- [ ] **Dado que** uma Analista faz login, **quando** o menu é carregado, **então** a opção "Certidões" não aparece.
- [ ] **Dado que** uma Analista chama `GET /v1/certidoes`, **quando** a API responde, **então** o status é `403` com o código `ACESSO_NEGADO`.
- [ ] **Dado que** um utilizador envia `empresaId` de outra empresa na query ou no corpo, **quando** a API responde, **então** o status é `403` e nenhum dado da outra empresa é devolvido.
- [ ] **Dado que** qualquer pessoa tenta ler ou escrever direto no Firestore pelo cliente, **quando** a regra é avaliada, **então** o acesso é negado (Security Rules deny-all).

---

## Épico 2: Motor de Busca e Triagem (Integração PNCP)

### US07 - Ingestão Automática do PNCP
> **Como** Sistema, **eu quero** consumir a API do PNCP diariamente, **para que** a base de editais do LicitaRadar esteja sempre atualizada sem intervenção manual.

**Rastreabilidade:** RF04 (Backend), ADR-0007
**Critérios de Aceitação:**

- [ ] **Dado que** o workflow agendado roda 1 vez por dia, **quando** termina com sucesso, **então** os editais novos com recebimento de propostas aberto estão gravados, cada um identificado pelo número de controle do PNCP.
- [ ] **Dado que** a ingestão roda 2 vezes seguidas sobre o mesmo intervalo, **quando** a segunda termina, **então** o número de editais na base é o mesmo da primeira (0 duplicados).
- [ ] **Dado que** o PNCP responde `500`, dá timeout ou devolve JSON malformado, **quando** a coleta falha, **então** nenhum edital existente é apagado, a falha fica registada e a próxima execução retoma do ponto onde a coleta anterior parou.
- [ ] **Dado que** a coleta atinge o teto de documentos por execução, **quando** o teto é alcançado, **então** a execução para e guarda o ponto onde parou, para continuar na próxima.

### US08 - Configuração do Perfil da Empresa
> **Como** Decisor ou Analista, **eu quero** definir as palavras-chave, estados (UF), modalidades e faixa de valor, **para que** o sistema saiba que tipo de editais interessam à empresa.

**Rastreabilidade:** RF06 (Pré-requisito para o Score)
**Critérios de Aceitação:**

- [ ] **Dado que** estou no meu perfil, **quando** adiciono termos (ex.: "computador", "notebook") e guardo, **então** `GET /v1/perfil` devolve exatamente os termos guardados e o próximo carregamento do feed já usa esse perfil.
- [ ] **Dado que** informo um termo com menos de 2 caracteres, mais de 50 termos, UF repetida ou UF fora do padrão de 2 letras, **quando** guardo, **então** a API responde `400` indicando o campo inválido.
- [ ] **Dado que** informo valor mínimo maior que o máximo, **quando** guardo, **então** vejo "O valor máximo deve ser maior ou igual ao mínimo" e nada é gravado.

### US09 - Visualizar Feed Ranqueado
> **Como** Analista, **eu quero** visualizar uma lista de editais ordenada pela aderência, **para que** os mais compatíveis apareçam no topo.

**Rastreabilidade:** RF04, RF06
**Critérios de Aceitação:**

- [ ] **Dado que** abro o Feed, **quando** a lista carrega, **então** vejo no máximo 20 editais por página, ordenados do maior Score para o menor.
- [ ] **Dado que** dois editais têm o mesmo Score, **quando** a lista é ordenada, **então** o desempate é: prazo mais próximo, depois publicação mais recente, depois id.
- [ ] **Dado que** existem mais de 20 editais, **quando** avanço a página, **então** recebo os próximos 20 sem repetir nenhum da página anterior.
- [ ] **Dado que** a base local tem o golden set carregado, **quando** a primeira página é pedida, **então** o p95 do tempo de resposta da API é de no máximo 800 ms.
- [ ] **Dado que** nenhum edital passa no perfil, **quando** o Feed carrega, **então** vejo o estado vazio com orientação para ajustar o perfil.

### US10 - Aplicar Filtros Dinâmicos
> **Como** Analista, **eu quero** filtrar os resultados por UF, modalidade, data e valor, **para que** encontre editais específicos sem perder a ordenação de Score.

**Rastreabilidade:** RF05
**Critérios de Aceitação:**

- [ ] **Dado que** seleciono a UF "SP", **quando** aplico o filtro, **então** 100% dos editais exibidos são de SP e a ordem por Score se mantém.
- [ ] **Dado que** combino UF e faixa de valor, **quando** aplico os dois, **então** só aparecem editais que cumprem ambos.
- [ ] **Dado que** informo valor mínimo maior que o máximo no filtro, **quando** aplico, **então** vejo "O máximo deve ser maior ou igual ao mínimo" e o feed não é recarregado.
- [ ] **Dado que** apliquei filtros, **quando** recarrego a página ou uso "voltar" do navegador, **então** os mesmos filtros continuam aplicados (estado guardado na URL).

### US11 - Cálculo Determinístico do Score
> **Como** Sistema, **eu quero** calcular o Score de Aderência (0 a 100), **para que** o edital possa ser classificado como Alta, Média ou Baixa compatibilidade.

**Rastreabilidade:** RF06, ADR-0008, [Regra do Score](../negocio/Regradenegocio/score.md)
**Critérios de Aceitação:**

- [ ] **Dado que** um edital é avaliado, **quando** o Score é calculado, **então** a nota é um número inteiro entre 0 e 100.
- [ ] **Dado que** a nota foi calculada, **quando** é classificada, **então** fica em Alta se for de 80 a 100, Média se for de 50 a 79 e Baixa se for de 0 a 49.
- [ ] **Dado que** um edital não tem nenhuma palavra-chave do perfil, **quando** é avaliado, **então** é descartado e não aparece no Feed.
- [ ] **Dado que** o valor do edital está mais de 10% fora da faixa do perfil, **quando** é avaliado, **então** é descartado.
- [ ] **Dado que** o mesmo edital e o mesmo perfil são avaliados 2 vezes, **quando** comparo as notas, **então** são idênticas.
- [ ] **Dado que** existe a tabela de 10 casos de teste do Score, incluindo valor igual ao limite da faixa e limite + R$ 0,01, **quando** a suíte roda, **então** os 10 casos passam.

### US12 - Acesso a Detalhes e Anexos
> **Como** Analista, **eu quero** abrir o detalhe do edital, **para que** possa ler a descrição completa e chegar ao documento original.

**Rastreabilidade:** RF04
**Critérios de Aceitação:**

- [ ] **Dado que** clico num edital do feed, **quando** o detalhe abre, **então** vejo objeto, órgão, UF, modalidade, valor estimado, data de encerramento e o Score com a sua faixa.
- [ ] **Dado que** o detalhe está aberto, **quando** clico em "Ver no PNCP", **então** a página original do edital no portal abre numa nova aba.
- [ ] **Dado que** o edital não tem valor estimado, **quando** o detalhe abre, **então** o campo mostra "Não informado" em vez de R$ 0,00.

### US13 - Recomendar Edital
> **Como** Analista, **eu quero** marcar um edital promissor como "Recomendado", **para que** ele seja enviado para a análise do Decisor.

!!! note "Após o MVP"
    Faz parte do fluxo de decisão, substituído no MVP por "Acompanhar edital" (US20). Ver o [Escopo do MVP](../negocio/visao/escopo.md).

**Rastreabilidade:** RF07
**Critérios de Aceitação:**

- [ ] **Dado que** clico na "Estrela" de um edital, **quando** a ação é registada, **então** esse edital aparece no Dashboard do Decisor em até 5 segundos.

---

## Épico 3: Gestão Documental e Conformidade

### US14 - Upload de Novas Certidões
> **Como** Decisor, **eu quero** fazer o upload de certidões informando a data de validade, **para que** a documentação fique guardada na nuvem.

**Rastreabilidade:** RF08, RF09
**Critérios de Aceitação:**

- [ ] **Dado que** anexo um PDF, JPG ou PNG de até 5 MB e informo uma data de validade, **quando** submeto, **então** o documento aparece na lista de certidões.
- [ ] **Dado que** anexo um arquivo de 5 MB + 1 byte, **quando** submeto, **então** o upload é recusado com a mensagem de limite de 5 MB.
- [ ] **Dado que** anexo um arquivo cujo conteúdo não é PDF, JPG nem PNG (mesmo com a extensão trocada), **quando** submeto, **então** o upload é recusado.
- [ ] **Dado que** tento gravar sem a "Data de validade", **quando** clico em Guardar, **então** a ação é bloqueada com erro no campo.

### US15 - Atualização de Certidão (Renovação)
> **Como** Decisor, **eu quero** substituir uma certidão prestes a vencer por uma nova, **para que** o alerta desapareça.

**Rastreabilidade:** RF08, RF10
**Critérios de Aceitação:**

- [ ] **Dado que** submeto uma certidão renovada, **quando** o *upload* conclui, **então** o arquivo anterior fica inativo, a nova data de validade passa a valer e o status é recalculado na mesma resposta.

### US16 - Alertas Visuais de Vencimento
> **Como** Decisor, **eu quero** receber um alerta destacado para certidões próximas do vencimento, **para que** evite inabilitações em processos.

**Rastreabilidade:** RF10
**Critérios de Aceitação (dias de calendário, fuso de Brasília):**

- [ ] **Dado que** faltam de 0 a 15 dias para o vencimento, **quando** entro no painel, **então** a certidão aparece como "Urgente".
- [ ] **Dado que** faltam de 16 a 30 dias, **quando** entro no painel, **então** a certidão aparece como "Atenção".
- [ ] **Dado que** faltam 31 dias ou mais, **quando** entro no painel, **então** a certidão aparece como "Em dia", sem alerta.
- [ ] **Dado que** a validade era ontem, **quando** entro no painel, **então** a certidão aparece como "Vencida".
- [ ] **Dado que** são 23:59:59 e depois 00:00:00 (horário de Brasília), **quando** consulto a mesma certidão, **então** os dias restantes diminuem em 1 exatamente na virada do dia.

---

## Épico 4: Fluxo de Decisão (após o MVP)

!!! note "Após o MVP"
    Este épico ficou fora do MVP. No lugar dele entra "Acompanhar edital" (US20), no Épico 5. Ver o [Escopo do MVP](../negocio/visao/escopo.md).

### US17 - Dashboard de Decisão
> **Como** Decisor, **eu quero** aceder a um painel consolidado apenas com os editais recomendados, **para que** não perca tempo a analisar ruído.

**Rastreabilidade:** RF11
**Critérios de Aceitação:**

- [ ] **Dado que** entro no Dashboard, **quando** a lista carrega, **então** 100% dos editais exibidos foram marcados com a "Estrela" pela Analista (US13) e nenhum outro aparece.

### US18 - Aprovar Edital para Disputa
> **Como** Decisor, **eu quero** aprovar um edital recomendado, **para que** ele avance para a fase de preparação de proposta.

**Rastreabilidade:** RF12
**Critérios de Aceitação:**

- [ ] **Dado que** aprovo uma recomendação, **quando** confirmo, **então** o edital sai de "Recomendados" e aparece em "Em Disputa", com a data e o autor da aprovação.

### US19 - Descartar Oportunidade
> **Como** Decisor, **eu quero** descartar um edital recomendado, **para que** a equipa saiba que não vamos concorrer.

**Rastreabilidade:** RF12
**Critérios de Aceitação:**

- [ ] **Dado que** clico em "Descartar", **quando** a ação é gravada, **então** o edital sai da minha fila e a Analista vê o status "Descartado" no mesmo edital.

---

## Épico 5: Acompanhamento e Alertas

### US20 - Acompanhar Edital
> **Como** Analista ou Decisor, **eu quero** marcar um edital do feed como de interesse e atualizar a sua etapa, **para que** a equipa saiba o que está em andamento e não perca prazos.

!!! warning "Entra no MVP se estiver em PR até 15/11/2026"
    Prioridade *Should have*. A ingestão do PNCP, a tela de perfil e a ligação do front à API vêm antes. Se não houver PR aberto até 15/11, a história sai do MVP. Ver o [Escopo do MVP](../negocio/visao/escopo.md).

**Rastreabilidade:** [Escopo do MVP](../negocio/visao/escopo.md)
**Critérios de Aceitação (prazo em dias de calendário, fuso de Brasília, como na US16):**

- [ ] **Dado que** clico em "Acompanhar" num edital do feed, **quando** a ação é gravada, **então** o edital aparece na lista "Acompanhados" com a etapa "Interessado".
- [ ] **Dado que** o edital já está acompanhado, **quando** clico em "Acompanhar" de novo, **então** a API responde `200` com o registo existente e a lista continua com 1 único item para esse edital.
- [ ] **Dado que** sou Analista ou Decisor da empresa, **quando** mudo a etapa, **então** só posso avançar uma etapa por vez (Interessado → Preparando → Enviado → Resultado) ou voltar de Preparando para Interessado, e a lista mostra a nova etapa.
- [ ] **Dado que** o edital está em Enviado ou Resultado, **quando** tento voltar para uma etapa anterior ou saltar etapas, **então** a API responde `422` e a etapa não muda.
- [ ] **Dado que** levo o edital para Resultado, **quando** gravo, **então** tenho de escolher "Venceu" ou "Não venceu", e a etapa não pode mais ser alterada.
- [ ] **Dado que** o edital está em Interessado ou Preparando, **quando** clico em "Deixar de acompanhar", **então** ele sai da lista e pode ser acompanhado de novo; em Enviado ou Resultado a opção não aparece.
- [ ] **Dado que** a lista "Acompanhados" carrega, **quando** há editais, **então** eles vêm ordenados pelo prazo mais próximo e:
    - faltam de 0 a 3 dias: aparece o destaque "Prazo próximo";
    - faltam 4 dias ou mais: sem destaque;
    - o prazo já passou e o edital está em Interessado ou Preparando: aparece "Prazo encerrado" e o edital vai para o fim da lista.
- [ ] **Dado que** sou de outra empresa, **quando** tento ler ou mudar um edital acompanhado, **então** a API responde `403`.
- [ ] **Dado que** o edital ou o acompanhamento não existe, **quando** faço qualquer ação sobre ele, **então** a API responde `404`.
- [ ] **Dado que** chamo qualquer rota de acompanhamento sem token ou com token inválido/expirado, **quando** a requisição chega, **então** a resposta é `401`.

### US21 - Contador de Editais Novos
> **Como** Analista ou Decisor, **eu quero** ver quantos editais novos entraram no meu feed desde a última vez que o abri, **para que** saiba se vale a pena revisar o feed hoje.

!!! note "Após o MVP"
    No MVP, o alerta dentro da plataforma é o selo de vencimento das certidões (US16). O contador depende da ingestão do PNCP e de guardar a última visita de cada utilizador, e fica como *Could have*. Ver o [Escopo do MVP](../negocio/visao/escopo.md).

**Rastreabilidade:** RF10, [Escopo do MVP](../negocio/visao/escopo.md)
**Critérios de Aceitação (o contador é por utilizador; "última visita" = última vez que abri o Feed):**

- [ ] **Dado que** a ingestão gravou editais aderentes ao meu perfil depois da última vez que abri o Feed, **quando** abro a plataforma, **então** vejo "N editais novos" no menu do Feed; abrir só a plataforma não zera o contador.
- [ ] **Dado que** abri o feed, **quando** volto ao menu, **então** o contador volta a zero.
- [ ] **Dado que** é o meu primeiro acesso, **quando** abro a plataforma, **então** N é igual ao total de editais aderentes ao perfil da empresa.
- [ ] **Dado que** a Analista e o Decisor são da mesma empresa, **quando** a Analista abre o Feed, **então** o contador do Decisor não muda.
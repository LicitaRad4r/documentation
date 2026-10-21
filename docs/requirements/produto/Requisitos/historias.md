# Product Backlog — Histórias de Utilizador (MVP)

Este documento traduz os Requisitos Funcionais do LicitaRadar em Histórias de Utilizador (User Stories) focadas na entrega de valor para as nossas personas principais: o **Decisor (Bruno)** e a **Analista (Mariana)**.

---

## Épico 1: Gestão de Acessos e Hierarquia

### US01 - Autenticação no Sistema
> **Como** Decisor ou Analista, **eu quero** fazer login na plataforma utilizando o meu e-mail corporativo e palavra-passe, **para que** consiga aceder à minha área de trabalho segura.

**Critérios de Aceitação:**
- [x] O sistema deve validar se o e-mail tem um formato correto.
- [x] O sistema deve exibir erro descritivo para credenciais inválidas.
- [x] Deve existir um fluxo funcional de "Esqueci-me da palavra-passe".
- [x] A sessão deve expirar automaticamente por inatividade (segurança).

### US02 - Gestão de Convites para a Equipa
> **Como** Decisor, **eu quero** enviar um convite de acesso para o e-mail da minha Analista, **para que** ela possa entrar na plataforma associada à conta da nossa organização.

**Critérios de Aceitação:**
- [x] O Administrador deve conseguir inserir um e-mail num campo de convite.
- [x] O sistema deve disparar um e-mail com um link único de registo.
- [x] O utilizador convidado entra automaticamente com o perfil "Analista" associado à empresa matriz.

### US03 - Restrição de Acessos Sensíveis
> **Como** Decisor, **eu quero** que a área de certidões e dados financeiros seja bloqueada para os perfis de Analista, **para que** eu garanta o sigilo das informações estratégicas da empresa.

**Critérios de Aceitação:**
- [x] A aba de "Documentação Legal" não deve renderizar no menu lateral para o perfil "Analista".
- [x] Se um "Analista" tentar aceder ao URL direto das certidões, deve ser redirecionado para uma página de "Acesso Negado (403)".

---

## Épico 2: Motor de Busca e Triagem (Integração PNCP)

### US04 - Visualizar Feed de Oportunidades

> **Como** Analista, **eu quero** visualizar uma lista de editais abertos do PNCP, ordenada pela aderência ao perfil da minha empresa, **para que** não tenha de procurar manualmente em dezenas de diários oficiais.

**Rastreabilidade:** RF04, RF06, ADR-0007, ADR-0008

**Critérios de Aceitação:**

- [ ] O feed só é acessível a utilizador autenticado e mostra apenas dados da organização do utilizador.
- [ ] Enquanto a lista é carregada, o ecrã exibe um estado de carregamento.
- [ ] O feed lista os editais já ingeridos e armazenados. Abrir o feed não faz chamadas ao PNCP.
- [ ] A lista vem ordenada do maior para o menor Score de Aderência (US06). Em caso de empate, vale o prazo limite mais próximo, depois a publicação mais recente e, por último, o ID do edital.
- [ ] Editais descartados pelo perfil da empresa (funil eliminatório) não aparecem no feed.
- [ ] Cada item (card) mostra: Título, Órgão, Modalidade, Prazo, Valor Estimado, Score (0–100) e faixa (Alta, Média ou Baixa).
- [ ] A lista é paginada (ou usa scroll infinito), sem carregar todos os editais de uma só vez.
- [ ] Se não existirem editais ingeridos, o ecrã mostra um estado vazio a informar isso.
- [ ] Se existirem editais, mas todos forem descartados pelo perfil, o ecrã mostra um estado vazio diferente, a orientar a rever os termos-chave e a faixa de valor do perfil.
- [ ] Se o carregamento falhar, o ecrã mostra uma mensagem de erro amigável com a opção "Tentar novamente". O erro nunca aparece como estado vazio.

### US05 - Filtrar Editais
> **Como** Analista, **eu quero** filtrar os editais por localização, valor e palavra-chave, **para que** encontre rapidamente as oportunidades que a minha PME tem capacidade operacional de atender.

**Rastreabilidade:** RF05, ADR-0003, página Filtros

**Critérios de Aceitação:**

- [ ] Existem campos de filtro para Palavra-chave, UF, Município, Modalidade, Datas e Valor Estimado.
- [ ] Os filtros só restringem o feed: não recalculam o Score, mantêm a ordenação por Score e não devolvem editais já descartados pelo perfil.
- [ ] O feed atualiza os resultados de forma dinâmica ao aplicar os filtros, com paginação e processamento no servidor.
- [ ] Enquanto os resultados são carregados, o ecrã exibe um estado de carregamento.
- [ ] A pesquisa respeita a meta de desempenho do projeto (p95 de leitura ≤ 800 ms).
- [ ] Se nenhum edital corresponder aos filtros aplicados, o ecrã mostra um estado vazio que informa que não há resultados para aquela combinação e oferece a ação "Limpar filtros".
- [ ] Este estado vazio é diferente dos estados vazios da US04 (sem editais ingeridos e todos descartados pelo perfil).
- [ ] Se a pesquisa falhar, o ecrã mostra uma mensagem de erro amigável com a opção "Tentar novamente", e os filtros aplicados mantêm-se.

### US06 - Calcular Score de Aderência

> **Como** Analista, **eu quero** ver uma pontuação de aderência em cada edital, **para que** saiba de imediato quais as licitações mais compatíveis com o perfil da nossa empresa.

**Rastreabilidade:** RF06, ADR-0008, páginas Score de Aderência e Filtros

**Critérios de Aceitação:**

*Exibição*

- [ ] Cada edital exibe um badge com o Score (nota inteira de 0 a 100) e a faixa correspondente: Alta (80 a 100), Média (50 a 79) ou Baixa (0 a 49). O texto da faixa aparece no badge, não apenas a cor.

*Funil eliminatório*

- [ ] O edital é descartado (sem Score e fora do feed) se falhar em qualquer um destes critérios do perfil:

    - nenhum termo-chave aparece no objeto do edital;
    - a empresa definiu uma categoria PNCP e o edital tem outra (se o edital não tem categoria, a restrição é ignorada);
    - restam menos dias úteis do que o prazo mínimo do perfil (padrão: 3), ou o prazo já expirou;
    - a empresa definiu modalidades e a do edital não está entre as aceites;
    - o valor está mais de 10% fora da faixa configurada.

*Pontuação (máximo de 100 pontos)*

- [ ] Termos-chave (peso 40): 40 pontos para termo exato ou expressão completa; 20 pontos para correspondência parcial.
- [ ] Localidade (peso 25): 25 pontos se a UF do órgão está no perfil; 0 caso contrário. O edital fora das UFs do perfil não é descartado.
- [ ] Valor estimado (peso 15): 15 pontos dentro da faixa; 5 pontos até 10% fora; 0 pontos se o valor não foi informado (o edital não é descartado).
- [ ] Prazo (peso 10): 10 pontos com 8 dias úteis ou mais; 5 pontos com 4 a 7; 0 pontos com menos de 4.
- [ ] Modalidade (peso 10): 10 pontos se é a prioritária; 5 pontos se é outra modalidade aceite.

*Reescala*

- [ ] UF, valor e modalidade são opcionais. Os que a empresa não configurou saem do cálculo, e a nota é reescalada: Score = arredondar(100 × pontos obtidos ÷ soma dos pesos dos critérios configurados).

*Correspondência dos termos-chave*

- [ ] A comparação ignora maiúsculas, minúsculas e acentos, e aceita plural simples.
- [ ] Termos curtos só correspondem como palavra inteira (a sigla "TI" não corresponde a "atividade").

*Comportamento do cálculo*

- [ ] O cálculo é determinístico: o mesmo perfil e o mesmo edital geram sempre o mesmo Score.
- [ ] O Score é calculado por empresa. Perfis diferentes geram notas diferentes para o mesmo edital, e uma empresa nunca vê o Score de outra.
- [ ] O Score é recalculado quando entram novos editais, quando o perfil é editado e numa tarefa diária à meia-noite (a pontuação de prazo muda com o passar dos dias).
- [ ] Os filtros rápidos da interface não recalculam o Score; apenas restringem o que é exibido.

### US07 - Sinalizar Edital Recomendado
> **Como** Analista, **eu quero** marcar um edital promissor como "Recomendado", **para que** ele seja enviado diretamente para o painel de aprovação do Decisor.

**Critérios de Aceitação:**
- [x] Deve existir um botão de ação (ex: ícone de estrela ou "Recomendar") em cada edital.
- [x] Ao clicar, o estado do edital no banco de dados deve ser alterado e refletido visualmente para a Analista.

---

## Épico 3: Gestão Documental e Conformidade

### US08 - Repositório de Certidões
> **Como** Decisor, **eu quero** fazer o upload das certidões legais da empresa, **para que** tenha os documentos centralizados e seguros para qualquer disputa.

**Critérios de Aceitação:**
- [x] O sistema deve aceitar upload de ficheiros em formato PDF, JPG e PNG (máx. 5MB).
- [x] O ficheiro deve ser guardado de forma segura, com o link associado à base de dados da empresa.

### US09 - Registo de Validade dos Documentos
> **Como** Decisor, **eu quero** inserir a data de validade no momento do upload de uma certidão, **para que** o sistema saiba quando o documento vai expirar.

**Critérios de Aceitação:**
- [x] O formulário de upload deve ter um campo obrigatório de "Data de Validade".
- [x] Não deve ser possível inserir uma data que já se encontre no passado no momento do registo inicial.

### US10 - Alerta de Vencimento de Certidões
> **Como** Decisor, **eu quero** receber um alerta quando uma certidão estiver prestes a expirar, **para que** tenha tempo de renovar o documento e evite a inabilitação num edital.

**Rastreabilidade:** RF08, RF09, RF10, US03, US08, US09

**Critérios de Aceitação:**

*Deteção e exibição*

- [ ] A verificação de datas corre automaticamente todos os dias às 00h00.
- [ ] O painel de alertas mostra as certidões vencidas ou a 30 dias ou menos do vencimento, das mais urgentes para as menos urgentes.
- [ ] Cada alerta mostra o nome da certidão, a data de validade e os dias restantes.
- [ ] O sistema exibe uma etiqueta visual vermelha (🔴) para documentos a 15 dias ou menos do vencimento, e amarela (🟡) para documentos entre 16 e 30 dias. Certidões já vencidas exibem a etiqueta "Vencida". A etiqueta traz texto, não apenas cor.
- [ ] Se nenhuma certidão estiver em alerta, o painel mostra uma mensagem a informar que todas as certidões estão em dia.
- [ ] O painel de alertas só é visível para o Decisor (US03).

*Ação: renovar a certidão*

- [ ] Cada alerta tem a ação "Renovar certidão".
- [ ] A ação abre um formulário para enviar o novo ficheiro (PDF, JPG ou PNG, máx. 5MB) e informar a nova data de validade, que é obrigatória.
- [ ] A nova data de validade tem de ser futura e posterior à data da certidão atual. Caso contrário, o formulário mostra um erro descritivo e não grava.
- [ ] Ao gravar, o novo ficheiro passa a ser a versão vigente da certidão e a etiqueta é recalculada de imediato, sem esperar pela verificação das 00h00.
- [ ] Se a nova data ainda estiver a 30 dias ou menos, o alerta continua com a etiqueta correspondente. Caso contrário, o alerta desaparece do painel.
- [ ] Se o envio falhar, o ecrã mostra uma mensagem de erro amigável com a opção "Tentar novamente", e a certidão anterior mantém-se inalterada.

---

## Épico 4: Fluxo de Decisão

### US11 - Dashboard de Decisão
> **Como** Decisor, **eu quero** aceder a um painel consolidado com as oportunidades selecionadas pela minha equipa, **para que** possa focar apenas nos editais com real potencial de ganho.

**Critérios de Aceitação:**
- [x] O Dashboard principal do Administrador deve carregar apenas editais marcados como "Recomendados" pela Analista.
- [x] A interface deve permitir a leitura do resumo e acesso rápido aos anexos do edital.

### US12 - Aprovação e Descarte de Oportunidades
> **Como** Decisor, **eu quero** aprovar ou descartar um edital recomendado, **para que** a equipa saiba exatamente quais os processos que vão avançar para a fase de lances.

**Critérios de Aceitação:**
- [x] Devem existir dois botões em cada edital do Dashboard: "Aprovar para Disputa" e "Descartar".
- [x] Ao clicar em "Descartar", o edital é removido da fila principal.
- [x] Ao clicar em "Aprovar", o estado muda para "Em Disputa" e o edital passa para o repositório de oportunidades ganhas/em andamento.
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
> **Como** Analista, **eu quero** visualizar uma lista de editais abertos importados do PNCP, **para que** não tenha de procurar manualmente em dezenas de diários oficiais.

**Critérios de Aceitação:**
- [x] A lista deve carregar por padrão os editais mais recentes.
- [x] Cada item (card) da lista deve mostrar: Título, Órgão, Modalidade, Prazo e Valor Estimado.
- [x] Deve existir paginação ou *scroll* infinito para não sobrecarregar o ecrã.

### US05 - Filtrar Editais
> **Como** Analista, **eu quero** filtrar os editais por localização, valor e palavra-chave, **para que** encontre rapidamente as oportunidades que a minha PME tem capacidade operacional de atender.

**Critérios de Aceitação:**
- [x] Devem existir campos de filtro para Palavra-chave, UF, Município, Modalidade, Datas e Valor Estimado.
- [x] O feed deve atualizar os resultados de forma dinâmica ao aplicar os filtros.
- [x] O sistema deve devolver os resultados da pesquisa em menos de 2 segundos.

### US06 - Calcular Score de Aderência
> **Como** Analista, **eu quero** ver uma pontuação de aderência em cada edital, **para que** saiba de imediato quais as licitações mais compatíveis com o nosso histórico e capacidade.

**Critérios de Aceitação:**
- [x] O sistema deve exibir uma *tag* ou *badge* com um Score (ex: 0 a 100% ou Alto/Médio/Baixo).
- [x] O cálculo deve considerar as palavras-chave previamente guardadas nas preferências da empresa.

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

**Critérios de Aceitação:**
- [x] A verificação de datas deve correr automaticamente todos os dias às 00h00.
- [x] O sistema deve exibir uma etiqueta visual vermelha (🔴) para documentos a 15 dias ou menos do vencimento.
- [x] O sistema deve exibir uma etiqueta visual amarela (🟡) para documentos entre 16 e 30 dias do vencimento.

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
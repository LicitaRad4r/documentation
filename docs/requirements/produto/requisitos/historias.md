# Product Backlog — Histórias de Utilizador (MVP)

Este documento traduz os Requisitos Funcionais do LicitaRadar em Histórias de Utilizador granulares. Seguindo a abordagem ScrumXP, os épicos foram desdobrados em histórias menores e testáveis (formato BDD) para facilitar o planeamento das *Sprints* e a automação de testes.

---

## Épico 1: Gestão de Acessos e Hierarquia

### US01 - Registo da Empresa (Onboarding)
> **Como** Decisor, **eu quero** registar a minha empresa na plataforma, **para que** possa criar a minha conta de Administrador e iniciar o uso do sistema.

**Rastreabilidade:** RF01
**Critérios de Aceitação:**
- [x] **Dado que** preencho o CNPJ, Nome, E-mail corporativo e Senha, **quando** submeto o formulário, **então** o sistema cria o meu perfil com a *role* "Administrador" e cria o `tenant_id` da empresa.

### US02 - Login no Sistema
> **Como** Utilizador, **eu quero** fazer login com e-mail e palavra-passe, **para que** consiga aceder à minha área de trabalho segura.

**Rastreabilidade:** RF01
**Critérios de Aceitação:**
- [x] **Dado que** insiro credenciais incorretas, **quando** clico em Entrar, **então** o sistema exibe "E-mail ou palavra-passe incorretos".
- [x] **Dado que** faço login com sucesso, **quando** a sessão expira (4h de inatividade), **então** sou redirecionado para o ecrã de login.

### US03 - Recuperação de Palavra-passe
> **Como** Utilizador, **eu quero** solicitar a recuperação da minha palavra-passe, **para que** possa voltar a aceder à conta caso a esqueça.

**Rastreabilidade:** RF01
**Critérios de Aceitação:**
- [x] **Dado que** insiro o meu e-mail na recuperação, **quando** submeto, **então** recebo um link com validade de 1 hora para redefinir a palavra-passe.

### US04 - Envio de Convites para a Equipa
> **Como** Decisor, **eu quero** enviar um convite de acesso para o e-mail da minha Analista, **para que** ela integre o ambiente da empresa.

**Rastreabilidade:** RF02
**Critérios de Aceitação:**
- [x] **Dado que** insiro o e-mail da minha funcionária, **quando** envio o convite, **então** o sistema gera um link único atrelado ao `tenant_id` da minha empresa.

### US05 - Aceitação do Convite
> **Como** Analista, **eu quero** aceitar o convite recebido por e-mail, **para que** crie a minha palavra-passe e aceda à plataforma.

**Rastreabilidade:** RF02
**Critérios de Aceitação:**
- [x] **Dado que** clico no link de convite, **quando** defino a minha palavra-passe, **então** o meu utilizador é criado com a *role* "Analista" na mesma empresa de quem me convidou.

### US06 - Restrição de Acessos Sensíveis (Backend e Frontend)
> **Como** Sistema, **eu quero** bloquear a área de certidões e dados financeiros para o perfil de Analista, **para que** o sigilo estratégico da empresa seja garantido.

**Rastreabilidade:** RF03, RNF06, ADR-0005
**Critérios de Aceitação:**
- [x] **Dado que** uma Analista faz login, **quando** o menu é carregado, **então** a opção "Documentação Legal" fica oculta.
- [x] **Dado que** uma Analista força o acesso via URL (`/certidoes`), **quando** a página tenta carregar, **então** ocorre um redirecionamento automático exibindo Erro 403.

---

## Épico 2: Motor de Busca e Triagem (Integração PNCP)

### US07 - Ingestão Automática do PNCP
> **Como** Sistema, **eu quero** consumir a API do PNCP diariamente, **para que** a base de editais do LicitaRadar esteja sempre atualizada sem intervenção manual.

**Rastreabilidade:** RF04 (Backend)
**Critérios de Aceitação:**
- [x] **Dado que** é 02h00 da manhã, **quando** o *job* é ativado, **então** o sistema recolhe novos editais do PNCP e grava-os no banco de dados.

### US08 - Configuração do Perfil da Empresa
> **Como** Decisor ou Analista, **eu quero** definir as palavras-chave, estados (UF) e faixa de valor, **para que** o sistema saiba que tipo de editais interessam à empresa.

**Rastreabilidade:** RF06 (Pré-requisito para o Score)
**Critérios de Aceitação:**
- [x] **Dado que** estou no meu perfil, **quando** adiciono os termos (ex: "computador", "notebook"), **então** o motor de busca passa a usar estes dados para calcular o Score.

### US09 - Visualizar Feed Ranqueado
> **Como** Analista, **eu quero** visualizar uma lista de editais ordenada pela aderência, **para que** os mais compatíveis apareçam no topo.

**Rastreabilidade:** RF04, RF06
**Critérios de Aceitação:**
- [x] **Dado que** abro o Feed, **quando** a lista carrega, **então** os editais vêm ordenados do maior Score para o menor.
- [x] **Dado que** faço *scroll* no fim da página, **quando** atinjo o limite, **então** a paginação automática traz os próximos 20 editais.

### US10 - Aplicar Filtros Dinâmicos
> **Como** Analista, **eu quero** filtrar os resultados por localização e data, **para que** encontre editais específicos sem perder a ordenação de Score.

**Rastreabilidade:** RF05
**Critérios de Aceitação:**
- [x] **Dado que** seleciono a UF "SP", **quando** aplico o filtro, **então** o Feed exibe apenas editais de São Paulo, mantendo as notas de aderência.

### US11 - Cálculo Determinístico do Score
> **Como** Sistema, **eu quero** calcular o Score de Aderência (0 a 100), **para que** o edital possa ser classificado como Alta, Média ou Baixa compatibilidade.

**Rastreabilidade:** RF06, ADR-0008
**Critérios de Aceitação:**
- [x] **Dado que** um edital não tem NENHUMA palavra-chave do perfil, **quando** é avaliado, **então** é sumariamente descartado e oculto do Feed.
- [x] **Dado que** a pontuação é calculada, **quando** atinge 85 pontos, **então** recebe o *badge* "Alta Aderência".

### US12 - Acesso a Detalhes e Anexos
> **Como** Analista, **eu quero** abrir o detalhe do edital, **para que** possa ler a descrição completa e descarregar o PDF do edital.

**Rastreabilidade:** RF04
**Critérios de Aceitação:**
- [x] **Dado que** clico num edital do feed, **quando** o modal/página abre, **então** vejo o link direto para descarregar o documento original do portal governamental.

### US13 - Recomendar Edital
> **Como** Analista, **eu quero** marcar um edital promissor como "Recomendado", **para que** ele seja enviado para a análise do Decisor.

**Rastreabilidade:** RF07
**Critérios de Aceitação:**
- [x] **Dado que** clico na "Estrela" de um edital, **quando** a ação é registada, **então** esse edital aparece no Dashboard do Decisor.

---

## Épico 3: Gestão Documental e Conformidade

### US14 - Upload de Novas Certidões
> **Como** Decisor, **eu quero** fazer o upload de certidões informando a data de validade, **para que** a documentação fique guardada na nuvem.

**Rastreabilidade:** RF08, RF09
**Critérios de Aceitação:**
- [x] **Dado que** anexo um PDF de 4MB e informo a data de validade futura, **quando** submeto, **então** o documento fica visível na lista de certidões.
- [x] **Dado que** tento gravar sem preencher a "Data de validade", **quando** clico em Guardar, **então** a ação é bloqueada com erro.

### US15 - Atualização de Certidão (Renovação)
> **Como** Decisor, **eu quero** substituir uma certidão prestes a vencer por uma nova, **para que** o alerta desapareça.

**Rastreabilidade:** RF08, RF10
**Critérios de Aceitação:**
- [x] **Dado que** submeto uma certidão renovada, **quando** o *upload* conclui, **então** o ficheiro anterior é inativado e a nova data de validade assume o controlo dos alertas.

### US16 - Alertas Visuais de Vencimento
> **Como** Decisor, **eu quero** receber um alerta destacado para certidões próximas do vencimento, **para que** evite inabilitações em processos.

**Rastreabilidade:** RF10
**Critérios de Aceitação:**
- [x] **Dado que** faltam 12 dias para o vencimento de uma certidão, **quando** entro no painel, **então** vejo a etiqueta vermelha 🔴 "Urgente / A Vencer".
- [x] **Dado que** uma certidão venceu ontem, **quando** verifico o painel, **então** vejo a indicação de que está "Expirada".

---

## Épico 4: Fluxo de Decisão

### US17 - Dashboard de Decisão
> **Como** Decisor, **eu quero** aceder a um painel consolidado apenas com os editais recomendados, **para que** não perca tempo a analisar ruído.

**Rastreabilidade:** RF11
**Critérios de Aceitação:**
- [x] **Dado que** entro na plataforma, **quando** vejo a Home, **então** só visualizo os editais marcados com a "Estrela" pela Analista (US13).

### US18 - Aprovar Edital para Disputa
> **Como** Decisor, **eu quero** aprovar um edital recomendado, **para que** ele avance para a fase de preparação de proposta.

**Rastreabilidade:** RF12
**Critérios de Aceitação:**
- [x] **Dado que** aprovo uma recomendação, **quando** confirmo, **então** o edital move-se para a aba "Em Disputa".

### US19 - Descartar Oportunidade
> **Como** Decisor, **eu quero** descartar um edital recomendado, **para que** a equipa saiba que não vamos concorrer.

**Rastreabilidade:** RF12
**Critérios de Aceitação:**
- [x] **Dado que** clico em "Descartar", **quando** a ação é gravada, **então** o edital sai da minha fila visual e a Analista é sinalizada da recusa.
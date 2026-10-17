# Cálculo do Score de Aderência

## Resumo

O Score de Aderência é o motor de inteligência do LicitaRadar. Ele usa os filtros do Perfil de Fornecedor para eliminar os editais fora do perfil e ranquear os demais com uma nota de 0 a 100, poupando o tempo gasto em triagem manual e destacando as oportunidades mais lucrativas. Os filtros estão descritos em [Filtros e Configuração de Perfil](filtros.md).

---

## 1. Princípios

- **Determinístico:** a nota depende apenas de três entradas: o perfil da empresa, o edital e a **data de referência** do cálculo. As mesmas três entradas sempre produzem a mesma nota.
- **Explicável:** a nota é a soma de critérios simples, e o detalhamento de cada um é guardado junto com o resultado.
- **Barato:** não usa IA nem leitura de PDFs, só os campos do edital disponibilizados pela API do PNCP (objeto, órgão, UF, modalidade, valor estimado, datas e categoria).
- **Configurável em um só lugar:** pesos, faixas e limiares ficam como constantes de configuração, para poder calibrá-los depois da demonstração sem reescrever a lógica.

---

## 2. Lógica de cálculo (MVP)

O cálculo tem três passos: um funil eliminatório, a pontuação por pesos e o ajuste para critérios não configurados.

### Passo 1: funil eliminatório

Antes de pontuar, o edital precisa passar por critérios excludentes. Se falhar em algum, ele é **descartado** e não aparece no feed:

| Critério | O edital é descartado quando... |
| :--- | :--- |
| **Termos-chave** | Nenhum termo do perfil corresponde ao edital |
| **Categoria (PNCP)** | A empresa selecionou uma categoria e a do edital é diferente (editais sem categoria não são descartados) |
| **Prazo** | O prazo restante é menor que o prazo mínimo do perfil (padrão: 3 dias úteis), ou já expirou |
| **Modalidade** | A empresa configurou modalidades e a do edital não está entre as aceitas |
| **Valor** | A empresa configurou faixa de valor e o valor do edital está **mais de 10%** fora dela |

### Descartado não é o mesmo que zero

    Um edital descartado não tem nota. Já um edital que passa pelo funil mas pontua pouco pode ter nota baixa, ou até 0, e continua no final do feed.

### Passo 2: pontuação por critério

Os editais que passam pelo funil recebem pontos em cada critério configurado. Com todos os critérios configurados, a soma máxima é 100:

| Critério | Regra de pontuação | Peso máximo |
| :--- | :--- | :---: |
| **Termos-chave** | 40 pts: termo exato ou expressão composta completa encontrados no texto do objeto (ex: "manutenção de computadores")<br>20 pts: correspondência parcial (ex: encontra as palavras separadas ou apenas o radical da palavra)<br>Com vários termos configurados, vale o de maior pontuação (não soma) | **40** |
| **Localidade (UF)** | 25 pts: UF do órgão está entre as UFs configuradas<br>0 pts: caso contrário | **25** |
| **Valor estimado** | 15 pts: dentro da faixa configurada<br>5 pts: até 10% fora da faixa<br>0 pts: valor não informado no edital | **15** |
| **Prazo** | 10 pts: 8 dias úteis ou mais<br>5 pts: de 4 a 7 dias úteis<br>0 pts: menos de 4 dias úteis (e ainda no prazo mínimo) | **10** |
| **Modalidade** | 10 pts: modalidade prioritária (ou qualquer modalidade aceita, se nenhuma for prioritária)<br>5 pts: demais modalidades aceitas | **10** |
| **Total** | | **100** |

### Passo 3: critério não configurado

Alguns filtros são opcionais (UF, valor e modalidade). Se a empresa não configurou um deles, o critério **sai do cálculo** e a nota é reescalada para 100, de modo que nenhum perfil fique com teto menor que outro:
```text
score = arredondar( 100 × pontos obtidos ÷ soma dos pesos dos critérios configurados )
```

O arredondamento é para o inteiro mais próximo, com 0,5 para cima. Termos-chave e prazo sempre entram no cálculo (o prazo usa o mínimo padrão quando a empresa não define outro), então a soma dos pesos nunca é menor que 50.

### Regras de correspondência dos termos

- Sem diferenciar maiúsculas, minúsculas e acentos.
- Plural simples é aceito ("notebook" encontra "notebooks").
- Termos curtos só casam como palavra inteira (a sigla "TI" não casa com "atividade").
- Termos com mais de uma palavra casam como expressão ("material de escritório").

### Dias úteis e data de referência

- Dias úteis excluem sábados, domingos e feriados nacionais, no fuso de Brasília.
- A contagem começa no dia seguinte à data de referência e termina na data limite do edital, inclusive.
- A data de referência é gravada junto com o score ("calculado em"). É ela que torna o cálculo repetível: o mesmo perfil e o mesmo edital, na mesma data de referência, dão sempre a mesma nota.

---

## 3. Classificação e ranqueamento

Com base na nota, a interface classifica as oportunidades no feed. Cada faixa tem **rótulo em texto**, além da cor:

| Faixa | Nota | Significado | Posição no feed |
| :--- | :---: | :--- | :--- |
| 🟢 **Alta aderência** | 80 a 100 | Oportunidades "padrão ouro", que cruzam bem com logística, capacidade e faixa de valor | Topo |
| 🟡 **Média aderência** | 50 a 79 | Boas oportunidades que pedem atenção (por exemplo, UF diferente ou valor um pouco fora da faixa) | Meio |
| 🔴 **Baixa aderência** | 0 a 49 | Passaram pelo funil, mas pontuam pouco | Final |

**Desempate**, nesta ordem: maior nota, prazo limite mais próximo, publicação mais recente e, por fim, identificador do edital em ordem crescente. Isso mantém a ordem do feed estável.

---

## 4. Exemplo resolvido

**Perfil de exemplo** (data de referência: 15/10/2026):

| Campo | Valor |
| :--- | :--- |
| Termos | notebook, impressora |
| UFs | DF, GO |
| Faixa de valor | R$ 50.000 a R$ 500.000 (aceito até R$ 550.000 e a partir de R$ 45.000) |
| Modalidade aceita | Pregão eletrônico |
| Prazo mínimo | 3 dias úteis (padrão) |

**Resultado para seis editais:**

| Edital | Situação | Termos | UF | Valor | Prazo | Modalidade | Nota | Faixa |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **A** | "Aquisição de notebooks", DF, R$ 180.000, 9 dias úteis, pregão | 40 | 25 | 15 | 10 | 10 | **100** | 🟢 Alta |
| **B** | Termo só na descrição, GO, R$ 520.000, 5 dias úteis, pregão | 20 | 25 | 5 | 5 | 10 | **65** | 🟡 Média |
| **C** | Igual ao A, mas em RS | 40 | 0 | 15 | 10 | 10 | **75** | 🟡 Média |
| **D** | Igual ao A, mas valor de R$ 700.000 | | | | | | Descartado | Valor |
| **E** | Igual ao A, mas com 2 dias úteis de prazo | | | | | | Descartado | Prazo |
| **F** | Objeto sem nenhum dos termos | | | | | | Descartado | Termos |

**Com um perfil simplificado** (sem UF e sem modalidade configuradas), os critérios aplicáveis somam 65 pontos (40 + 15 + 10). O edital B passa a valer 30 ÷ 65 × 100 = 46,15, que arredonda para **46 (Baixa)**. O edital C, que antes perdia pontos pela UF, passa a valer 65 ÷ 65 × 100 = **100 (Alta)**, porque a UF não faz mais parte do cálculo.

---

## 5. Quando o score é calculado e onde é guardado

- **Base global:** a ingestão guarda todos os editais normalizados uma única vez, para todas as empresas.
- **Quando recalcular:** o score de cada empresa é calculado (a) quando novos editais são coletados, (b) quando o perfil é editado, reavaliando todos os editais ativos, e (c) **uma vez por dia**, porque os pontos de prazo mudam com o calendário. Um edital "Alta" hoje pode virar "Média" amanhã sem que nada no perfil tenha mudado.
- **O que é guardado:** para cada empresa, o score dos editais elegíveis, a faixa, o detalhamento de pontos por critério e a data de referência. O detalhamento permite explicar o ranking ao usuário (por exemplo, quais critérios foram atendidos).
- **Descartados:** não são guardados por empresa. Como a base de editais é global, quando o perfil muda eles podem ser reavaliados.
- **Editais encerrados** saem do feed.

### "Custo do recálculo diário"

O recálculo diário multiplica o número de empresas pelo de editais ativos. Em um banco que cobra por leitura e escrita, esse volume precisa ser estimado e acompanhado contra o teto de US$ 50 (RNF21 a RNF24). Uma alternativa, se o custo pesar, é guardar só os pontos dos critérios que não dependem da data e calcular os pontos de prazo na hora de exibir.

---

## 6. Interação com os filtros dinâmicos

O score é calculado **exclusivamente a partir do Perfil de Fornecedor (fixo)**. Quando o Analista usa o refinamento no feed:

- O sistema **não recalcula o score**.
- A filtragem é feita no **servidor**, sobre campos já guardados, com paginação. Filtrar só no navegador esconderia editais que ainda não foram carregados e mostraria "nenhum resultado" quando existem.
- O refinamento **só restringe** os editais já elegíveis. Para trazer de volta editais descartados, é preciso alterar o perfil.
- O ranqueamento original é mantido.

**Vantagens:** evita recálculos desnecessários, mantém o custo controlado no modelo *serverless* e dá uma resposta rápida na filtragem.

---

## 7. Integração com alertas e demonstração

- **Alertas:** o alerta de nova oportunidade (RF21) pode disparar para editais com nota **a partir de 80** (Alta aderência).
- **Demonstração:** a relevância dos dez primeiros resultados de cada perfil será medida na demo. Os resultados da medição servem para ajustar os pesos e as faixas, que por isso ficam como constantes de configuração.
- **Testes:** a tabela de casos de borda e o exemplo resolvido viram testes automatizados.

---

## 8. Fora do MVP

Estes pontos aparecem como desejos nos documentos anteriores, mas dependem de dados ou recursos que o MVP não tem:

- **Pontuar "aceita fornecimento nacional ou remoto":** exigiria ler o texto do edital.
- **Modalidade com "histórico de vitórias":** o produto não guarda histórico de vitórias. A prioridade vem de um campo do perfil.
- **Termos excluídos** (por exemplo, "bolsa para notebook").
- **Semântica avançada ou IA** na correspondência de termos.

---


## Histórico de versões

| Versão | Data | Descrição | Autor |
| :---: | :---: | :--- | :--- |
| `1.0` | 14/10/2026 | Versão inicial | Maria Julia Sena |
| `1.1` | 15/10/2026 | Funil revisado, critério não configurado, exemplo resolvido, casos de borda, recálculo, filtragem no servidor e correção do português | Maria Julia Sena |
# Filtros e Configuração de Perfil

## Resumo

Este documento detalha a mecânica de filtros do LicitaRadar. Os filtros do perfil da empresa são a base do **Score de Aderência**: eles definem quais editais do PNCP ficam de fora e como os demais são ranqueados. O resultado é um feed que entrega ao Analista de Licitações apenas as oportunidades relevantes, lucrativas e com prazo viável para a PME. O cálculo detalhado está em [Cálculo do Score de Aderência](score.md).

---

## 1. O papel dos filtros na plataforma

No modelo tradicional de vendas governamentais, o Analista de Licitações gasta a maior parte do dia na "triagem bruta": configurando filtros em portais lentos e lendo dezenas de páginas de PDFs apenas para encontrar uma oportunidade válida.

No LicitaRadar, a abordagem elimina o trabalho braçal e eleva o papel do analista a estrategista, dividindo a filtragem em duas camadas:

- **Filtros de Perfil (fixos):** a empresa configura o seu **Perfil de Fornecedor** uma única vez. O Score de cada edital é calculado a partir desse perfil, de modo que o feed diário já nasça limpo e focado no negócio. Quem mantém o perfil é, em geral, o Analista, com validação do Decisor (sócio ou diretor comercial).
- **Filtros de Refinamento (dinâmicos):** o Analista usa a plataforma diariamente para fazer microajustes conforme a estratégia da semana (por exemplo, procurar um termo específico em um dia ou aumentar a faixa de valor em outro), dedicando o tempo poupado à análise técnica do Termo de Referência e à elaboração de propostas vencedoras.

## A ingestão não depende do perfil

A coleta de editais no PNCP é **global e idempotente**: todos os editais são normalizados e guardados uma única vez, para todas as empresas. O perfil só entra no momento de calcular o Score de cada empresa. Assim, quando o perfil muda, os editais já coletados podem ser reavaliados sem nova coleta.

---

## 2. Tipos de filtros (escopo do MVP)

Para garantir a entrega no prazo do ciclo B2B (aproximadamente 2 meses) e manter o foco nas funcionalidades *Must Have*, o sistema de filtros foi desenhado para ser determinístico e de baixo custo de processamento.

Os filtros se dividem em **seis categorias**. Cada uma pode **eliminar** o edital, **pontuar** o edital, ou os dois:

| Filtro | Obrigatório no perfil? | Elimina o edital quando... | Pontua? | Se não configurado |
| :--- | :---: | :--- | :---: | :--- |
| 2.1 Datas e prazos | Prazo mínimo tem valor padrão | O prazo restante é menor que o prazo mínimo | Sim | Usa o prazo mínimo padrão (3 dias úteis) |
| 2.2 Localidade (UF) | Não | Nunca | Sim | Nível nacional: o critério sai do cálculo |
| 2.3 Termos-chave | Sim | Nenhum termo corresponde ao edital | Sim | Não se aplica: perfil exige termos |
| 2.4 Valor estimado | Não | O valor está mais de 10% fora da faixa | Sim | O critério sai do cálculo |
| 2.5 Modalidade | Não | A modalidade não está entre as aceitas | Sim | O critério sai do cálculo |
| **2.6 Categoria (PNCP)** | Não | A categoria do edital é diferente da selecionada (salvo se vier sem categoria) | Não (Apenas funil) | O critério sai do cálculo |

### 2.1. Filtro de datas e prazos

Garante que a empresa só visualize oportunidades para as quais ainda há tempo hábil de preparar a proposta e separar os documentos.

- **Comportamento:** o perfil define o **prazo mínimo** (em dias úteis) para envio de propostas. Editais com prazo restante menor que esse valor são eliminados. Para os demais, quanto mais folga, mais pontos. No refinamento, o Analista também pode filtrar pela **data de publicação** ou pela **data de abertura/limite**.
- **Exemplo de uso:** ocultar automaticamente editais cujo prazo limite seja inferior a 3 dias úteis, evitando esforço inútil da equipe comercial.
- **Regra de negócio:** o valor padrão do prazo mínimo é de 3 dias úteis. Dias úteis excluem sábados, domingos e feriados nacionais, no fuso de Brasília.

### 2.2. Filtro de localidade (UF)

Define as regiões onde a empresa tem capacidade logística para atuar.

- **Comportamento:** seleção múltipla de Estados (UF). A UF considerada é a do órgão comprador.
- **Exemplo de uso:** uma empresa de engenharia de Brasília seleciona "DF" e "GO". Editais de obras no "RS" **não são eliminados**, mas perdem os pontos de localidade e, por isso, nunca chegam à faixa de Alta Aderência.
- **Regra de negócio:** se deixado em branco, o sistema assume nível "Nacional" (todo o Brasil) e o critério deixa de entrar no cálculo.

### 2.3. Filtro de termos-chave (objeto do edital)

É o filtro mais crítico para a precisão da busca. Cruza as palavras definidas pela empresa com o objeto e a descrição da licitação.

- **Comportamento:** inserção de *tags* ou palavras-chave (por exemplo, "computadores", "notebook", "material de escritório").
- **Regra de negócio (MVP):** busca por correspondência de palavras, sem diferenciar maiúsculas, minúsculas e acentos, e aceitando o plural simples ("notebook" encontra "notebooks"). Termos curtos só correspondem como palavra inteira, para evitar falsos positivos (a sigla "TI" não deve casar com "atividade"). Não há semântica avançada de IA nesta primeira versão, para poupar recursos em nuvem.
- **Efeito:** se nenhum termo corresponde ao edital, ele é **eliminado**. Termos no objeto principal valem mais do que termos apenas na descrição.

### 2.4. Filtro de valor estimado

Garante que a empresa não perca tempo com licitações que não justificam o custo operacional da participação.

- **Comportamento:** definição de uma faixa de valor (mínimo e máximo).
- **Exemplo de uso:** mostrar apenas editais com valor estimado acima de R$ 50.000,00.
- **Regra de negócio:** valor dentro da faixa pontua cheio. Valor até 10% fora da faixa (abaixo do mínimo ou acima do máximo) pontua parcialmente. Valor mais de 10% fora da faixa elimina o edital. Se o edital não informar valor estimado, ele não é eliminado, mas não recebe pontos neste critério.

### 2.5. Filtro de modalidade (opcional no MVP)

Permite que empresas que só participam de tipos específicos de licitação limitem os resultados.

- **Comportamento:** seleção das modalidades aceitas (por exemplo, Pregão Eletrônico, Concorrência, Dispensa). A empresa pode marcar uma delas como **prioritária**.
- **Regra de negócio:** se configurado, editais de outras modalidades são eliminados. Modalidades prioritárias pontuam mais que as demais aceitas.

### 2.6. Filtro de categoria / catálogo nativo (PNCP)

Atua como um atalho seguro para quem não quer listar dezenas de termos específicos do seu ramo, utilizando a própria classificação oficial do governo.

- **Comportamento:** seleção de categorias ou grupos de contratação baseados no catálogo que a API do PNCP já fornece nativamente (ex: categorias de Materiais, Serviços ou Obras, baseados no Padrão CATMAT/CATSER).
- **Exemplo de uso:** uma empresa seleciona a categoria "Serviços de Tecnologia da Informação". O sistema filtra automaticamente as licitações dessa categoria sem precisar de uma lista de "termos ocultos" mantida pela nossa equipe.
- **Regra de negócio (MVP):** a filtragem é feita pelo código ou *tag* de categoria retornado no JSON da API do PNCP. Se um edital for publicado pelo órgão sem a devida categorização (campo vazio na API), o sistema ignora esta restrição para esse edital específico, garantindo que o usuário não perca oportunidades por erro de preenchimento do governo.

---

## 3. Dinâmica com o Score de Aderência

Os filtros configurados não servem só para esconder editais, mas para **ranqueá-los**. O motor do LicitaRadar elimina os editais que falham nos critérios obrigatórios e atribui de 0 a 100 pontos aos demais:

| Situação do edital | Resultado | Posição no feed |
| :--- | :--- | :--- |
| **Aderência total:** corresponde aos termos, está na UF selecionada, dentro da faixa de valor, com prazo confortável | Score de **80 a 100** (Alta aderência) | Topo do feed |
| **Aderência parcial:** corresponde aos termos, mas perde pontos em algum critério (por exemplo, UF diferente) | Score de **50 a 79** (Média aderência) | Meio do feed |
| **Pouca correspondência:** passou pelos critérios obrigatórios, mas pontua pouco | Score **abaixo de 50** (Baixa aderência) | Final do feed |
| **Fora do perfil:** nenhum termo corresponde, prazo menor que o mínimo, modalidade excluída ou valor mais de 10% fora da faixa | **Descartado** (não aparece) | Oculto |

---

## 4. Refinamento diário (filtros dinâmicos)

Os filtros de refinamento **não alteram o Score** nem o perfil da empresa. Eles agem sobre os editais que já são elegíveis para a empresa:

- A filtragem é feita no **servidor**, sobre campos já guardados, com paginação. Assim funciona mesmo com muitos editais e sem recalcular o Score.
- O refinamento **só restringe**: um termo temporário esconde editais, mas não traz de volta editais que o perfil já descartou. Para ampliar a busca, o Analista precisa alterar o Perfil de Fornecedor.
- O ranqueamento original é mantido. Um edital que corresponda ao termo temporário aparece na posição do seu Score de perfil.

---

## 5. Experiência do usuário (UI/UX)

Para o **Usuário Operacional** (Analista de Licitações), a interface dos filtros deve equilibrar automação com flexibilidade diária:

1. **Perfil de Fornecedor (fixo):** uma página de "Configurações de Conta" onde ficam os filtros globais da empresa, que formam a base do feed.
2. **Barra de refinamento (dinâmica):** uma área visível no topo do feed onde o Analista pode, a qualquer momento, adicionar uma palavra-chave temporária ou ajustar a data para explorar o dia, sem alterar o perfil base da empresa.
3. **Indicadores visuais:** cada edital no feed exibe pequenas *tags* mostrando quais critérios foram atendidos e a urgência do prazo (por exemplo, uma tag `[DF]`, uma tag `[R$ 100k]` ou uma tag `[Encerra em 2 dias]`), acelerando a decisão. Os rótulos sempre trazem **texto** além da cor, para não depender só de cor.

---

## Histórico de versões

| Versão | Data | Descrição | Autor |
| :---: | :---: | :--- | :--- |
| `1.0` | 14/10/2026 | Versão inicial | Maria Julia Sena |
| `1.1` | 15/10/2026 | Alinhamento com o Score: efeito de cada filtro (elimina ou pontua), filtro não configurado, ingestão global, refinamento no servidor, alteração para Categoria (PNCP) | Maria Júlia Sena |
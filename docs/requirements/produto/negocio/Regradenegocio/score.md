# Cálculo do Score de Aderência

## Resumo
O Score de Aderência é o núcleo de inteligência do LicitaRadar (**ADR-0008**). Ele utiliza os filtros do Perfil de Fornecedor para eliminar editais fora do escopo e ranquear as oportunidades elegíveis com uma nota de 0 a 100. O objetivo é poupar o tempo de triagem manual da Analista e destacar as oportunidades mais lucrativas para o Decisor.

---

## 1. Princípios Arquiteturais

Conforme definido nas Decisões Técnicas da equipa, o cálculo obedece aos seguintes princípios:
* **Determinístico (ADR-0008):** A nota depende exclusivamente de três entradas (perfil da empresa, edital e data de referência). As mesmas entradas produzem sempre o mesmo resultado, sem o uso de IA ou semântica avançada neste MVP.
* **Baixo Custo de Processamento (ADR-0004):** O cálculo utiliza apenas os campos estruturados da API do PNCP (objeto, órgão, UF, modalidade, valor, datas), evitando a leitura custosa de PDFs.
* **Configurável:** Pesos, faixas e limiares são mantidos como constantes de configuração no código, permitindo a sua calibração rápida após as demonstrações de validação.
* **Explicável:** O detalhamento dos pontos por critério é guardado na base de dados junto com a nota final, permitindo justificar o ranqueamento na interface.

---

## 2. Motor de Cálculo: A Lógica de Pontuação

A avaliação de cada edital ocorre em três passos sequenciais:

### Passo 1: Funil Eliminatório
Critérios excludentes. Se o edital falhar em qualquer um destes pontos, é **descartado** e não é gerado o seu score para aquela PME:

| Critério | Condição de Eliminação (Descarte) |
| :--- | :--- |
| **Termos-chave** | Nenhum termo do perfil corresponde ao texto do objeto do edital. |
| **Categoria (PNCP)** | A empresa definiu uma categoria e a do edital é diferente. |
| **Prazo** | O prazo restante é inferior ao prazo mínimo do perfil (padrão: 3 dias úteis) ou já expirou. |
| **Modalidade** | A modalidade do edital não consta na lista de aceites pela empresa. |
| **Valor** | O valor estimado do edital encontra-se **mais de 10%** fora da faixa configurada pela empresa. |

### Passo 2: Pontuação por Pesos
Os editais que sobrevivem ao funil recebem pontos. Com o perfil completo, a soma máxima é 100:

| Critério | Regra de Pontuação | Peso Máx |
| :--- | :--- | :---: |
| **Termos-chave** | **40 pts:** Termo exato ou expressão composta completa.<br>**20 pts:** Correspondência parcial (palavras separadas ou radical). | **40** |
| **Localidade (UF)** | **25 pts:** UF do órgão corresponde às UFs do perfil.<br>**0 pts:** Caso contrário. | **25** |
| **Valor Estimado** | **15 pts:** Dentro da faixa configurada.<br>**5 pts:** Até 10% fora da faixa estipulada.<br>**0 pts:** Valor não informado. | **15** |
| **Prazo** | **10 pts:** 8 dias úteis ou mais.<br>**5 pts:** De 4 a 7 dias úteis.<br>**0 pts:** Menos de 4 dias úteis (mas acima do mínimo). | **10** |
| **Modalidade** | **10 pts:** É a modalidade prioritária da PME.<br>**5 pts:** É uma modalidade secundária aceite. | **10** |
| **Total** | A soma máxima possível com todos os critérios. | **100** |

### Passo 3: Reescala (Critérios Opcionais)
Filtros como UF, Valor e Modalidade são opcionais. Se a PME não configurar um deles, o critério é removido do cálculo e a nota final é reescalada (garantindo que o teto se mantém nos 100 pontos):
`Score = arredondar( 100 × pontos obtidos ÷ soma dos pesos dos critérios configurados )`

*(Nota: Os termos-chave e o prazo são obrigatórios, garantindo que a base de cálculo nunca é inferior a 50).*

---

## 3. Armazenamento e Gatilhos de Recálculo

Para garantir a escalabilidade e o limite de custos em Cloud, a arquitetura de dados divide-se da seguinte forma:

* **Base Global (PostgreSQL - ADR-0005/0007):** A ingestão do PNCP guarda todos os editais normalizados uma única vez, de forma idempotente, servindo todas as empresas. Os editais descartados não geram registos individuais.
* **Base de Leitura (Firestore - ADR-0005):** O score final, a faixa e o detalhamento de pontos são gravados no Firestore para leitura rápida pela aplicação Frontend (React), minimizando consultas pesadas.

**Quando ocorre o recálculo do Score?**
1. Na **ingestão diária** de novos editais do PNCP.
2. Na **edição do Perfil de Fornecedor**, que força a reavaliação de todos os editais ativos para aquela empresa.
3. **Job Diário à meia-noite**, uma vez que os pontos do critério "Prazo" mudam consoante a passagem dos dias úteis.

**Refinamento do Feed vs Recálculo:**
Quando a Analista utiliza os filtros rápidos da interface, o sistema **não recalcula o score**. A filtragem ocorre no servidor do backend (Fastify) sobre os dados já gravados no Firestore, restringindo apenas o que é exibido.

---

## 4. Classificação Visual (UI)

O Frontend mapeia a nota nas seguintes faixas visuais para organizar o feed:

| UI | Nota | Significado | Posição no Feed |
| :--- | :---: | :--- | :--- |
| 🟢 **Alta Aderência** | 80 a 100 | "Padrão ouro" - cruzamento perfeito com a estratégia da PME. | Topo |
| 🟡 **Média Aderência** | 50 a 79 | Boas oportunidades, mas que requerem revisão (ex: valor no limiar). | Meio |
| 🔴 **Baixa Aderência** | 0 a 49 | Passaram no funil, mas não se destacam nos pesos. | Final |

*Critérios de desempate no Feed:* Maior nota > Prazo limite mais próximo > Publicação recente > ID do Edital.

---

## 5. Casos de Teste (Golden Set) para QA

Conforme definido na Estratégia de Testes (**ADR-0009**), este cenário serve de *Golden Set* para validação automatizada em Vitest.

**Perfil de Referência (Data: 15/10/2026):**
* **Termos:** notebook, impressora
* **UFs:** DF, GO
* **Valor:** R$ 50.000 a R$ 500.000
* **Modalidade:** Pregão eletrônico
* **Prazo Min:** 3 dias úteis

**Resultados Esperados (Massa de Teste):**

| ID | Cenário | Resultado Esperado | Status |
| :--- | :--- | :--- | :--- |
| **A** | "Aquisição de notebooks", DF, R$ 180.000, 9 dias úteis, pregão | **100 pontos** (40+25+15+10+10) | 🟢 Alta |
| **B** | Termo na descrição, GO, R$ 520.000, 5 dias úteis, pregão | **65 pontos** (20+25+5+5+10) | 🟡 Média |
| **C** | Igual ao "A", mas situado no RS | **75 pontos** (40+0+15+10+10) | 🟡 Média |
| **D** | Valor de R$ 700.000 (muito acima dos 10% da faixa tolerada) | **Descartado** | Falha Valor |
| **E** | Prazo limite de apenas 2 dias úteis | **Descartado** | Falha Prazo |
| **F** | Objeto não contém "notebook" nem "impressora" | **Descartado** | Falha Termo |

---

## 6. Fora do Escopo (MVP)
Para manter a viabilidade do Checkpoint 1, ficam conscientemente de fora:
* Inteligência Artificial para ler "fornecimento nacional/remoto" dentro do PDF.
* Histórico preditivo de vitórias para pesar modalidades.
* Exclusão semântica avançada de palavras negativas compostas.
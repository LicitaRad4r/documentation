# Visão Geral do Produto

## Resumo
O LicitaRadar é uma plataforma SaaS B2B que ajuda Pequenas e Médias Empresas (PMEs) a participar de compras públicas. Ele ingere e ranqueia os editais do Portal Nacional de Contratações Públicas (PNCP) por aderência ao perfil da empresa, monitora a validade das certidões de habilitação e avisa sobre oportunidades e vencimentos. 

---

## A Essência do Produto

<div class="grid cards" markdown>

-   **Para**

    ---

    Empresas fornecedoras (PMEs) que já atuam ou desejam ingressar no mercado de compras públicas. *(Mais detalhes em [Personas](../personas/)).*

-   **Cujo problema é**

    ---

    A sobrecarga operacional da equipa comercial, a perda de tempo com triagem manual e a inabilitação por certidões vencidas.

-   **O LicitaRadar é**

    ---

    Um assistente inteligente (SaaS B2B) que automatiza a busca, a triagem e o ranqueamento de editais, e monitoriza proativamente a validade das certidões.

-   **Diferente de**

    ---

    Plataformas de volume massivo (como ConLicitação, que geram ruído) ou plataformas focadas em automação de lances (como Effecti, que são caras e complexas).


</div>

---
## Como o LicitaRadar se diferencia

Enquanto os concorrentes focam em volume de dados ou em automação de lances, o LicitaRadar combina curadoria inteligente com gestão de risco:

1. **Score de Aderência:** em vez de entregar uma lista infinita de editais, o sistema usa um algoritmo para ranquear as oportunidades, colocando no topo apenas as que realmente fazem sentido para o perfil da empresa (termos, UF e valor).
2. **Controlo integrado de certidões:** a concorrência foca apenas em encontrar o edital. O LicitaRadar acompanha o utilizador até à fase de habilitação, emitindo alertas proativos sobre o vencimento das certidões e evitando que a empresa seja desclassificada por falhas burocráticas.
3. **Foco exclusivo nas PMEs:** interface simples, sem excesso de funcionalidades complexas, pensada para o decisor (ROI) e para a analista (agilidade).

## Identificação da Oportunidade e do Problema

O projeto é necessário porque o processo de prospecção atual das PMEs é altamente manual, reativo e ineficiente. Todos os dias, um grande volume de novas oportunidades é publicado no PNCP. O LicitaRadar resolve duas grandes dores operacionais e financeiras que afetam as empresas:

| Dor | Tipo de impacto | O que acontece |
| :--- | :--- | :--- |
| **Perda de oportunidades** | Custo de oportunidade | Devido ao volume massivo de dados e ao esforço manual de leitura, editais altamente lucrativos passam despercebidos ou são descobertos quando já não há tempo hábil para montar uma proposta. |
| **Inabilitação por falhas de controle** | Risco operacional | A gestão das certidões de habilitação costuma ser feita de memória ou em planilhas isoladas. Descobrir que um documento está vencido na véspera da entrega resulta em desclassificação imediata no certame. |

A oportunidade de mercado explorada pelo LicitaRadar é transformar essa rotina exaustiva em um processo automatizado e focado em inteligência de dados, garantindo que as empresas vejam primeiro as licitações com maior probabilidade de vitória.

A figura a seguir apresenta o diagrama de Ishikawa, com as causas organizadas pelos 6Ms e o problema:

![Diagrama de Ishikawa - LicitaRadar](ishikawa.jpg)
<p style="text-align: center;"><em>Figura 1 – Diagrama de Ishikawa do problema (6Ms).</em></p>

---

## Diagrama de Escopo do MVP

```mermaid
flowchart LR
    A[LicitaRadar - Visão do Produto] --> B(MVP - Em Escopo)
    A --> C(Fora do Escopo)

    B --> B1[1. Configuração de Perfil]
    B --> B2[2. Ingestão do PNCP & Score]
    B --> B3[3. Feed de Editais Ranqueado]
    B --> B4[4. Controlo de Certidões]
    B --> B5[5. Alertas Proativos]

    C --> C1[Automação de Lances / Robôs]
    C --> C2[Envio de Propostas]
    C --> C3[Leitura de PDFs com OCR]
    C --> C4[Integração com outros portais]

    style A fill:#2563eb,stroke:#1d4ed8,color:#fff,stroke-width:2px
    style B fill:#16a34a,stroke:#15803d,color:#fff,stroke-width:2px
    style C fill:#dc2626,stroke:#b91c1c,color:#fff,stroke-width:2px
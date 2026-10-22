# Matriz MoSCoW (Priorização do MVP)

A Matriz MoSCoW do LicitaRadar foi construída e definida **estritamente com base nos Requisitos Funcionais e nas Histórias de Utilizador** do projeto. 

Esta rastreabilidade garante que o Produto Mínimo Viável (MVP) foque apenas no que entrega valor real para as nossas personas (Decisor e Analista) no ciclo B2B atual, priorizando o núcleo funcional (descoberta de editais e gestão de risco) e descartando automações que fujam ao escopo validado.

---

## 🟢 Must Have (Deve Ter)
*Obrigatório para o sistema funcionar e entregar o valor central (MVP).*

* **(US01 a US06)** Gestão de acessos, autenticação, fluxo de convites e isolamento de segurança entre perfis (RF01, RF02, RF03).
* **(US07)** Ingestão automática e periódica de editais do PNCP (RF04).
* **(US08, US09 e US11)** Configuração do perfil da empresa, cálculo determinístico do Score de Aderência e exibição do Feed ordenado (RF04, RF06).
* **(US12)** Acesso aos detalhes e anexos originais dos editais.
* **(US14, US15 e US16)** Upload de certidões com data de validade obrigatória e emissão de alertas visuais de vencimento (RF08, RF09, RF10).

## 🟡 Should Have (Deveria Ter)
*Importante para a experiência do utilizador e para o fluxo de valor completo.*

* **(US10)** Filtros dinâmicos no Feed (UF, modalidade, data, valor) aplicados sobre os resultados ranqueados (RF05).
* **(US13, US17, US18 e US19)** Fluxo de Decisão: Recomendação de editais promissores pela Analista e aprovação/descarte no Dashboard exclusivo do Decisor (RF07, RF11, RF12).

## 🔵 Could Have (Poderia Ter)
*Desejável, mas não bloqueia a validação do produto. Pode ser feito se houver tempo extra nas Sprints finais.*

* Envio de alertas de novas licitações de alta aderência e vencimento de certidões por notificação *push* ou e-mail (além do alerta visual interno).
* Exportação da lista de editais filtrados para CSV/Excel.

## 🔴 Won't Have (Não Terá Neste Ciclo)
*Totalmente fora do escopo do ciclo atual (Checkpoints 1 a 4). Pode entrar no futuro.*

* Automação e robôs para envio de lances em pregões (fora do escopo).
* Envio direto de propostas através da plataforma (fora do escopo).
* Leitura em massa de PDFs de editais com Inteligência Artificial / OCR (fora do escopo).
* Integração com outros portais de compras além do PNCP (fora do escopo).

### Mapa Visual de Priorização

```mermaid
flowchart TD
    %% Definição de cores (com texto a preto para compatibilidade com Dark Mode)
    classDef must fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#000000;
    classDef should fill:#fef08a,stroke:#ca8a04,stroke-width:2px,color:#000000;
    classDef could fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#000000;
    classDef wont fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#000000;

    %% MUST HAVE
    M["🟢 MUST HAVE (Obrigatório)"]:::must
    M --- M1["Acessos & Segurança\n(US01 a US06)"]:::must
    M --- M2["Score & Feed PNCP\n(US07 a US09, US11)"]:::must
    M --- M3["Alertas de Certidões\n(US14 a US16)"]:::must

    %% SHOULD HAVE
    S["🟡 SHOULD HAVE (Importante)"]:::should
    S --- S1["Filtros no Feed\n(US10)"]:::should
    S --- S2["Dashboard de Decisão\n(US13, US17 a US19)"]:::should

    %% COULD HAVE
    C["🔵 COULD HAVE (Desejável)"]:::could
    C --- C1["Alertas por E-mail/Push"]:::could
    C --- C2["Exportar para CSV"]:::could

    %% WON'T HAVE
    W["🔴 WON'T HAVE (Fora do Ciclo)"]:::wont
    W --- W1["Robôs de Lances"]:::wont
    W --- W2["Leitura PDF com IA"]:::wont

    %% Força o alinhamento estritamente vertical das categorias
    M ~~~ S ~~~ C ~~~ W
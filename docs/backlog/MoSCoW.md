# Matriz MoSCoW (Priorização do MVP)

A Matriz MoSCoW do LicitaRadar foi construída **com base nos Requisitos Funcionais e nas Histórias de Utilizador** do projeto e segue o [Escopo do MVP](../requirements/produto/negocio/visao/escopo.md).

Esta rastreabilidade garante que o Produto Mínimo Viável (MVP) foque apenas no que entrega valor real para as nossas personas (Decisor e Analista). A prioridade é o núcleo funcional: descoberta de editais e gestão de risco. O que não cabe no prazo fica para depois.

---

## 1. Priorização MoSCoW

| Categoria | Rastreabilidade (US / RF) | Requisito / Funcionalidade |
| :--- | :--- | :--- |
| **Must have**<br>*(Obrigatório)* | **US02, US06**<br>RF01, RF03 | Login, separação de permissões entre Decisor e Analista e isolamento entre empresas. |
| | **US07**<br>RF04 | Ingestão automática e idempotente de editais do PNCP. |
| | **US08, US09, US11**<br>RF04, RF06 | Configuração do perfil da empresa, cálculo determinístico do Score de Aderência e feed ordenado. |
| | **US14 a US16**<br>RF08 a RF10 | Upload de certidões com data de validade, renovação e status de vencimento (alerta dentro da plataforma). |
| | **US20** | Acompanhar edital por etapas: Interessado, Preparando, Enviado e Resultado, com destaque para prazos de até 3 dias. É a jornada nova do Checkpoint 3 (15/11/2026). |
| **Should have**<br>*(Importante)* | **US10**<br>RF05 | Filtros dinâmicos no feed (UF, modalidade, data e valor). |
| | **US12**<br>RF04 | Detalhe do edital com link para o original no PNCP. |
| **Could have**<br>*(Desejável, após o MVP)* | **US01**<br>RF01 | Cadastro self-service da empresa. No MVP, as empresas e os usuários são criados por script. |
| | **US03**<br>RF01 | Recuperação de senha com tela própria. No MVP, usa a recuperação padrão do Firebase Auth. |
| | **US04, US05**<br>RF02 | Convite da Analista por e-mail. |
| | **US13, US17 a US19**<br>RF07, RF11, RF12 | Fluxo de decisão: recomendação pela Analista e aprovação ou descarte no painel do Decisor. |
| | **US21**<br>RF10 | Contador de editais novos desde a última vez que o usuário abriu o feed. |
| | - | Alertas de editais novos e de vencimento de certidões por e-mail ou push. |
| | - | Exportação da lista de editais filtrados para CSV/Excel. |
| **Won't have**<br>*(Fora do produto neste ciclo)* | - | Automação e robôs para envio de lances em pregões. |
| | - | Envio direto de propostas através da plataforma. |
| | - | Leitura em massa de PDFs de editais com Inteligência Artificial / OCR. |
| | - | Integração com outros portais de compras além do PNCP. |
| | - | Predição de chance de vitória em licitações. |

---
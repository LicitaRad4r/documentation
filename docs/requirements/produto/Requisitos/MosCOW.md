# Matriz MoSCoW e Backlog do MVP - LicitaRadar

A nossa priorização segue a metodologia Lean, focando exclusivamente na entrega de valor para as Pequenas e Médias Empresas (PMEs) através da triagem de editais e gestão de risco.

## 🟩 M - Must Have (Obrigatório para o MVP)
*Funcionalidades vitais sem as quais o LicitaRadar não resolve o problema principal.*

1. **Ingestão de Dados do PNCP:** O sistema deve consumir a API do Portal Nacional de Contratações Públicas diariamente.
2. **Score de Aderência (v0):** O sistema deve calcular uma pontuação matemática para cada edital com base no perfil da PME.
3. **Filtros Ágeis de Triagem:** A analista deve conseguir filtrar editais por data, valor e palavra-chave.
4. **Painel de Alertas de Certidões:** O sistema deve alertar sobre certidões da empresa que estejam a menos de 30 dias de caducar.

## 🟨 S - Should Have (Importante, mas pode esperar)
*Funcionalidades de alto valor, mas que podem ser contornadas manualmente na primeira versão.*

1. **Filtro de Palavras Negativas:** Capacidade de excluir termos específicos do feed de editais para limpar a visão da analista.
2. **Notificações por E-mail:** Envio de alertas de vencimento de certidões diretamente para o e-mail do decisor (se não for possível, o alerta fica apenas no ecrã inicial).
3. **Exportação de Oportunidades:** Exportar a lista de editais selecionados para CSV/Excel.

## 🟦 C - Could Have (Desejável)
*Funcionalidades interessantes se houver tempo extra (baixo esforço, impacto moderado).*

1. **Integração com Calendário:** Adicionar a data de entrega da proposta diretamente ao Google Calendar do utilizador.
2. **Dashboard Gráfico de ROI:** Gráficos visuais mostrando o valor total dos editais aprovados para participação.

## 🟥 W - Won't Have (Não teremos neste ciclo)
*Funcionalidades conscientemente deixadas de fora do MVP para manter o foco na triagem.*

1. **Submissão Automática de Propostas:** O sistema não enviará a proposta nem os documentos para o PNCP. A plataforma foca-se apenas na descoberta e triagem.
2. **Gestão Financeira Pós-Contrato:** O sistema não fará a gestão de faturação caso a PME vença a licitação.
3. **Deploy na Nuvem (Semana 2):** A arquitetura inicial rodará apenas localmente via Docker, não havendo deploy no Firebase/Cloud Run nesta fase de validação.

---

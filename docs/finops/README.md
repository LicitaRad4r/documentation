# FinOps — plano custo zero

**Responsável:** DevOps  
**Projeto:** LicitaRadar  
**Atualizado:** 31/10/2026  

Sem voucher GCP **não há** alertas de orçamento 50/80/100%. A prova para o tutor é o conjunto de prints de cada serviço no **plano gratuito**, sem forma de pagamento cadastrada (ver ADR-0012).

Os arquivos ficam em [`evidencias/`](evidencias/README.md).

## Checklist de evidências (Checkpoint 2)

| # | Serviço | O que o print deve mostrar | Arquivo sugerido |
| - | ------- | -------------------------- | ---------------- |
| 1 | Firebase | Projeto no plano **Spark**; **sem** conta de faturamento / Blaze | `evidencias/01-firebase-spark.png` |
| 2 | Render | Workspace no plano **Free**; sem cartão | `evidencias/02-render-free.png` *(Semana 5)* |
| 3 | GitHub Actions | Uso dentro da cota gratuita | `evidencias/03-github-actions.png` *(quando o job existir)* |
| 4 | UptimeRobot | Plano gratuito, sem cartão | `evidencias/04-uptime-robot.png` *(Semana 5)* |

Até a Semana 4, o item **1 (Firebase Spark)** é o prioritário.

## Como tirar o print do Firebase (item 1)

1. Acesse o [Firebase Console](https://console.firebase.google.com/) no projeto da equipe.
2. Confirme que o plano é **Spark** (não Blaze).
3. Em Project settings / Usage and billing (ou equivalente), mostre que **não há** billing account vinculada.
4. Salve o print em `evidencias/01-firebase-spark.png` (ou `.jpg`) e atualize a tabela de status abaixo.

## Regras de ouro

1. Nunca cadastrar cartão em GCP, Firebase, Render, Supabase ou GitHub.
2. Nunca clicar em Upgrade / Blaze no Firebase.
3. Testes pesados, k6 e seed grande só no emulador — nunca no Firestore real.
4. Um projeto Firebase por equipe.
5. Chave de serviço com papel mínimo, só em secrets (nunca no Git); gitleaks no CI.
6. Serviço novo = ADR + aprovação do Tech Lead.

## Status atual

| Item | Status |
| ---- | ------ |
| ADR-0012 plano custo zero | Proposto (aguardando professor / Tech Lead) |
| Firebase Spark + print | Pendente — criar projeto e anexar evidência |
| Render / UptimeRobot | Semana 5 |
| Job PNCP no Actions | Esqueleto no repositório de código; ativar na Semana 5 |

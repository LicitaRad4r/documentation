## Checkpoint 2 (05/11)

1. Subir do zero: clone limpo, `docker compose up --build` e seed, com o app abrindo ligado à API real.
2. Login por papel: Decisor e Analista da Empresa A; a Analista não vê "Certidões" e recebe "Acesso negado" ao forçar o endereço.
3. Jornada 1 (Triagem): ajuste do perfil da empresa (termos, UFs, faixa de valor) e feed ranqueado pelo score mudando em seguida.
4. Jornada 2 (Filtros): busca por "notebook", filtros de UF e modalidade com etiquetas removíveis, mantidos ao recarregar a página.
5. Jornada 3 (Certidões): renovação da CNDT com arquivo e nova data; recusa de arquivo de 6 MB; certidão saindo de "Precisam de atenção".
6. Isolamento: Empresa A tentando abrir registro da Empresa B recebe 403; acesso direto ao Firestore negado pelas regras deny-all.
7. Ingestão: execução da coleta do PNCP, novos editais no feed e segunda execução sem duplicar.
8. Qualidade: último PR verde no CI (testes do front e deny-all), matriz de rastreabilidade e relatório de testes em `docs`.

## Checkpoint 3 (15/11)

1. Pipeline de Deploy e Preview: abertura de um PR de teste para validar o canal de preview no Firebase Hosting, seguido de merge na `main` publicando o front no Hosting e a API no Render (auto-deploy), com smoke test automático.
2. Validação de Health Checks: verificação dos endpoints `/healthz` e `/readyz` na URL pública da API.
3. Jornada 1 (Triagem): login como Analista na URL de produção, feed ranqueado e filtros, com observação do cold start do Render (tela de carregamento na primeira chamada).
4. Jornada 2 (Acompanhamento — jornada nova, US20): "Acompanhar" um edital no feed, mover pelo quadro Interessado → Preparando → Enviado → Resultado com escolha de Venceu/Não venceu; selos "Prazo próximo" e "Prazo encerrado".
5. Jornada 3 (Certidões): login como Decisor, alerta de vencimento e renovação com upload.
6. Isolamento: login como Decisor da Empresa B com quadro e certidões próprios; Analista sem acesso a Certidões.
7. Alertas: issue aberta automaticamente na falha do job de ingestão (PR #39) e monitor do UptimeRobot em `/readyz`.
8. Auditoria de Custos: evidências em `finops/evidencias/` (Firebase Spark, Render Free, GitHub Actions) com gasto acumulado de US$ 0, conforme a ADR-0001.
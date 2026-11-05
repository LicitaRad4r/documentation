# LicitaRadar — Backlog das Sprints 3 e 4 (Gate 2) e Roteiro da Demo

05/11/2026

## Calendário e premissas

As Sprints 3 e 4 (18/10 a 01/11) formam o Gate 2, "Núcleo funcional". O gate passa quando a jornada principal roda localmente, sem configuração manual oculta nem dependência instável. Hoje (05/11) estamos no quinto dia da Sprint 5, então este backlog é o que ficou em atraso do Gate 2 e precisa fechar agora, antes de qualquer item de deploy.

Neste documento J1 a J4 seguem a Matriz de Rastreabilidade: J1 feed, J2 filtros, J3 certidões e J4 segurança (login, 401 e 403). A página de Jornadas da documentação usa outra numeração, e nela o acompanhamento do edital (US20) é a Jornada 2.

## Onde estamos

Já está cumprido:

- **API (OpenAPI v0.3):** perfil, feed com score determinístico, filtros, busca e paginação, certidões com renovação, 11 casos de segurança (CT-SEG-001 a 011), CORS e regras do Firestore deny-all (21 testes passando).
- **Servidor:** 183 testes sem falha e 98,3% de cobertura de linhas.
- **Front J1 a J3 com API simulada:** testados hoje, com busca vazia, filtros combinados, paginação no F5, renovação de certidão e as validações de data, arquivo ausente, tipo inválido, mais de 5 MB e cancelar.
- **Seed de demo:** duas empresas com perfis diferentes (TI no DF e GO, e só notebook no RS) e três usuários com papéis.
- **Documentação:** EP/BVA v1, primeiro relatório de testes e gasto zero documentado com o print do plano Spark.

O que está travado: o PR #24 (front da J4) tem o CI vermelho por um caminho de arquivo no teste de contrato e três pendências da revisão.

## Backlog do Gate 2: o que ficou em atraso

As pendências vêm do relatório de testes de 04/11 (BUG-001 a BUG-005 e os testes pendentes) e do Cronograma. Os quatro primeiros itens bloqueiam a demo local que o gate pede.

| ID | Pendência | Origem | Prio | Dono | Critério de aceite | Prazo |
| --- | --- | --- | --- | --- | --- | --- |
| G2-01 | Destravar o PR #24 (login, 401 e 403 no front) e corrigir o caminho do schema no teste de contrato | BUG-001 e CI | Must | Front-End e QA | CI verde, merge feito e login real com o emulador abrindo o feed | 06/11 |
| G2-02 | Subir o Compose com a API real por padrão, sem dados falsos | BUG-002 | Must | DevOps e Front-End | `docker compose up --build` abre o front ligado à API, sem o selo "API simulada". O mock continua disponível como opção | 07/11 |
| G2-03 | Rodar o seed no `docker compose up`, ou documentar o passo no README | BUG-003 | Must | Back-End e DevOps | Do zero, o Compose chega ao feed com dados e logins de demo, testado por quem não escreveu o README | 07/11 |
| G2-04 | Rodar no CI os testes do front e o deny-all com emulador | BUG-004 | Must | DevOps e QA | O CI executa o vitest do front e o `test:int`, e uma falha bloqueia o PR | 09/11 |
| G2-05 | Tela para configurar o perfil: termos, UFs, modalidades e faixa de valor (a US08 é Must na MoSCoW) | BUG-005 | Must | Front-End | O formulário salva em `PUT /v1/perfil`, o feed muda conforme o perfil e valores inválidos mostram o erro do campo | 09/11 |
| G2-06 | Fechar os 5 testes pendentes: CT-FEED-007, CT-FILT-008, CT-FILT-009 (p95), CT-CERT-008 e CT-CERT-009 | Relatório de 04/11 | Must | QA e Front-End | Os 5 casos passam e entram na matriz. O p95 de leitura é medido com o seed | 09/11 |
| G2-07 | Um roteiro E2E contra a API real com o emulador (hoje os testes de tela rodam contra o mock) | Relatório de 04/11 | Must | QA | Login, feed e renovação de certidão rodando contra a API real, sem mock | 10/11 |
| G2-08 | Atualizar a matriz de rastreabilidade e o relatório de testes | Cronograma do CP2 | Must | QA | A matriz tem teste e resultado nos 39 CTs (hoje só o CT-SEG-010 está preenchido). O relatório é refeito na `main`, já que o PR #13 integrou a `feat/api-base` | 08/11 |
| G2-09 | Alinhar ADRs e código: os ADRs 0005 e 0006 descrevem PostgreSQL no Neon com Drizzle e o 0007 descreve a ingestão por script no Actions, mas o código só usa Firestore e o workflow chama uma rota da API que não existe | ADRs v0.2 | Must | Tech Lead | Nova versão dos ADRs que reflita o que será entregue, ou plano para implementar o que falta, e `Arquitetura.md` atualizado | 07/11 |
| G2-10 | Preencher `backlog/backlogcp2.md` e `gestao_metodologia/revisoesbacklog.md`, hoje vazios | Documentação | Must | PO | Pelo menos duas revisões do backlog registradas, uma da Sprint 3 e outra da Sprint 4, com o que mudou de prioridade e por quê. Registre as que de fato aconteceram; se não houve registro, anote a data de hoje | 08/11 |
| G2-11 | Ingestão incremental dos editais do PNCP rodando localmente, com o score calculado por empresa | Guia do PO | Must | Back-End | Com o Compose no ar, uma execução traz editais novos do PNCP (ou de fixtures gravadas), rodar duas vezes não duplica e o feed das duas empresas muda | 12/11 |
| G2-12 | Roteiro da demo do Gate 2 e o ensaio de 15 minutos do guia (veja a seção abaixo) | Guia do PO | Must | PO e QA | Ensaio feito por quem não escreveu o README, com o resultado e os bugs registrados como BUG-xxx | 12/11 |
| G2-13 | Conferir a acessibilidade básica das telas: uso só pelo teclado, rótulo em todo campo e status nunca só por cor | Guia do PO | Should | Front-End e QA | Checklist de teclado, rótulos e status passando no feed e nas certidões | 10/11 |

O G2-09 é o que mais pesa no resto, porque define se a ingestão (G2-11) grava em PostgreSQL ou só em Firestore, e quem decide é a Tech Lead.

O US20 (acompanhar edital) só entra no MVP se estiver em PR até 15/11, conforme a MoSCoW, senão passa para Could. Alertas por e-mail ou push já são Could.

## Riscos

| Risco | Impacto | Mitigação |
| --- | --- | --- |
| ADRs e código divergem (G2-09) | Ninguém sabe onde a ingestão deve gravar | Tech Lead fecha até 07/11 |
| Ingestão do PNCP (API externa, normalização, duplicação) é a história mais incerta | O feed continua preso ao Golden Set | Limitar a poucos editais por execução e usar fixtures gravadas como reserva |
| O PR #24 segue vermelho | A J4 não é testada e todo PR novo herda o CI quebrado | G2-01 até 06/11 |
| Um commit novo no PR #24 pode derrubar as aprovações, se a `main` tiver essa regra | Nova rodada de revisão | Conferir a regra em Settings antes de pedir o commit |
| Jornadas numeradas de forma diferente no Guia e no repositório | Relatórios e evidências citando jornadas diferentes | Alinhar a numeração na planning |

## Roteiro da demo do Gate 2

A demo é local, no computador, e dura cerca de 10 minutos. Só mostra o que o Gate 2 exige. A preparação é um clone limpo do repositório, `docker compose up --build` e o seed rodado, tudo feito por quem não escreveu o README.

| # | Tempo | Cena | Quem fala | O que mostrar | O que prova |
| --- | --- | --- | --- | --- | --- |
| 1 | 0:00 a 1:00 | Problema e objetivo | PO | A PME perde editais e prazos acompanhando à mão. O LicitaRadar ranqueia o feed do PNCP pelo perfil da empresa | Contexto do Gate 1 |
| 2 | 1:00 a 2:00 | Subir do zero | DevOps | Clone limpo, `docker compose up --build`, seed e o app abrindo ligado à API real | G2-02 e G2-03 |
| 3 | 2:00 a 3:30 | Login por papel | Front-End | Entrar como decisor da empresa A, depois como analista. Analista não vê Certidões e, ao abrir o endereço, recebe "Acesso negado". Sessão expirada volta ao login | J4, G2-01 |
| 4 | 3:30 a 5:00 | Perfil e feed ranqueado | Front-End | Mudar termos, UFs e faixa de valor no perfil e ver o feed mudar. Empresa B (só notebook no RS) tem outro feed | G2-05, score determinístico, seed de duas empresas |
| 5 | 5:00 a 6:15 | Buscar e filtrar | QA | Busca por "notebook", filtros de estado e modalidade com etiquetas removíveis, recarregar mantendo os filtros, busca sem resultado com o botão de limpar | J1, J2 |
| 6 | 6:15 a 7:30 | Certidões e erro tratado | PO | Renovar a CNDT com arquivo e nova data. Mostrar a recusa de um arquivo de 6 MB e a certidão saindo de "Precisam de atenção" | J3 |
| 7 | 7:30 a 8:30 | Isolamento entre empresas | Tech Lead | Empresa A tentando abrir registro da B e recebendo negação. Acesso direto ao Firestore negado | CT-SEG, regras deny-all |
| 8 | 8:30 a 9:15 | Ingestão | Back-End | Rodar a ingestão, ver editais novos entrarem, rodar de novo sem duplicar e o feed das duas empresas mudar | G2-11 |
| 9 | 9:15 a 10:00 | Qualidade | QA | Último PR verde com testes do front e do deny-all no CI, matriz de rastreabilidade e relatório de testes em `docs` | G2-04, G2-08 |

Checkup da véspera:

- [ ] Clone limpo e Compose testados por quem não escreveu o README
- [ ] Contas de decisor A, analista A e decisor B testadas no navegador que será usado
- [ ] Um PDF válido e um arquivo de 6 MB prontos para a cena 6
- [ ] PR mais recente verde e abas abertas com matriz e relatório
- [ ] Cada pessoa ensaiou a sua cena com o cronômetro

Plano de contingência: se o Compose falhar, usar o app com a API simulada (`VITE_API_MOCK=true`), que é o que usamos para testar J1 a J3, e avisar a banca na hora.
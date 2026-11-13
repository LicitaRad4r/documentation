# Escopo do MVP

## Resumo

O MVP entrega cinco jornadas do LicitaRadar numa versão que cabe no prazo: **configurar o perfil, ver o feed ranqueado, acompanhar editais, controlar certidões e receber o alerta de vencimento dentro da plataforma**. **Acompanhar editais** é a jornada nova do Checkpoint 3 (15/11/2026) e é *Must have* desde 09/11/2026.

Ficam para depois do MVP:

- o cadastro self-service com convites;
- a recuperação de senha própria;
- o contador de editais novos;
- os alertas por e-mail;
- o fluxo de recomendação entre Analista e Decisor.

**Data-limite para funcionalidades: 22/11/2026.** Depois disso, o time só corrige, testa e estabiliza até a demonstração de 05/12/2026.

---

## O que entra no MVP

A situação abaixo foi conferida no repositório do código em 09/11/2026, na `main`.

| # | Jornada | Versão do MVP | Histórias | Situação em 09/11 |
| :- | :--- | :--- | :--- | :--- |
| 1 | **Configurar o perfil** | Termos, UFs, modalidades e faixa de valor, numa tela | US08 | API na `main` (`GET/PUT /v1/perfil`). **Falta a tela**, que ainda não existe na `main` |
| 2 | **Ver o feed ranqueado** | Editais reais do PNCP, ordenados pelo Score, com filtros e detalhe | US07, US09, US10, US11, US12 | Feed, filtros, score e ingestão do PNCP na `main` (rota `POST /jobs/ingestao-pncp` e workflow). **Falta ativar a ingestão em produção** (secrets e variável do repositório). Detalhe (US12): a API está no contrato (`GET /v1/editais/{editalId}`), mas **falta a tela de detalhe no front** |
| 3 | **Acompanhar edital** | Quadro para marcar o edital de interesse e mudar a etapa: Interessado → Preparando → Enviado → Resultado | US20 | Não iniciado. **Jornada nova do Checkpoint 3: PR até 15/11** |
| 4 | **Controlar certidões** | Upload com validade, status Urgente / Atenção / Em dia / Vencida e renovação | US14, US15, US16 | API e tela de listagem e renovação na `main`. O front ainda sobe com `VITE_API_MOCK=true` por padrão. **Falta a tela de envio de certidão nova (US14)**: o front só tem a renovação. Os arquivos são gravados em disco local (ver riscos) |
| 5 | **Receber alertas** | Só dentro da plataforma: selo nas certidões a vencer | US16 | Selo pronto |
| — | **Acesso e segurança** | Login com e-mail e senha; Decisor e Analista com permissões separadas | US02, US06 | Segurança da API, tela de login e CORS na `main` |

A numeração desta tabela é a do Caderno. Em [Jornadas de Utilizador](../../jornadas.md) a numeração é outra (por exemplo, o acompanhamento é a Jornada 2); a tabela de correspondência está lá.

### Como as contas existem sem a tela de cadastro

As empresas e os usuários da demonstração são criados por um **script de seed**: duas empresas, cada uma com um Decisor e uma Analista. A separação de papéis continua real e demonstrável, porque é verificada no token e na API, e não na tela de convite.

---

## O que fica para depois do MVP

| Item | Histórias | Por quê |
| :--- | :--- | :--- |
| Cadastro self-service da empresa | US01 | O seed cobre a demonstração; a tela e as validações custam mais que o valor no MVP |
| Convites por e-mail | US04, US05 | Exige envio de e-mail, link de uso único e expiração |
| Recuperação de senha própria | US03 | Pode usar a recuperação padrão do Firebase Auth, sem tela nossa |
| Recomendação da Analista e painel do Decisor | US13, US17, US18, US19 | Ver a decisão abaixo |
| Contador de editais novos | US21 | Depende da ingestão do PNCP rodando em produção e de guardar a última visita de cada usuário. O alerta de vencimento das certidões (US16) já cobre a parte de alertas no MVP |
| Alertas por e-mail ou push | — | Exige serviço de envio; o alerta dentro da plataforma resolve a dor principal |

---

## Decisão: acompanhar edital × fluxo de decisão

Depois que um edital interessa, o MVP precisa de **uma** forma de seguir com ele. Havia duas opções:

| Critério | A. Acompanhar edital | B. Fluxo de decisão |
| :--- | :--- | :--- |
| Dor atacada | Perder o prazo de um edital já escolhido | Decisor sem tempo para triar |
| Quem mais ganha | Analista e Decisor | Decisor |
| Cobre o que acontece depois | Sim, até o Resultado | Não, para em "Em Disputa" |
| Tamanho | Um estado por edital e uma tela de quadro | Estrela, painel, aprovar, descartar e aviso |
| Está no Caderno B2B | Sim (jornada 3) | Não (o Caderno trata como *Could have*) |

**Escolha: opção A, *Must have*.** Ela cobre a jornada até o fim, ataca a perda de prazos (uma das duas dores centrais da Visão do Produto), segue o Caderno e é a menor das duas. A opção B vira evolução: a recomendação pode entrar depois como uma etapa antes de "Interessado".

A escolha era condicional, com data de corte em 15/11. Em 09/11/2026 ela passou a *Must have*, porque o Checkpoint 3 pede uma jornada nova da S5–S6 marcada como *Must* e com critério de aceite numérico, e o acompanhamento é a jornada nova do LicitaRadar. A ingestão do PNCP e a tela de perfil vêm antes.

Os critérios de aceite da US20 estão em [Histórias de Utilizador](../../../../backlog/historias.md#us20-acompanhar-edital).

---

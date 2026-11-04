# Escopo do MVP

**Status:** Proposta · **Data:** 03/11/2026 · **Responsáveis:** Maria Júlia (PO) e Júlia Neiva (Tech Lead)

## Resumo

O MVP entrega as **cinco jornadas** do LicitaRadar numa versão que cabe no prazo: **configurar o perfil, ver o feed ranqueado, acompanhar editais, controlar certidões e receber alertas dentro da plataforma**. Ficam para depois do MVP o cadastro self-service com convites, a recuperação de senha própria, os alertas por e-mail e o fluxo de recomendação entre Analista e Decisor.

**Data-limite para funcionalidades: 22/11/2026.** Depois disso, o time só corrige, testa e estabiliza até a demonstração de 05/12/2026.

---

## O que entra no MVP

| # | Jornada | Versão do MVP | Histórias | Situação em 03/11 |
| :- | :--- | :--- | :--- | :--- |
| 1 | **Configurar o perfil** | Termos, UFs, modalidades e faixa de valor, numa tela | US08 | Backend pronto; falta a tela |
| 2 | **Ver o feed ranqueado** | Editais reais do PNCP, ordenados pelo Score, com filtros e detalhe | US07, US09, US10, US11, US12 | Feed, filtros e score prontos no backend; **falta a ingestão do PNCP** |
| 3 | **Acompanhar edital** | Marcar edital de interesse e mudar a etapa: Interessado → Preparando → Enviado → Resultado | US20 (nova) | A fazer |
| 4 | **Controlar certidões** | Upload com validade, status Urgente / Atenção / Em dia / Vencida e renovação | US14, US15, US16 | Backend pronto; front feito, falta ligar à API |
| 5 | **Receber alertas** | Só dentro da plataforma: selo nas certidões a vencer e contador de editais novos desde a última visita | US16, US21 (nova) | Selo pronto; contador a fazer |
| — | **Acesso e segurança** | Login com e-mail e senha; Decisor e Analista com permissões separadas | US02, US06 | Segurança pronta; falta a tela de login |

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
| Alertas por e-mail ou push | — | Exige serviço de envio; o alerta dentro da plataforma resolve a dor principal |

---

## Decisão: acompanhar edital × fluxo de decisão

Depois que um edital interessa, o MVP precisa de **uma** forma de seguir com ele. Havia duas opções:

| Critério | A. Acompanhar edital | B. Fluxo de decisão |
| :--- | :--- | :--- |
| Dor atacada | Perder o prazo de um edital já escolhido | Decisor sem tempo para triar |
| Quem mais ganha | Analista e Decisor | Decisor |
| Cobre o que acontece depois | Sim, até o Resultado | Não, para em "Em Disputa" |
| Tamanho | Um estado por edital e uma tela de lista | Estrela, painel, aprovar, descartar e aviso |

**Escolha: opção A.** Ela cobre a jornada até o fim, ataca a perda de prazos (uma das duas dores centrais da Visão do Produto) e é a menor das duas. A opção B vira evolução: a recomendação pode entrar depois como uma etapa antes de "Interessado".

---

## Histórias novas

### US20 - Acompanhar Edital
> **Como** Analista ou Decisor, **eu quero** marcar um edital do feed como de interesse e atualizar a sua etapa, **para que** a equipa saiba o que está em andamento e não perca prazos.

- [ ] **Dado que** clico em "Acompanhar" num edital do feed, **quando** a ação é gravada, **então** o edital aparece na lista "Acompanhados" com a etapa "Interessado".
- [ ] **Dado que** um edital está acompanhado, **quando** mudo a etapa, **então** só posso escolher Interessado, Preparando, Enviado ou Resultado, e a lista mostra a nova etapa.
- [ ] **Dado que** a lista "Acompanhados" carrega, **quando** há editais, **então** eles vêm ordenados pelo prazo mais próximo, e os que vencem em até 3 dias úteis aparecem destacados.
- [ ] **Dado que** sou de outra empresa, **quando** tento ler ou mudar um edital acompanhado, **então** a API responde `403`.

### US21 - Contador de Editais Novos
> **Como** Analista, **eu quero** ver quantos editais novos entraram no meu feed desde a última visita, **para que** saiba se vale a pena revisar o feed hoje.

- [ ] **Dado que** a ingestão gravou editais novos aderentes ao meu perfil, **quando** abro a plataforma, **então** vejo "N editais novos" no menu do Feed.
- [ ] **Dado que** abri o feed, **quando** volto ao menu, **então** o contador volta a zero.

---

## Prazos internos

| Até | Entrega |
| :--- | :--- |
| 07/11 | Backend na `main`; front ligado à API; telas de login e perfil |
| 10/11 | Ingestão do PNCP com dados reais e sem duplicados |
| 15/11 | Sistema publicado e acessível por uma URL pública |
| 22/11 | Acompanhar edital (US20) e contador de novos (US21) — **fim das funcionalidades** |
| 29/11 | Só correções, testes, desempenho e ensaio de rollback |
| 05/12 | Demonstração |

## Riscos e plano B

| Risco | Plano B |
| :--- | :--- |
| Ingestão do PNCP atrasa além de 10/11 | Gravar uma carga real do PNCP como arquivo e carregar pelo seed. O feed mostra dados reais, e a coleta automática vira evolução |
| Arquivos das certidões somem no servidor gratuito a cada reinício | Decidir até 12/11 onde guardar os arquivos; se não houver opção gratuita, a demo guarda só os metadados e a validade |
| Acompanhar edital não fica pronto até 22/11 | A jornada 3 sai do MVP; a demo mostra as jornadas 1, 2, 4 e 5 |

## Decisão

| Escopo aprovado | Data | Decidido por | Aprovado por |
| :--- | :--- | :--- | :--- |
| *(Sim / Com ajustes)* | *(dd/mm/aaaa)* | *(nomes)* | *(nome)* |
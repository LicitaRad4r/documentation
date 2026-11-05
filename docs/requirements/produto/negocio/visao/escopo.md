# Escopo do MVP

**Status:** Proposta · **Data:** 04/11/2026 · **Responsáveis:** Maria Júlia (PO)

## Resumo

O MVP entrega quatro jornadas do LicitaRadar numa versão que cabe no prazo: **configurar o perfil, ver o feed ranqueado, controlar certidões e receber o alerta de vencimento dentro da plataforma**. **Acompanhar editais** é a quinta jornada e entra se estiver em PR até 15/11/2026.

Ficam para depois do MVP:

- o cadastro self-service com convites;
- a recuperação de senha própria;
- o contador de editais novos;
- os alertas por e-mail;
- o fluxo de recomendação entre Analista e Decisor.

**Data-limite para funcionalidades: 22/11/2026.** Depois disso, o time só corrige, testa e estabiliza até a demonstração de 05/12/2026.

---

## O que entra no MVP

A situação abaixo foi conferida no repositório do código em 04/11/2026, na `main` e nas branches abertas.

| # | Jornada | Versão do MVP | Histórias | Situação em 04/11 |
| :- | :--- | :--- | :--- | :--- |
| 1 | **Configurar o perfil** | Termos, UFs, modalidades e faixa de valor, numa tela | US08 | API na `main` (`GET/PUT /v1/perfil`). **Falta a tela**, que ainda não existe em nenhuma branch |
| 2 | **Ver o feed ranqueado** | Editais reais do PNCP, ordenados pelo Score, com filtros e detalhe | US07, US09, US10, US11, US12 | Feed, filtros e score na `main`. **Falta a ingestão do PNCP**: não há adapter nem a rota `/jobs/ingestao-pncp`, e o workflow está desligado; o feed lê só o seed golden. Detalhe (US12): o front chama `/editais/{id}`, mas a rota não foi encontrada na API *(confirmar com o Backend)* |
| 3 | **Acompanhar edital** *(condicional)* | Marcar edital de interesse e mudar a etapa: Interessado → Preparando → Enviado → Resultado | US20 | Não iniciado. **Entra se estiver em PR até 15/11** |
| 4 | **Controlar certidões** | Upload com validade, status Urgente / Atenção / Em dia / Vencida e renovação | US14, US15, US16 | API e tela na `main`. O front ainda sobe com `VITE_API_MOCK=true` por padrão. Upload de certidão nova (US14): só há a rota de renovação *(confirmar)* |
| 5 | **Receber alertas** | Só dentro da plataforma: selo nas certidões a vencer | US16 | Selo pronto |
| — | **Acesso e segurança** | Login com e-mail e senha; Decisor e Analista com permissões separadas | US02, US06 | Segurança da API na `main`. Tela de login em `feat/checkpoint-front` e CORS em `fix/cors-front`, **ambos sem merge** |

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
| Contador de editais novos | US21 | Depende da ingestão do PNCP, que ainda não existe, e de guardar a última visita de cada usuário. O alerta de vencimento das certidões (US16) já cobre a parte de alertas no MVP |
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
| Está no Caderno B2B | Sim (jornada 3) | Não (o Caderno trata como *Could have*) |

**Escolha: opção A, condicional.** Ela cobre a jornada até o fim, ataca a perda de prazos (uma das duas dores centrais da Visão do Produto), segue o Caderno e é a menor das duas. A opção B vira evolução: a recomendação pode entrar depois como uma etapa antes de "Interessado".

Como a US20 ainda não foi iniciada e a ingestão do PNCP vem antes, a opção A entra com **data de corte em 15/11**. Se não houver PR aberto até lá, a jornada 3 sai do MVP e a demo mostra as jornadas 1, 2, 4 e 5.

Os critérios de aceite da US20 estão em [Histórias de Utilizador](../../requisitos/historias.md#us20-acompanhar-edital).

---

## Prazos internos

| Até | Entrega |
| :--- | :--- |
| 07/11 | Front ligado à API (login e CORS na `main`, `VITE_API_MOCK=false` por padrão); tela de perfil |
| 10/11 | Ingestão do PNCP com dados reais e sem duplicados |
| 15/11 | Sistema publicado e acessível por uma URL pública; **corte da US20**: PR aberto ou a história sai do MVP |
| 22/11 | Acompanhar edital (US20), se não foi cortado — **fim das funcionalidades** |
| 29/11 | Só correções, testes, desempenho e ensaio de rollback |
| 05/12 | Demonstração |

## Riscos e plano B

| Risco | Plano B |
| :--- | :--- |
| Ingestão do PNCP atrasa além de 10/11 | Gravar uma carga real do PNCP como arquivo e carregar pelo seed. O feed mostra dados reais, e a coleta automática vira evolução |
| Arquivos das certidões somem no servidor gratuito a cada reinício | Decidir até 12/11 onde guardar os arquivos; se não houver opção gratuita, a demo guarda só os metadados e a validade |
| Acompanhar edital não tem PR até 15/11 | A jornada 3 sai do MVP; a demo mostra as jornadas 1, 2, 4 e 5 |

## Decisão

| Escopo aprovado | Data | Decidido por | Aprovado por |
| :--- | :--- | :--- | :--- |
| *(Sim / Com ajustes)* | *(dd/mm/aaaa)* | *(nomes)* | *(nome)* |
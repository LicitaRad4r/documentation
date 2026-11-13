# Jornadas de Utilizador

Este documento mapeia as jornadas ponta a ponta do LicitaRadar. Cada jornada descreve a experiência do utilizador desde o gatilho inicial até à retenção e liga cada etapa às Histórias de Utilizador (US).

As jornadas estão ordenadas por prioridade: primeiro as que entram no MVP e, no fim, a que fica para depois do MVP.

## Visão geral

| Jornada | Persona | Histórias | No MVP? |
| :--- | :--- | :--- | :--- |
| 1 — Triagem de oportunidades | Mariana (Analista) | US07, US08, US09, US10, US11, US12 | **Sim.** |
| 2 — Acompanhamento até o resultado | Mariana (Analista) | US20 | **Sim.** É a jornada nova do Checkpoint 3 (15/11/2026). |
| 3 — Conformidade documental | Bruno (Decisor) | US14, US15, US16 | **Sim.** O alerta de vencimento (US16) é o alerta do MVP. |
| 4 — Acesso com isolamento de segurança | Bruno e Mariana | US02, US06 | **Sim**, com as contas criadas por script de seed. |
| 5 — Funil de decisão do Decisor | Bruno (Decisor) | US13, US17, US18, US19 | **Após o MVP.** |

---

## Jornada 1 — Mariana (Analista) faz a Triagem de Oportunidades

1. **Descoberta:** Mariana tem a tarefa diária e frustrante de procurar novos editais em múltiplos diários oficiais governamentais, perdendo muito tempo com PDFs irrelevantes.
2. **Onboarding / Preparação:** Ela entra no LicitaRadar e configura o perfil da sua empresa, definindo palavras-chave (ex: "software", "computador"), estados de interesse, modalidades e faixa de valor aceitável (US08).
3. **Ação Principal:** O sistema faz o trabalho pesado: a ingestão diária traz os editais do PNCP (US07) e calcula o Score de Aderência (US11). Mariana abre o seu *Feed* e visualiza a lista já ranqueada (US09). Aplica filtros para refinar a busca (US10) e abre o detalhe dos editais mais promissores (US12).
4. **Retenção / Acompanhamento:** Mariana acede à plataforma diariamente porque a ingestão traz novas licitações de forma automática. Ela clica em "Acompanhar" nos melhores editais e segue o andamento na Jornada 2 (US20).

```mermaid
journey
    title Jornada 1 - Mariana faz a triagem de oportunidades
    section Descoberta
      Busca em portais: 1: Mariana
      Lê PDFs inúteis: 1: Mariana
    section Preparação
      Perfil (US08): 3: Mariana
    section Ação Principal
      Coleta PNCP (US07): 4: Sistema
      Calcula score (US11): 4: Sistema
      Vê o feed (US09): 5: Mariana, Sistema
      Filtra (US10): 4: Mariana
      Detalhe (US12): 4: Mariana
    section Retenção
      Volta todo dia: 5: Mariana
```

---

## Jornada 2 — Mariana (Analista) acompanha os editais até o resultado

1. **Descoberta:** Depois de escolher os editais, a equipa controla o andamento numa planilha. Um prazo passa despercebido e a proposta não é enviada a tempo.
2. **Onboarding / Preparação:** No feed, Mariana clica em "Acompanhar" nos editais que a empresa vai disputar. Eles entram no quadro "Acompanhados", na coluna "Interessado" (US20).
3. **Ação Principal:** Conforme o trabalho avança, Mariana move o edital para a coluna "Preparando" e depois para "Enviado". Em cada coluna, os editais ficam ordenados pelo prazo mais próximo, e os que vencem em até 3 dias (dias corridos) aparecem destacados (US20).
4. **Retenção / Acompanhamento:** Ao abrir o quadro "Acompanhados", Mariana confere o que está perto do prazo. Quando sai o resultado, move o edital para a coluna "Resultado", regista "Venceu" ou "Não venceu" e o edital fecha o ciclo.

```mermaid
journey
    title Jornada 2 - Mariana acompanha os editais até o resultado
    section Descoberta
      Controla em planilha: 1: Mariana
      Perde um prazo: 1: Mariana
    section Preparação
      Acompanha (US20): 4: Mariana
    section Ação Principal
      Muda a etapa (US20): 4: Mariana
      Destaca prazo curto: 5: Sistema
    section Retenção
      Regista resultado: 5: Mariana
```

---

## Jornada 3 — Bruno (Decisor) gere a Conformidade Documental

1. **Descoberta:** Bruno sabe que vencer uma licitação não serve de nada se a empresa for inabilitada por ter uma Certidão Negativa de Débitos vencida no dia da disputa.
2. **Onboarding / Preparação:** Bruno faz o upload das certidões legais da empresa e cadastra as datas de validade exatas de cada uma (US14).
3. **Ação Principal:** O sistema calcula os prazos a cada consulta. Certo dia, ao aceder à plataforma, Bruno depara-se com um alerta "Urgente" indicando que uma certidão expira em 12 dias (US16).
4. **Retenção / Acompanhamento:** Bruno obtém o novo documento no órgão emissor, faz a renovação e substituição do ficheiro na plataforma (US15). O alerta desaparece e a conformidade é mantida.

```mermaid
journey
    title Jornada 3 - Bruno mantém as certidões em dia
    section Descoberta
      Medo de inabilitar: 1: Bruno
    section Preparação
      Envia (US14): 3: Bruno
    section Ação Principal
      Calcula prazos: 4: Sistema
      Alerta (US16): 2: Bruno, Sistema
    section Retenção
      Renova (US15): 4: Bruno
      Segue habilitada: 5: Bruno
```

---

## Jornada 4 — A Equipa entra com Acessos Separados

No MVP, as empresas e os utilizadores são criados por script de seed. O cadastro self-service (US01) e o convite por e-mail (US04, US05) ficam para depois do MVP.

1. **Descoberta:** A organização decidiu adotar o LicitaRadar. Bruno precisa de colocar a sua Analista a trabalhar na plataforma, mas exige que ela não tenha acesso às certidões da empresa.
2. **Onboarding / Preparação:** As contas da empresa são criadas por script: Bruno como Decisor e Mariana como Analista, ligados à mesma empresa.
3. **Ação Principal:** Mariana faz login com e-mail e palavra-passe (US02). O token identifica o seu papel de "Analista" e a empresa a que pertence.
4. **Retenção / Acompanhamento:** A confiança no sistema vem do isolamento: sempre que Mariana navega na aplicação, a área de "Certidões" permanece oculta e a API recusa o acesso; nenhuma empresa vê os dados de outra (US06).

```mermaid
journey
    title Jornada 4 - A equipa entra com acessos separados
    section Descoberta
      Separar acessos: 2: Bruno
    section Preparação
      Contas por script: 4: Sistema
    section Ação Principal
      Login (US02): 4: Mariana
      Vira Analista: 5: Sistema
    section Retenção
      Bloqueio (US06): 5: Sistema
      Confia no sistema: 5: Bruno
```

---

## Jornada 5 — Bruno (Decisor) avalia o Funil de Editais

Esta jornada ficou fora do MVP. No lugar dela entra a Jornada 2, de acompanhamento. Ver o [Escopo do MVP](negocio/visao/escopo.md).

1. **Descoberta:** Bruno precisa de decidir em quais processos a empresa vai gastar recursos para concorrer, mas não tem tempo para ler centenas de editais brutos.
2. **Onboarding / Preparação:** Ele acede ao seu Dashboard de Decisão exclusivo. O sistema filtra o ruído e exibe apenas os editais que a Mariana marcou como "Recomendados" (US13, US17).
3. **Ação Principal:** Bruno avalia rapidamente o resumo e os valores de cada oportunidade recomendada. Ele decide "Aprovar" o edital para iniciar a montagem da proposta (US18) ou "Descartar", removendo-o da fila (US19).
4. **Retenção / Acompanhamento:** Os editais aprovados movem-se para a fila "Em Disputa", permitindo que Bruno acompanhe visualmente o pipeline de vendas B2B da sua empresa.

```mermaid
journey
    title Jornada 5 - Bruno avalia o funil de editais
    section Descoberta
      Sem tempo: 1: Bruno
    section Preparação
      Dashboard (US17): 4: Bruno, Sistema
    section Ação Principal
      Avalia resumo: 4: Bruno
      Aprova (US18): 5: Bruno
      Descarta (US19): 4: Bruno, Mariana
    section Retenção
      Em Disputa: 5: Bruno
```
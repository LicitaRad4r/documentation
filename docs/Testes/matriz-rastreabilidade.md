# Matriz de rastreabilidade: requisito → critério de aceite → caso de teste (CT) → resultado

Colunas **Teste(s)** e **Resultado** vazias = caso ainda não implementado no repositório. Hoje está implementado e executado o **CT-SEG-010**.

## Jornada 1: Triagem Inteligente no Feed (CT-FEED-001 a 009)
**Rastreabilidade:** US07, US08, US09, US11, US12,RF04, RF06, ADR-0007 e ADR-0008

| CT | Cenário | Requisitos | Critério de aceite | Valores-limite (BVA) | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **CT-FEED-001** | Ordenação por Score | US09,US12 ,RF04, RF06 | Lista em ordem não crescente de Score | -1, 0, 1, 99, 100, 101 | Unitário e integração |  |  |
| **CT-FEED-002** | Desempate | US09, RF04 | Mesmo Score: prazo mais próximo, publicação mais recente, ID | Diferença de 1 min/1 dia no prazo; ID_A vs ID_B (alfabeto/numérico) | Unitário |  |  |
| **CT-FEED-003** | Isolamento de perfil | US08, US11, RF06, ADR-0008 | Edital A: 100 (Empresa A) e 75 (B); edital C: 75 (A) e 100 (B); D, E e F descartados | Matriz com 0 correspondências, 1 correspondência, N correspondências | Integração |  |  |
| **CT-FEED-004** | Sem chamada ao PNCP | US07,US12 ,RF04 ,ADR-0007 | Nenhuma requisição externa ao abrir o feed | Fronteira entre 0 e 1 requisição externa | Integração |  |  |
| **CT-FEED-005** | Carregamento | US09, RF04 | Estado de loading aparece antes da lista | Tempo de resposta = 0ms, < 100ms (imediato), > 500ms (Loader visível) | E2E |  |  |
| **CT-FEED-006** | Seed vazio | US09, RF04 | Mensagem "sem editais ingeridos" | Quantidade de editais na base = 0, 1, 2 | E2E |  |  |
| **CT-FEED-007** | Todos descartados | US09, US11, RF06 | Mensagem do perfil, com orientação | Editais compatíveis = 0, 1 | E2E |  |  |
| **CT-FEED-008** | API falha | US09, RF04 | Mensagem de erro com "Tentar novamente", sem ecrã em branco | Fronteira entre os limites de retentativas automáticas (Ex: 0, 3, 4) | E2E |  |  |
| **CT-FEED-009** | Paginação | US09,RF04,RNF05 | Página limitada ao pageSize, sem repetição entre páginas | pageSize = 0, 1, 2, 99, 100, 101; page = 0, 1 | Integração |  |  |

## Jornada 2: Busca Direcionada e Filtros Dinâmicos (CT-FILT-001 a 009)
**Rastreabilidade:** US10, US11 ,RF05, RF06 ,ADR-0003, ADR-0008 e página Filtros

| CT | Cenário | Requisitos | Critério de aceite | Valores-limite (BVA) | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **CT-FILT-001** | valorMin=50000 | US10, RF05 | valorMin=50000: só valor >= 50000; Score e ordem iguais aos do feed sem filtro | `-0.01`, `0`, `0.01`; `49999.99`, `50000.00`, `50000.01`; `min = max` | Integração |  |  |
| **CT-FILT-002** | Filtro impossível (valorMin de 1 bilhão) | US10, RF05 | "Sem resultados para estes filtros" e botão "Limpar filtros" | `valorMin = 1.000.000.000` → Mensagem *"Sem resultados para estes filtros"* e botão *"Limpar filtros"* | E2E |  |  |
| **CT-FILT-003** | Clicar em "Limpar filtros" | US10, RF05 | Limpar filtros devolve o feed completo na ordem original | Transição: N filtros aplicados → 0 filtros (feed original completo) | E2E |  |  |
| **CT-FILT-004** | Filtro não recupera descartado | US10, US11, RF05, RF06 | Edital descartado (caso D) não reaparece com valorMin/valorMax que o incluiriam | Parâmetros de busca englobando o escopo do edital D → permanência do descarte | Integração |  |  |
| **CT-FILT-005** | Filtros não recalculam | US10, US11, RF05, RF06, ADR-0008 | Score de cada edital igual com e sem filtros | Diferença de Score antes e depois do filtro = `0` | Unitário e integração |  |  |
| **CT-FILT-006** | Palavra-chave | US10,RF05, RF06, ADR-0008 | "NOTEBOOK" e "Notébook" encontram o mesmo edital | Termos com acento vs sem acento gerando idênticos resultados (`"NOTEBOOK"` e `"Notébook"`) | Unitário |  |  |
| **CT-FILT-007** | Parâmetro inválido | US10,RF05, RNF05 | 400 com mensagem; ecrã mantém a lista anterior | Envio de parâmetro inválido → HTTP `400` e preservação da lista anterior no ecrã | Integração |  |  |
| **CT-FILT-008** | Erro durante a pesquisa | US10, RF05 | Erro na pesquisa: mensagem com "Tentar novamente"; filtros mantidos | Estado dos inputs de filtro preservados intactos após a falha HTTP | E2E |  |  |
| **CT-FILT-009** | Desempenho | US10, RF05, RNF03 | p95 de leitura <= 800 ms com o seed | Limiar estrito: `799 ms` vs `801 ms` (calculado sobre 200 amostras) | Integração |  |  |

## Jornada 3: Monitorização de Risco Documental e Revalidação (CT-CERT-001 a 010)
**Rastreabilidade:** US14, US15, US16, US03, RF08 a RF10 e RNF05

| CT | Cenário | Requisitos | Critério de aceite | Valores-limite (BVA) | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **CT-CERT-001** | Limites da classificação: -1, 0, 15, 16, 30 e 31 dias | US08, RF08 | -1, 0, 15, 16, 30 e 31 dias: Vencida, Urgente, Urgente, Atenção, Atenção e Em dia | -1 e 0 (Vencida -> Urgente); 15 e 16(Urgente -> Atenção); 30 e 31 (Atenção -> Em dia) | Unitário |  |  |
| **CT-CERT-002** | Painel com o seed | US16, RF10 | Certidões em alerta, da mais urgente para a menos; a de 31 dias não aparece | Limiar estrito: 30`dias (aparece no painel) vs 31 dias (some do painel) | Integração e E2E |  |  |
| **CT-CERT-003** | Renovar com dados válidos | US15, RF08, RF10 | Etiqueta recalculada na hora, sem avançar o relógio | Recálculo imediato do status no payload de resposta HTTP `200` | E2E |  |  |
| **CT-CERT-004** | Nova validade a mais de 30 dias | US15, US16, RF10 | Nova validade a mais de 30 dias: o alerta some | Fronteira exata: 30 dias (continua no painel) vs 31 dias (o alerta desaparece) | Integração e E2E |  |  |
| **CT-CERT-005** | Nova validade a 20 dias | US15, US16, RF10 | Nova validade a 20 dias: o alerta continua como Atenção | Fronteiras da faixa de Atenção: 16 dias vs 30 dias | Integração |  |  |
| **CT-CERT-006** | Data no passado, ou não posterior à atual | US14, US15, RF09, RNF05 | Data no passado ou não posterior à atual: 400 com o campo; nada gravado | `hoje`, `atual`, `atual + 1 dia` ->  HTTP 400 com erro no campo validade e base intacta | Integração |  |  |
| **CT-CERT-007** | Ficheiro inválido (tipo ou mais de 5MB) | US14, US15, RF08, RNF05 | Arquivo inválido (tipo ou > 5MB): 400; certidão anterior inalterada | `5 MB` exatos (passa) vs `5 MB + 1 byte` ou formato inválido -> HTTP `400` e ficheiro anterior inalterado | Integração |  |  |
| **CT-CERT-008** | Painel sem alertas | US16, RF10 | "Todas as certidões estão em dia" | `0` itens em risco no array de listagem de alertas | E2E |  |  |
| **CT-CERT-009** | Falha de rede no envio | US15, RF08 | Falha de rede no envio: erro com "Tentar novamente"; certidão anterior mantida | Estado prévio do registo preservado após falha HTTP (ex: `500` / timeout) | E2E |  |  |
| **CT-CERT-010** | Rotina das 00h00 | US16, RF10 | Com relógio controlado, as etiquetas mudam ao virar o dia | Instante `23:59:59` BRT (`02:59:59Z`) vs `00:00:00` BRT (`03:00:00Z`) alterando os dias restantes | Unitário |  |  |

## Jornada 4: Barreira de Segurança e Acesso Cruzado (CT-SEG-001 a 011)
**Rastreabilidade:** US01 a US06, RF01 a RF03 e RNF06 e ADR-0005

| CT | Quem | Pedido | API esperada | Interface | Requisitos | Critério de aceite | Nível | Teste(s) | Resultado |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **CT-SEG-001** | Sem sessão | GET /feed sem token | 401 | Redireciona ao login | US02, RF01 | GET /feed sem token: 401 e redireciona ao login | API e E2E |  |  |
| **CT-SEG-002** | Token adulterado | GET /feed com assinatura inválida | 401 | Redireciona ao login | US02, RF01 | Assinatura inválida: 401 e redireciona ao login | API |  |  |
| **CT-SEG-003** | Token expirado | GET /feed com token vencido | 401 | Login, com aviso de sessão expirada | US02, US03, RF01 | Token expirado: 401 e login com aviso de sessão expirada | API e E2E |  |  |
| **CT-SEG-004** | Token válido sem orgId ou role | GET /feed | 403 | Acesso Negado | US01, US04, US05, RF01 , RF02, RNF06 | Token válido sem orgId ou role: 403 | API |  |  |
| **CT-SEG-005** | Analista | GET /certidoes | 403 | Página "Acesso Negado (403)"; menu sem a aba | US06, RF03 | Analista em GET /certidoes: 403, página Acesso Negado, menu sem a aba | API e E2E |  |  |
| **CT-SEG-006** | Analista | PUT /certidoes/{id}/renovacao | 403; nada gravado | Não aplicável | US06, RF03 | Analista em PUT renovacao: 403 e nada gravado | API |  |  |
| **CT-SEG-007** | Decisor da empresa A | GET /certidoes/{id da B} | 403 | Acesso Negado | RNF06 | Decisor da A lendo certidão da B: 403 | API |  |  |
| **CT-SEG-008** | Decisor da empresa A | PUT /certidoes/{id da B}/renovacao | 403; certidão da B inalterada | Não aplicável | RNF06 | Decisor da A renovando certidão da B: 403 e certidão da B inalterada | API |  |  |
| **CT-SEG-009** | Analista da empresa A | GET /perfil com orgId da B | 403 | Acesso Negado | RNF06 | Analista da A em GET /perfil com orgId da B: 403 | API |  |  |
| **CT-SEG-010** | Cliente do Firebase | Leitura e escrita direta no Firestore, sem passar pela API | Negado pelas regras | Não aplicável | RNF06 | Leitura e escrita direta no Firestore, sem a API: negado pelas regras | Regras | `server/tests/integration/firestore-deny.all.test.ts` (21 testes) | **Passou** (21 de 21, 28/10/2026) |
| **CT-SEG-011** | Qualquer bloqueio | Verifica corpo e log | Sem dados do recurso nem pessoais | Não aplicável | RNF06 | Bloqueio sem dados do recurso nem pessoais (corpo e log) | API |  |  |

## Execução do CT-SEG-010

- Ambiente: emulador do Firestore em Docker; `npm run test:int` em `server/`.
- Data: 28/10/2026. Resultado: 21 passaram, 0 falharam. Regra testada: `firestore.rules` em deny-all (`allow read, write: if false;`).
- Verifica, para sem login, logado sem claim `empresaId`, decisor da A e analista da A: não lê o próprio documento nem o de outra empresa, não lista a coleção, não cria/atualiza/apaga, não acessa `editais`, `scores` e `usuarios`; e os dados seguem intactos depois das tentativas (4 × 5 + 1 = 21).

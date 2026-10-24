## Jornada 1: Triagem Inteligente no Feed (CT-FEED-001 a 009)

| CT | Campo / Domínio | Regra (Fonte) | Classes Válidas (EP) | Classes Inválidas (EP) | Valores-Limite (BVA) |
| :---: | :--- | :--- | :--- | :--- | :--- |
| CT-FEED-001 | Ordenação por score | Nota 0 a 100; listar ordem não crescente | [0, 100] (Ex: 0, 75, 100) | Nota < 0 ou Nota > 100 (Ex: -1, 101) | -1, 0, 1, 99, 100, 101 |
| CT-FEED-002 | Desempate | 1º prazo mais próximo, 2º publicação mais recente, 3º ID | Itens com scores idênticos | n/a | Diferença de 1 min/1 dia no prazo; ID_A vs ID_B (alfabeto/numérico) |
| CT-FEED-003 | Isolamento do Perfil | Verificar as notas (Edital x Empresa) | Edital A: Empresa A (100), Empresa B (75) | Empresas C, D, E (Perfis sem correspondência) | Matriz com 0 correspondências, 1 correspondência, N correspondências |
| CT-FEED-004 | Sem chamada ao PNCP | API lendo somente os scores gravados | Chamadas HTTP externas = 0 ao abrir GET /feed | Chamadas HTTP externas >= 1 ao PNCP | Fronteira entre 0 e 1 requisição externa |
| CT-FEED-005 | Carregamento | Indicador de loading | Exibição do esqueleto/loader durante o processamento | Ausência de loader (tela travada ou em branco) | Tempo de resposta = 0ms, < 100ms (imediato), > 500ms (Loader visível) |
| CT-FEED-006 | Seed vazio | Mensagem de "Sem editais ingeridos" | Banco de dados de editais zerado | Banco de dados possui editais, mas não renderiza a lista | Quantidade de editais na base = 0, 1, 2 |
| CT-FEED-007 | Todos descartados | Mensagem de "Nenhum edital combina..." | Filtro resulta em 0 editais compatíveis | Filtro resulta em >= 1 edital compatível, mas exibe mensagem | Editais compatíveis = 0, 1 |
| CT-FEED-008 | API Falha | Mensagem de erro com "tentar novamente" | Retorno HTTP Status 200 OK | Retorno HTTP Status 4xx, 5xx (Ex: 500) | Fronteira entre os limites de retentativas automáticas (Ex: 0, 3, 4) |
| CT-FEED-009 | Paginação | Página limitada ao pageSize, sem repetição | page >= 1; pageSize de 1 a 100 | page <= 0; pageSize <= 0 ou > 100; não inteiros | pageSize = 0, 1, 2, 99, 100, 101; page = 0, 1 |

## Jornada 2: Busca Direcionada e Filtros Dinâmicos (CT-FILT-001 a 009)

| CT | Campo / Domínio | Regra (Fonte) | Classes Válidas (EP) | Classes Inválidas (EP) | Valores-Limite (BVA) |
| :---: | :--- | :--- | :--- | :--- | :--- |
| CT-FILT-001 | Filtro valor mínimo/valor máximo | Filtro não pode buscar fora da faixa de valor estipulada | Ausente; ValorMin; ValorMax | Texto; número negativo ; valorMin > valorMax | -1, 0, 0.01, 49999, 50000, 50001|
| CT-FILT-002 | Filtro Impossível | Filtros não condizentes com perfil; ex: valorMin: 1 bilhão | valorMin: 50000; valorMax: 1000000 | 1000000000; 10000000000 | -1000000000; 1000000000 |
| CT-FILT-003 | Limpar filtros | Restauração do feed original | Botão Limpar filtro com filtros ativos | Manter os filtros aplicados após o reset | Transição de estados N filtros aplicados -> 0 filtros aplicados |
| CT-FILT-004 | Filtros não recuperam editais descartados pelo perfil | Filtro restritivo sobre editais aderentes | Edital D (fora da faixa de perfil) reaparecer com o filtro | Pesquisar pelos parâmetros de busca incluindo o escopo do edital D |
| CT-FILT-005 | Filtros não recalculam | Subconjunto filtrado, preservando notas e a ordem relativa | Modificação de pontuação ou reordenação ao refinar por texto/UF/valor | Diferença score antes e depois do filtro = 0 |
| CT-FILT-006 | Palavra chave | Normalizador idêntico ao score (sem maiúsculas/acentos/plural simples | NOTEBOOK; notebook; Notébook; notebooks | Strings vazias ou mal formatadas; números  | Termos com acento vs sem acento gerando matchs idênticos |
| CT-FILT-007 | Parâmetro inválido | Verificação de tipos corretos e consistentes | uf/modalidade (DF/TI) | Tipos incorretos; datas inválidas ; strings fora do padrão;  parâmetros fora da lista | retorno HTTP 400; ecra mantendo lista anterior|
| CT-FILT-008 | Erro durante a pesquisa | Mensagem de erro com "tentar novamente" |Erro de rede ou indisponibilidade temporária durante refinamento com mensagem de erro amigável | Perda dos filtros aplicados ao falhar a requisição| Estado do input de busca e filtros preservados após o erro HTTP|
| CT-FILT-009 | Desempenho |Meta de latência p95≤800 ms com o seed| Execução rápida e bem sucedida | Latência p95>800 ms | Teste com limiar estrito: 700, 900 |

## Jornada 3: Monitorização de Risco Documental e Revalidação (`CT-CERT-001` a `010`)
**Rastreabilidade:** US08, US09, US10, US03 e RF08 a RF10

| CT | Campo / Domínio | Regra (Fonte) | Classes Válidas (EP) | Classes Inválidas (EP) | Valores-Limite (BVA) |
| :---: | :--- | :--- | :--- | :--- | :--- |
| **CT-CERT-001** | Limites da classificação | Dias até a validade em fuso de Brasília: <0 Vencida; 0-15 Urgente; 16-30 Atenção; >31 Em dia | -2 (vencida), 3 (urgente), 20 (atenção), 45 (em dia) | n/a | -1 e 0 (Vencida -> Urgente); 15 e 16(Urgente -> Atenção); 30 e 31 (Atenção -> Em dia) |
| **CT-CERT-002** | Painel com o seed | Certidões em alerta ordenadas da mais urgente para a menos; a de 31 dias não aparece | Seed com validades em 0, 3, 15, 16, 30, 31 | Omitir certidões críticas ou exibir certidões válidas 31 | Limiar estrito: 30`dias (aparece no painel) vs 31 dias (some do painel) |
| **CT-CERT-003** | Renovar com dados válidos | Etiqueta recalculada na hora, sem avançar o relógio até à meia-noite | Data futura válida enviada com ficheiro correto | Enviar sem dados ou com data inválida | Recálculo imediato do status no payload de resposta HTTP `200` |
| **CT-CERT-004** | Nova validade a mais de 30 dias | O alerta some do painel | Nova validade + 31 dias | Validade dentro da janela de alerta 30 dias | Fronteira exata: 30 dias (continua no painel) vs 31 dias (o alerta desaparece) |
| **CT-CERT-005** | Nova validade a 20 dias | O alerta continua, agora com etiqueta de Atenção | Nova validade entre 16 e 30 dias a contar de hoje | Validade fora da faixa de atenção | Fronteiras da faixa de Atenção: 16 dias vs 30 dias |
| **CT-CERT-006** | Data no passado, ou não posterior à atual | 400 com o campo indicado (`validade`); nada é gravado | Datas estritamente futuras e posteriores à atual | Passado (`hoje - 1`), hoje (`hoje`), igual ou anterior à atual (`atual`) | `hoje`, `atual`, `atual + 1 dia` ->  HTTP 400 com erro no campo validade e base intacta |
| **CT-CERT-007** | Ficheiro inválido (tipo ou mais de 5MB) | 400; certidão anterior inalterada | PDF, JPG ou PNG com tamanho  > 5 MB | Extensões proibidas (`.txt`, `.exe`), ficheiros vazios (`0 bytes`), tamanho > 5 | `5 MB` exatos (passa) vs `5 MB + 1 byte` ou formato inválido -> HTTP `400` e ficheiro anterior inalterado |
| **CT-CERT-008** | Painel sem alertas | Mensagem exata: "Todas as certidões estão em dia"* | Empresa cujas certidões possuem validade + 31 dias | Exibir painel com itens vencidos ou urgentes | `0` itens em risco no array de listagem de alertas |
| **CT-CERT-009** | Falha de rede no envio | Erro com "Tentar novamente"; certidão anterior mantida | Tentativa de submissão com interrupção simulada de rede | Apagar ou corromper dados da certidão em caso de falha de requisição | Estado prévio do registo preservado após falha HTTP (ex: `500` / timeout) |
| **CT-CERT-010** | Rotina das 00h00 | Com relógio controlado, as etiquetas mudam ao virar o dia civil em Brasília | Simulação temporal via relógio falso (`vi.useFakeTimers()`) | Dependência exclusiva de ação manual para atualizar o status | Instante `23:59:59` BRT (`02:59:59Z`) vs `00:00:00` BRT (`03:00:00Z`) alterando os dias restantes |

---

## Jornada 4: Barreira de Segurança e Acesso Cruzado (`CT-SEG-001` a `011`)
**Rastreabilidade:** US01, US03, RF01, RF03 e RNF06

| CT | Pedido / Requisição | Papel / Quem | Nível de Teste | Resposta HTTP API | Comportamento de Interface / Regra |
| :---: | :--- | :--- | :--- | :--- | :--- |
| **CT-SEG-001** | `GET /feed` | Sem sessão (Anônimo) | API & E2E | `401` | Redireciona ao login |
| **CT-SEG-002** | `GET /feed` | Token com assinatura inválida | API | `401` | Redireciona ao login |
| **CT-SEG-003** | `GET /feed` | Token expirado | API & E2E | `401` | Login com aviso de sessão expirada |
| **CT-SEG-004** | `GET /feed` | Token válido sem `orgId` ou sem `role` | API | `403` | Acesso Negado |
| **CT-SEG-005** | `GET /certidoes` | Analista | API & E2E | `403` | Página "Acesso Negado (403)"; menu sem a aba de documentação legal |
| **CT-SEG-006** | `PUT /certidoes/{id}/renovacao` | Analista | API | `403` | Nada é gravado |
| **CT-SEG-007** | `GET /certidoes/{id da empresa B}` | Decisor da Empresa A | API | `403` | Acesso Negado |
| **CT-SEG-008** | `PUT /certidoes/{id da empresa B}/renovacao` | Decisor da Empresa A | API | `403` | Certidão da empresa B permanece inalterada |
| **CT-SEG-009** | `GET /perfil?orgId=empresa-B` | Analista da Empresa A | API | `403` | Acesso Negado (tenant vem exclusivamente do token) |
| **CT-SEG-010** | Leitura e escrita direta no Firestore sem passar pela API | Cliente / SDK direto | Regras do Firestore | Negado pelas regras (`deny-all`) | Não aplicável |
| **CT-SEG-011** | Qualquer tentativa de acesso bloqueado | Utilizador malicioso / Geral | API | Bloqueio padrão | Corpo de erro sem dados do recurso nem dados pessoais; Log JSON contendo apenas `requestId`, `rota` e `status` |

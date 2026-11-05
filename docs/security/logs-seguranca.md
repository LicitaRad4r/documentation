# Padrão de Logs de Segurança e Conformidade (LGPD)

## 1. Visão Geral
Este documento define o padrão arquitetural de logs de segurança para requisições bloqueadas com status `401 (Não Autenticado)` e `403 (Acesso Negado)`, visando a conformidade com a LGPD e a observabilidade no Google Cloud Run via Cloud Logging.

## 2. Especificação do Formato de Log
Os logs de bloqueio geram estruturas JSON estritas contendo exclusivamente campos permitidos.

### Campos Permitidos
- `severity`: Nível do log adaptado para o Cloud Logging (`WARNING`).
- `time`: Timestamp Unix em milissegundos.
- `requestId`: Identificador único da requisição (`x-request-id`).
- `evento`: Identificador fixo com valor `bloqueio_seguranca`.
- `rota`: Template da rota normalizada (ex: `/v1/certidoes`), evitando vazamento de parâmetros dinâmicos ou IDs.
- `metodo`: Verbo HTTP utilizado (`GET`, `POST`, etc.).
- `status`: Código de status HTTP (`401` ou `403`).
- `codigo`: Código de erro de domínio (`NAO_AUTENTICADO`, `EMPRES_NAO_CONFIGURADA`, `ACESSO_NEGADO`).
- `msg`: Mensagem padrão fixa (`Acesso bloqueado`).

### Dados Proibidos (Política de Redação e LGPD)
É estritamente proibido registrar em log:
- Endereços de IP dos clientes.
- Endereços de e-mail, nomes ou dados cadastrais.
- Tokens de autenticação, senhas ou cabeçalhos HTTP brutos (`req.headers`, `req.query`, `req.body`).

## 3. Como Consultar no Cloud Logging
Para filtrar eventos de bloqueio de segurança no Console do Google Cloud, utilize a seguinte query no Logs Explorer:

```kql
resource.type="cloud_run_revision"
jsonPayload.evento="bloqueio_seguranca"
severity="WARNING"
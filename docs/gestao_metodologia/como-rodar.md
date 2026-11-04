# Como rodar o LicitaRadar localmente

**Responsável:** DevOps  
**Atualizado:** 04/11/2026  

## Pré-requisito

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) instalado e **aberto** (ícone ativo).

Não é necessário instalar Node.js, Firebase CLI nem rodar `npm install` na máquina para subir o ambiente. Auth e Firestore sobem pelos **emuladores** dentro do Compose.

## Comando

No repositório de código ([`LicitaRad4r/LicitaRadar`](https://github.com/LicitaRad4r/LicitaRadar)):

```bash
git clone https://github.com/LicitaRad4r/LicitaRadar.git
cd LicitaRadar
docker compose up --build
```

A **primeira** execução demora vários minutos (baixa imagens e emuladores). As próximas são mais rápidas.

| Serviço | URL |
| ------- | --- |
| Frontend | http://localhost:5173 |
| API | http://localhost:3333/health |
| Emulator UI (Firebase) | http://localhost:4000 |
| Auth emulator | localhost:9099 |
| Firestore emulator | localhost:8080 |

## O que não fazer

Não use o fluxo antigo:

- `docker compose up -d` só como “banco”
- `cd server && npm install && npm run dev`
- `cd client && npm install && npm run dev`
- instalar Firebase CLI / emuladores na máquina

Isso gera erro de dependências em muitos ambientes e **não** é o caminho suportado. O Compose já sobe `firebase` + `api` + `web`.

## Problemas comuns

| Sintoma | O que fazer |
| ------- | ----------- |
| `Cannot connect to the Docker daemon` | Abra o Docker Desktop e espere ficar pronto. |
| Porta em uso | Liberar `3333`, `5173`, `4000`, `8080`, `9099` (ou mudar no `.env`). |
| Build “travado” na primeira vez | Esperar 2–5 min; a imagem do Firebase baixa o emulador. |
| Estado estranho | `docker compose down -v` e depois `docker compose up --build`. |

## Lint / testes fora do Docker (opcional)

Só se for rodar o mesmo que o CI na máquina (precisa Node.js 22+):

```bash
cd client && npm ci && npm run lint && npm run build
cd ../server && npm ci && npm run lint && npm run build && npm run test:unit
```

Guia espelhado no README do repositório de código.

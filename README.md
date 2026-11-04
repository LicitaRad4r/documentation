# LicitaRadar

Plataforma de inteligência de dados e triagem de editais públicos (PNCP) para PMEs, focada em segurança documental, gestão de certidões e ranqueamento de oportunidades governamentais.

Projeto de Extensão da UnB (FCTE) - AI Lab Makers

**Professores:** Nilton Correia e Fabrício Ataides

---

## Stack Tecnológico

| Camada              | Tecnologia                                                                 |
| ------------------- | -------------------------------------------------------------------------- |
| **Frontend**        | React + Vite + TypeScript + Tailwind CSS                                   |
| **Backend**         | Node.js + TypeScript + Fastify + Zod                                       |
| **QA e Testes**     | Vitest (unitários/contrato) + Playwright (E2E)                             |
| **DevOps e Deploy** | GitHub Actions (CI) + Firebase Hosting (front) + Render (API, plano custo zero) |
| **Auth e dados**    | Firebase Auth + Firestore (plano Spark / emuladores locais)                |

> Até a Semana 4 o ambiente de desenvolvimento é **100% local** (Docker Compose + emuladores). Não use Cloud Run nem PostgreSQL para subir o projeto no dia a dia.

---

## Rodando localmente

O guia completo e atualizado está no repositório de código:
[`LicitaRad4r/LicitaRadar`](https://github.com/LicitaRad4r/LicitaRadar) (arquivo `README.md`).

**Resumo:**

1. Instale e **abra** o [Docker Desktop](https://www.docker.com/products/docker-desktop/).
2. Não precisa instalar Node.js nem Firebase CLI na máquina para subir o app.

```bash
git clone https://github.com/LicitaRad4r/LicitaRadar.git
cd LicitaRadar
docker compose up --build
```

A primeira execução demora alguns minutos. Depois acesse:

| Serviço     | URL                          |
| ----------- | ---------------------------- |
| Frontend    | http://localhost:5173        |
| API         | http://localhost:3333/health |
| Emulator UI | http://localhost:4000        |

**Não use** o fluxo antigo (`npm install` no `client`/`server` + Firebase CLI na máquina) para “só rodar”. Esse caminho é opcional e só para lint/testes fora do Docker.

## Documentação (este repositório)

Site: [GitHub Pages](https://licitarad4r.github.io/documentation/)

Para editar/visualizar localmente:

```bash
pip install -r requirements.txt
mkdocs serve
```

FinOps (prints de plano gratuito / sem faturamento): `docs/finops/`.

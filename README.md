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
| **QA e Testes**     | Vitest (Unitários/Regras de negócio) + Playwright (E2E/Interface)          |
| **DevOps e Deploy** | GitHub Actions (CI/CD) + Firebase Hosting (Front) + Google Cloud Run (API) |
| **Banco de Dados**  | PostgreSQL                                                                 |

---

## Rodando localmente

**Pré-requisitos:** Node.js, Docker, Firebase CLI e Emuladores do Firebase.

```bash
git clone https://github.com/LicitaRad4r/LicitaRadar.git
cd LicitaRadar

# Subir infraestrutura local (ex: Banco de dados via Docker)
docker compose up -d

# Backend (Fastify)
cd server 
npm install 
npm run dev

# Frontend (React + Vite)
cd ../client 
npm install 
npm run dev
```
## Documentação

Disponível em [GitHub Pages](https://licitarad4r.github.io/documentation/) ou localmente com:

```bash
pip install mkdocs-material mkdocs-awesome-pages-plugin
mkdocs serve
```
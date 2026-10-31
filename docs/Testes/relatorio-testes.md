# Primeiro relatório de testes (Checkpoint 2)
 
**Projeto:** LicitaRadar · **Responsável pela qualidade:** Kauan Tarchetti Peixoto · **Data da última atualização:** 28/10/2026
 
## 1. Resumo
| Item | Resultado |
| --- | --- |
| Testes de integração com emulador (deny-all do Firestore) | **21 de 21** passaram, executados localmente em 28/10/2026 |

## 2. O que foi testado
 
| Frente | Arquivos (`server/tests/`) | Testes | Casos do plano |
| --- | --- | --- | --- |
| Segurança das regras do Firestore (deny-all) | `integration/firestore-deny-all.test.ts` | 21 (emulador) | CT-SEG-010 |

**Regras do Firestore.** `firestore.rules` em deny-all e 21 testes no emulador: sem login, logado sem claim, decisor e analista da empresa A tentam ler, listar, criar, atualizar e apagar nas coleções `certidoes`, `editais`, `scores` e `usuarios`; tudo é negado e o dado da empresa B permanece intacto. O isolamento entre empresas **na API** é coberto por CT-SEG-007 a 009; as duas camadas se completam.

## 3. Como reproduzir
```bash
cd server
# integração (precisa do emulador do Firestore no ar, ex.: docker compose up -d firebase)
npm run test:int         # 21 testes do deny-all
```

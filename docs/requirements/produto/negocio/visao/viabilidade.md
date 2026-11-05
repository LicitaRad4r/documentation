# Viabilidade e Sucesso do MVP

## Análise de Viabilidade

A construção do LicitaRadar é sustentável e adequada ao ciclo atual de desenvolvimento, sustentando-se em quatro pilares principais:

*   **Viabilidade Técnica:** A arquitetura utiliza tecnologias modernas e de alto desempenho (React, Node.js, Fastify, Cloud Run). A integração de dados é viável através da API do PNCP. O uso do Firebase Emulator Suite permite simular o banco de dados localmente, mitigando riscos técnicos.
*   **Viabilidade de Prazo:** O ciclo restrito de aproximadamte 2 meses é gerido pela aplicação rigorosa da matriz MoSCoW, blindando o escopo para que apenas as jornadas essenciais sejam desenvolvidas até ao fim do ciclo.
*   **Viabilidade Financeira:** O risco é baixo. Os testes iniciais ocorrem 100% localmente (custo zero). Em produção, a arquitetura *serverless* otimizada mantém o custo total de nuvem sustentável e abaixo do teto estipulado.
*   **Viabilidade de Mercado:** Existe uma demanda latente das PMEs por soluções simples. Enquanto a concorrência foca em volume, o LicitaRadar resolve as dores diretas de forma proativa.

---

## Gestão de Custos (FinOps)

Para garantir que a infraestrutura não ultrapassa o limite orçamental, o projeto segue as seguintes estimativas e limites de segurança:

| Etapa | Custo Estimado |
| :--- | :--- |
| **Desenvolvimento local (Emuladores e Docker)** | US$ 0 |
| **Checkpoint 3 (Sistema na nuvem)** | Gasto acumulado ≤ US$ 12 |
| **Estimativa total de nuvem no ciclo** | US$ 10 a US$ 20 |
| **Teto máximo por equipa** | **US$ 50** |
| **Alertas de orçamento configurados (GCP)** | 50% (US$ 25), 80% (US$ 40) e 100% (US$ 50) |

---

## Critérios de Sucesso do MVP

O MVP será considerado bem-sucedido se, no momento da demonstração oficial, cumprir os seguintes requisitos funcionais e técnicos:

1.  **Personalização:** Dois perfis de empresa diferentes receberem feeds distintos e coerentes com a sua configuração.
2.  **Precisão:** A relevância dos dez primeiros resultados for comprovadamente aderente a cada perfil.
3.  **Proatividade:** Certidões vencidas ou a vencer gerarem alertas visuais nos dados de teste.
4.  **FinOps:** O custo da nuvem permanecer estritamente abaixo do teto de US$ 50.
5.  **Qualidade:** As metas técnicas forem atingidas (cobertura de testes de domínio ≥ 80%, zero vulnerabilidades críticas e Lighthouse ≥ 90).

---

## Conformidade com a LGPD

Para garantir a total adequação à Lei Geral de Proteção de Dados (LGPD) no contexto do nosso SaaS B2B, o LicitaRadar adota uma política estrita de minimização e retenção:

*   **Quais dados guardamos:** Apenas os estritamente necessários para a operação das contas corporativas (Nome, Cargo e E-mail corporativo do Decisor e da Analista).
*   **Porquê guardamos:** Utilizados exclusivamente para autenticação, gestão de acessos baseada em hierarquia e envio de alertas operacionais.
*   **Durante quanto tempo:** Os dados são mantidos apenas enquanto a subscrição da PME estiver ativa. Em caso de cancelamento, os dados da empresa e dos utilizadores são eliminados de forma segura num prazo de 30 dias.
# Equinology / VetEquus — Documentação

Fonte única da verdade da documentação do ecossistema. Substitui os `.md`
espalhados pelos repositórios de código (preservados em [`archive/`](archive/)).

> **Regra de ouro:** documentação derivável do código (rotas, schema) **aponta
> para a fonte**, não a copia — cópia gera divergência. Manuais de cliente e
> decisões arquiteturais (ADRs) ficam aqui porque não vivem no código.

## Sistemas

| Sistema | Repo | Stack | Papel | Doc |
|---|---|---|---|---|
| API | `vetequus-api` | NestJS + Prisma + Postgres | Backbone: regra de negócio, auth, pagamentos | [systems/api.md](systems/api.md) |
| Web profissional | `equinology-web-v2` | Next.js 16 (App Router) | App do veterinário/gestor | [systems/web.md](systems/web.md) |
| App cliente | `equinology-app-v2` | Expo / React Native | App do tutor (dono do cavalo) | [systems/app-cliente.md](systems/app-cliente.md) |
| Painel admin | `equinology-adm` | Next.js 16 | Super-admin (gestão de tenants/planos) | [systems/admin.md](systems/admin.md) |

## Índice

- **Visão geral**
  - [Arquitetura](overview/architecture.md)
  - [Modelo de dados](overview/data-model.md)
  - [Personas e fluxos críticos](overview/personas-e-fluxos.md)
- **Guias**
  - Desenvolvedor: [Setup local + variáveis de ambiente](guides/developer/setup.md) · [Convenções](guides/developer/convencoes.md)
  - Operações: [Deploy](guides/operations/deploy.md) · [Checklist de QA](guides/operations/qa-checklist.md)
  - Cliente: [Manual do tutor (app)](guides/client/tutor-app.md) · [Manual do veterinário (web)](guides/client/veterinario-web.md)
- **Decisões (ADRs)**
  - [0001 — Ponte Fatura → Caixa](decisions/0001-fatura-caixa.md)
  - [0002 — IA via OpenRouter (server-side)](decisions/0002-ia-openrouter.md)
  - [0003 — Endurecimento do webhook Asaas](decisions/0003-webhook-asaas.md)
- **Histórico:** [`archive/`](archive/) — docs originais preservados, incluindo a [auditoria 2026-05-28](archive/AUDITORIA-VETEQUUS-2026-05-28.md).

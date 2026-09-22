# Equinology — documentação técnica

O Equinology reúne uma API, uma aplicação web para clínicas, um app para tutores, um painel administrativo e um site institucional.

## Comece aqui

1. [Visão do sistema](overview/o-que-e-o-sistema.md) e [arquitetura](overview/architecture.md).
2. [Modelo de dados](overview/data-model.md) e [fluxos de uso](overview/personas-e-fluxos.md).
3. [Instalação e configuração](guides/developer/setup.md).
4. [Mapa de manutenção](guides/developer/manutencao.md): onde alterar cada funcionalidade.
5. [Publicação](guides/operations/deploy.md).

## Aplicações

| Aplicação | Repositório | Guia |
|---|---|---|
| API — NestJS, Prisma e PostgreSQL | [vetequus-api](https://github.com/ExecutivosDigital/vetequus-api) | [API](systems/api.md) |
| Web profissional — Next.js | [equinology-web-v2](https://github.com/EquinologySistemas/equinology-web-v2) | [Web](systems/web.md) |
| App do tutor — Expo / React Native | [equinology-app-v2](https://github.com/EquinologySistemas/equinology-app-v2) | [App](systems/app-cliente.md) |
| Painel administrativo — Next.js | [equinology-adm-v2](https://github.com/EquinologySistemas/equinology-adm-v2) | [Painel](systems/admin.md) |
| Site institucional — Next.js | [equinology-institutional](https://github.com/EquinologySistemas/equinology-institutional) | [Institucional](systems/institucional.md) |

## Referências técnicas

- [Convenções de desenvolvimento](guides/developer/convencoes.md) e [validação](guides/operations/qa-checklist.md).
- [Integrações](guides/onboarding/README.md): pagamentos, e-mail, IA e publicação do app.
- Funcionamento de [fatura e caixa](decisions/0001-fatura-caixa.md), [IA](decisions/0002-ia-openrouter.md), [webhook Asaas](decisions/0003-webhook-asaas.md) e [operação offline](decisions/0004-offline.md).
- Fluxos das interfaces: [tutor](guides/client/tutor-app.md) e [veterinário](guides/client/veterinario-web.md).

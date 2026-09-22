# API — vetequus-api

**Stack:** NestJS 10, TypeScript, Prisma 6 e PostgreSQL.

## Entrada e estrutura

`src/infra/main.ts` inicia a aplicação na porta `3333` ou em `PORT`. Swagger fica em `/api`; Scalar, em `/reference`.

| Diretório | Responsabilidade |
|---|---|
| `src/core/` | Tipos base, entidades, erros e `Either` |
| `src/domain/enterprise/entities/` | Entidades de negócio |
| `src/domain/application/services/` | Casos de uso e regras |
| `src/domain/application/repositories/` | Contratos de persistência |
| `src/infra/http/controllers/` | Rotas, DTOs e validação de entrada |
| `src/infra/http/presenters/` | Formatos de resposta |
| `src/infra/shared/database/prisma/` | Client, mapeadores e repositórios |
| `src/infra/shared/` | Auth, banco de pagamentos, storage, e-mail e ambiente |

## Contratos HTTP

Consulte os controllers e DTOs para parâmetros, paginação e respostas. As famílias incluem usuários/empresas, clientes, animais, propriedades, atendimentos, fichas clínicas, saúde, financeiro, assinaturas, estoque, CRM, notas, lembretes, anúncios, tutoriais e administração.

O portal do tutor usa rotas `client-portal`, com acesso ao conteúdo compartilhado e às notas do próprio proprietário.

## Autenticação e validação

- O `AuthGuard` global valida o Bearer JWT e consulta a situação da conta por `session-validity.ts`.
- O módulo de autenticação configura duração de `90d` para os tokens. Os endpoints de token reemitem o acesso usando o fluxo definido em cada controller.
- Guards de perfil e verificações nos serviços delimitam operações de profissional, tutor e administrador.
- `main.ts` registra transformação/validação de DTOs, validação de parâmetros UUID, tradução de mensagens e filtro global de exceções.
- Endpoints de autenticação e recuperação usam `ThrottlerGuard` com limites declarados no próprio controller.
- `IdempotencyInterceptor` trata escritas autenticadas que trazem `Idempotency-Key`; veja [offline](../decisions/0004-offline.md).

## Acesso do tutor

O cadastro pela clínica usa nome e telefone. O fluxo de primeiro acesso consulta `POST /client/first-access/lookup` com telefone e, quando aplicável, define e-mail e senha por `POST /client/first-access`. Uma conta com e-mail recebe orientação com e-mail mascarado. `POST /client/forgot-email` auxilia a identificação da conta.

Os DTOs, o serviço e o repositório definem normalização, unicidade e política de senha. Consulte `src/domain/application/services/client/services/client.service.ts`.

## Exclusão da própria conta

`DELETE /client/me` exige o token do tutor. A operação transacional remove dados pessoais de cadastro, credenciais, códigos de recuperação, notas privadas do proprietário, cartões pessoais e registros de idempotência relacionados. A referência passa a se chamar `Conta excluída` e suas sessões deixam de ser válidas.

Históricos clínicos, animais, faturas, movimentações e vínculos necessários à clínica são preservados. A operação é local à API; registros financeiros externos e backups pertencem aos respectivos processos de operação. O arquivamento administrativo de cliente tem seu próprio contrato.

Referências: `prismaClient.repository.ts`, `lock-active-client.ts`, `session-validity.ts` e `test/account-deletion.integration.ts`.

## Integrações

| Integração | Código |
|---|---|
| Asaas: assinaturas, cobranças e repasses | `src/infra/shared/bank/asaas.ts` |
| Faturas e recebimentos | `src/domain/application/services/invoice/invoice.service.ts` |
| Webhook | `src/infra/http/controllers/signature/companySignature.controller.ts` |
| S3 / Cloudflare R2 | `src/infra/shared/storage/` |
| E-mail | `src/infra/shared/email/`, templates em `src/domain/application/shared/email/` |
| Agendamentos | Schedulers de assinatura, inatividade e limpeza de idempotência |

O e-mail usa `MailModule`: driver `log` ou `smtp`, este último implementado com Nodemailer na classe `BrevoMailProvider` e configurável por variáveis. O nome da classe não determina o provedor SMTP escolhido.

Veja [setup](../guides/developer/setup.md), [modelo](../overview/data-model.md), [manutenção](../guides/developer/manutencao.md) e [deploy](../guides/operations/deploy.md).

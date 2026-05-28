# Sistema: vetequus-api

**Stack:** NestJS + Prisma (Postgres). Porta 3333. Swagger em `/api`, Scalar em `/reference`.
**Repo:** `vetequus-api`. **Entrypoint:** `src/infra/main.ts`.

## Estrutura

```
src/
  core/    # Either, Entity/ValueObject base, erros
  domain/  # application/ (repositórios = interfaces, serviços, ports) + enterprise/entities
  infra/   # http/ (controllers, modules, presenters, dtos), shared/ (auth, bank, storage, email, env, database)
  utils/
```

## Superfície de rotas

> A lista canônica vive no código (controllers) e no Swagger `/api`. Não a
> duplicamos aqui. Snapshot histórico em [archive/from-api/API_ROUTES.md](../archive/from-api/API_ROUTES.md).

Grupos: `user`/`company`/`password-code`; `client/*`; `admin/*`; `animal` +
clínicos; `appointment(-animal)`; financeiro (`transaction`/`payment`/`invoice`/
`client-invoice`/`bank-account`/`credit-card`); `signature(-plan)`; estoque
(`product`/`stock-*`/`field-stock`); `board`/`lead`; `note`/`reminder`/`tag`/
`stud-farm`/`sanitary-protocol`; `file`; `ads`/`coupons`.

## Autenticação

- `AuthGuard` global (`APP_GUARD`) valida Bearer JWT e injeta `userId`/`companyId`/
  `tokenType`. `@IsPublic()` libera rotas específicas (login, register, reset, webhook).
- JWT HS256, `JWT_SECRET`, expiração **90 dias** (sem refresh-token real; cada ator
  tem endpoint que reemite o access).
- Guards adicionais: `AdminAuthGuard`, `AdminSuperAdminGuard`, `RoleGuard`.

## Integrações

- **Asaas** (`infra/shared/bank/asaas.ts`): assinaturas e pagamento de faturas.
- **Storage** R2/S3 (`POST /file`, **autenticado**).
- **Email**: nodemailer/SMTP.

## Pontos de atenção (auditoria 2026-05-28)

Já corrigidos: backdoor `/scripts` removido; `POST /file` agora exige auth;
webhook Asaas autenticado por token + idempotência de cartão; `Invoice`
`:id` escopado por `companyId`. Pendentes: rate-limit, `ValidationPipe`
whitelist, helmet, health check, validação de assinatura por `expirationDate`,
filtro de planos ativos. Ver [auditoria completa](../archive/AUDITORIA-VETEQUUS-2026-05-28.md).

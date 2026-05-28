# Convenções

## Commits
Conventional Commits (`commitlint.config.ts` na API e no adm): `feat`, `fix`,
`chore`, `refactor`, etc. Use "add" para feature nova, "update" para melhoria,
"fix" para correção.

## API (NestJS)
- Camadas: controller (HTTP) → service (`domain/application`) → repository
  (interface em `domain`, impl Prisma em `infra`). Serviços retornam `Either`.
- `companyId` **sempre** via `@CurrentCompanyId()` (JWT), nunca do body.
- Rotas públicas: `@IsPublic()` (use com parcimônia; webhooks devem validar token).
- DTOs com `class-validator`; presenters convertem entidade → HTTP.

## Frontends
- Web/adm: máscaras e componentes — ver [MASKS_GUIDE](../../archive/from-web/MASKS_GUIDE.md) e [UI_COMPONENTS](../../archive/from-web/UI_COMPONENTS.md).
- Uploads: sempre anexar o Bearer token (o endpoint `/file` exige auth).
- Salvar `fullUrl` (URL absoluta) retornado por `/file`, não `url` relativo.

## Banco / Prisma
- Mudou schema? Gere migration com `prisma migrate` — **não** edite o banco com
  SQL manual (há histórico de drift; ver [modelo de dados](../../overview/data-model.md)).

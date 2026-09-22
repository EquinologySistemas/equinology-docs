# Convenções de desenvolvimento

## Estrutura e contratos

- Na API, siga controller/DTO → serviço → interface de repositório → implementação Prisma; presenters definem a resposta HTTP.
- Preserve o escopo do usuário: clínica pelo JWT, tutor pelos vínculos de proprietário e painel pelos guards administrativos.
- Declare o acesso público com `@IsPublic()` quando fizer parte do contrato. Webhooks validam sua própria credencial.
- Reutilize os validadores e a tradução de mensagens existentes.
- No frontend, use as funções de API, os mapeadores e os componentes de formulário do respectivo repositório.
- Para uploads, envie o Bearer token e use a URL absoluta retornada pelo endpoint. Registros que aceitam anexos estruturados usam a coleção `attachments`.
- Reutilize os utilitários de datas; diferencie data civil (`YYYY-MM-DD`) de instante com fuso.

## Banco

O schema e as migrations pertencem ao repositório da API. Em ambiente local:

```bash
yarn prisma migrate status
yarn prisma migrate dev --name descricao_da_alteracao
yarn prisma generate
```

Antes dos comandos, confirme `DATABASE_URL` e o host exibido pelo Prisma. Gere migrations em banco local e versione o SQL junto com `schema.prisma`. Revise as alterações antes da publicação.

Na publicação, `yarn prisma migrate deploy` aplica o histórico versionado ao banco escolhido. Registre a versão e o backup conforme o [guia de deploy](../operations/deploy.md). Comandos de reset ou sincronização direta de schema são destinados a bases locais descartáveis.

## Offline

O cliente web usa a camada de `lib/offline/`. Ao modificar uma escrita clínica, confira a resposta online e a resposta local, os vínculos temporários e a ordem da fila. A API recebe a chave de idempotência usada nas tentativas de sincronização. Veja [ADR 0004](../../decisions/0004-offline.md).

## Commits e documentação

Siga Conventional Commits, conforme os arquivos de commitlint existentes: `feat`, `fix`, `docs`, `chore`, `refactor` e demais tipos configurados.

Mudanças em fluxo, ambiente, publicação ou integração devem atualizar o respectivo guia em `equinology-docs`.

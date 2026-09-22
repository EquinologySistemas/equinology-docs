# Publicação e operação

## Referências da API

| Item | Referência operacional |
|---|---|
| URL usada pelos clientes | `https://api.equinology.com.br` |
| Servidor | AWS EC2, `3.21.4.120`, usuário `ubuntu` |
| Diretório no servidor | `/home/ubuntu/App` |
| Processo | PM2 `api` |
| Entrada compilada | `dist/src/infra/main.js` |
| Proxy | HTTPS no nginx, aplicação em `localhost:3333` |
| Configuração de execução | `.env` do ambiente, conforme `src/infra/shared/env/env.ts` |

## Preparar uma versão da API

1. Selecione a versão a publicar.
2. Instale as dependências com `yarn install --frozen-lockfile`.
3. Com o ambiente de desenvolvimento selecionado, gere o Client e compile:

```bash
yarn prisma generate
yarn tsc --noEmit
yarn build
```

4. Execute as verificações aplicáveis descritas em [validação](qa-checklist.md).
5. Prepare o artefato com `dist/`, `prisma/`, `package.json` e `yarn.lock`. Guarde a versão anterior para recuperação.
6. Confirme o backup do banco e os arquivos de configuração do ambiente de destino.
7. Revise as migrations da versão antes de publicá-la.

O arquivo `example.deploy.ps1` automatiza build, envio por SSH, aplicação de migrations no servidor e reinício do PM2. Recebe `-ServerAddress`, `-SshKeyPath` e, opcionalmente, `-RemoteUser` (padrão `ubuntu`). Configure o `.env` do destino e mantenha a chave PEM fora do repositório. A sequência abaixo detalha as etapas para execução manual.

## Aplicar no ambiente de destino

No ambiente de ferramentas da release, que recebeu as dependências completas na preparação, selecione explicitamente a URL do banco de destino. Confira o host apresentado pelo Prisma. O diretório deve conter o schema e as migrations do mesmo commit do artefato.

```bash
yarn prisma migrate status
yarn prisma migrate deploy
yarn prisma migrate status
```

Esses comandos exigem o Prisma CLI da versão instalada no projeto. Aplique as migrations versionadas antes de colocar em execução código que depende delas. Geração de migrations e resets pertencem ao ambiente local.

No diretório de execução, instale as dependências de runtime e gere o Prisma Client de acordo com o schema da aplicação:

```bash
yarn install --production=true --frozen-lockfile
npx --yes prisma@6.5.0 generate
pm2 restart api --update-env
pm2 status
pm2 logs api --lines 100 --nostream
```

Em uma instalação inicial, o processo pode ser criado com:

```bash
pm2 start dist/src/infra/main.js --name api
pm2 save
```

Configure as variáveis de ambiente e o proxy HTTPS do nginx para encaminhar as requisições à porta da API.

## Conferência após publicar

- Abrir `/api` e `/reference` na API.
- Conferir inicialização e logs do processo.
- Executar login com as contas do ambiente.
- Conferir upload e leitura de um arquivo de teste.
- Conferir no Asaas a URL `https://api.equinology.com.br/signature/webhook` e o token configurado, conforme o [guia da integração](../onboarding/asaas.md).

## Web, painel e institucional

Os três são projetos Next.js independentes:

```bash
yarn install --frozen-lockfile
yarn build
yarn start
```

Na hospedagem, configure o repositório correto, branch e variáveis conforme [setup](../developer/setup.md). A web usa `master`; painel e institucional usam `main`.

As variáveis `NEXT_PUBLIC_*` entram no build. `OPENROUTER_API_KEY` pertence ao ambiente de servidor da web. Configure os domínios de cada aplicação na hospedagem.

Na web, a conferência inclui o service worker, os dados locais e a tela `/sync`. O fluxo offline está descrito no [ADR 0004](../../decisions/0004-offline.md).

## App Expo/EAS

A configuração de referência está em `app.json` e `eas.json`:

- Expo SDK 54; versão do app definida em `expo.version`.
- Projeto: [@equinologys-team/equinology-app-v2](https://expo.dev/accounts/equinologys-team/projects/equinology-app-v2).
- `expo.owner`: `equinologys-team`; `expo.extra.eas.projectId`: `304c5e5a-a548-4593-9270-8abd5a010912`.
- iOS `com.equinollogy.app`; Android `com.equinology.appv2`.
- `appVersionSource: remote`; `production.autoIncrement: true`.
- Perfis `development` (development client/interno), `preview` (interno) e `production`.
- Perfil `submit.production`: os dados de envio são resolvidos com a configuração/credenciais do projeto.

Antes do build, configure `EXPO_PUBLIC_API_URL` e as credenciais das lojas no projeto EAS. No repositório do app:

```bash
npx eas-cli@latest whoami
npx eas-cli@latest project:info
npx eas-cli@latest build --platform ios --profile production
npx eas-cli@latest build --platform android --profile production
```

Para enviar um build específico, use seu ID:

```bash
npx eas-cli@latest submit --platform ios --id <ID-DO-BUILD-IOS>
npx eas-cli@latest submit --platform android --id <ID-DO-BUILD-ANDROID>
```

Após o envio, configure a versão e solicite sua revisão/publicação no App Store Connect ou no Google Play Console.

## Recuperação de versão

Mantenha o artefato anterior e o registro das configurações da publicação. Para recuperar código, restaure o artefato compatível com o banco atual e reinicie o processo. Migrações e dados exigem planejamento próprio: a restauração de um binário não desfaz alterações no banco.

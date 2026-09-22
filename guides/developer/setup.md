# Setup local

Execute os comandos no repositório da aplicação indicada.

## Ferramentas e clones

Use Node.js 22.x, Yarn Classic 1.22.x, Git e PostgreSQL local (ou Docker). Cada repositório possui seu próprio `yarn.lock`. Mantenha esse lockfile durante a instalação.

```bash
git clone https://github.com/EquinologySistemas/equinology-docs.git
git clone https://github.com/ExecutivosDigital/vetequus-api.git
git clone https://github.com/EquinologySistemas/equinology-web-v2.git
git clone https://github.com/EquinologySistemas/equinology-app-v2.git
git clone https://github.com/EquinologySistemas/equinology-adm-v2.git
git clone https://github.com/EquinologySistemas/equinology-institutional.git
```

A web usa a branch `master`; os demais repositórios usam `main`.

## API e banco local

No repositório da API, instale as dependências e inicie o PostgreSQL:

```bash
yarn install --frozen-lockfile
docker compose up -d postgres
```

O compose versionado expõe PostgreSQL em `localhost:5432`, usuário `postgres`, senha de desenvolvimento `docker`, banco `vetequus-api`. Confira se essa porta está livre antes de iniciar o serviço.

Crie um arquivo `.env` local com esta base. Os valores abaixo são destinados a desenvolvimento:

```dotenv
DATABASE_URL="postgresql://postgres:docker@localhost:5432/vetequus-api?schema=public"
JWT_SECRET="equinology-local-development"
PORT=3333
MAIL_DRIVER="log"
SMTP_USER="dev@example.test"
SMTP_KEY=""
SMTP_HOST=""
SMTP_PORT="587"
SMTP_FROM=""
AWS_ACCESS_KEY_ID=""
AWS_SECRET_ACCESS_KEY_ID=""
AWS_BUCKET_NAME=""
CLOUDFLARE_ACCOUNT_ID=""
CLOUDFLARE_URL=""
ASAAS_URL=""
ASAAS_KEY=""
ASAAS_WEBHOOK_TOKEN="equinology-local-webhook"
```

O contrato de ambiente é `src/infra/shared/env/env.ts`. Todas as chaves da base acima, exceto `PORT` e as opções de SMTP/driver, são exigidas pelo schema de ambiente. Upload e pagamentos usam as credenciais do ambiente de integração escolhido. `MAIL_DRIVER=log` registra o e-mail no console local; `smtp` envia pelo provedor configurado.

Defina também a URL explicitamente no terminal de trabalho, para que os comandos usem o banco local:

**macOS/Linux:**

```bash
export DATABASE_URL='postgresql://postgres:docker@localhost:5432/vetequus-api?schema=public'
```

**PowerShell:**

```powershell
$env:DATABASE_URL = 'postgresql://postgres:docker@localhost:5432/vetequus-api?schema=public'
```

Confira o host exibido pelo Prisma antes de aplicar o histórico:

```bash
yarn prisma migrate status
yarn prisma migrate dev
yarn prisma generate
yarn start:dev
```

`migrate status` pode indicar migrations a aplicar em um banco novo. Neste roteiro, o destino de `migrate dev` deve ser o PostgreSQL local. Se o Prisma solicitar decisões de reset ou de reconciliação, confira o banco e o histórico antes de prosseguir; a preparação local deve usar uma base descartável.

A API escuta em `http://localhost:3333`, com Swagger em `/api` e Scalar em `/reference`.

### Cadastros para desenvolvimento

Os scripts em `prisma/seed-*.ts` são executados explicitamente. Leia o script e configure as variáveis antes de usar.

- `seed-plan-admin.ts`: plano e usuário do painel. Recebe `ADMIN_NAME`, `ADMIN_EMAIL`, `ADMIN_PASSWORD`, `PLAN_NAME`, `PLAN_DESCRIPTION`, `PLAN_MONTHLY_PRICE` e `PLAN_YEARLY_DISCOUNT`.
- `seed-test-account.ts`: conta de teste da clínica e assinatura de cortesia. Recebe as opções `TEST_*` e `COURTESY_*` descritas no arquivo.
- Os demais seeds atendem conjuntos específicos de demonstração.

Com a URL local e as variáveis do seed definidas no terminal:

```bash
yarn ts-node prisma/seed-plan-admin.ts
yarn ts-node prisma/seed-test-account.ts
```

Use identidades exclusivas do ambiente de desenvolvimento. O primeiro script imprime os dados de acesso criados no terminal.

## Web profissional

Crie `.env.local` na raiz do repositório:

```dotenv
NEXT_PUBLIC_API_URL=http://localhost:3333
NEXT_PUBLIC_USER_TOKEN=equinology_token
NEXT_PUBLIC_APP_URL=http://localhost:3000
OPENROUTER_API_KEY=
```

```bash
yarn install --frozen-lockfile
yarn dev -p 3000
```

`OPENROUTER_API_KEY` é usada pelas rotas de IA no servidor Next.js. Configure-a para exercitar chat/transcrição.

| Opção adicional | Uso |
|---|---|
| `NEXT_PUBLIC_ENABLE_SW=true` | Ativa service worker também no desenvolvimento |
| `NEXT_PUBLIC_OFFLINE_DISABLED=true` | Executa o cliente sem a camada offline |
| `NEXT_PUBLIC_BUILD_TIME` | Identificação de build usada pela configuração de publicação |

Para validar navegação offline com o comportamento de produção, use `yarn build` e `yarn start -p 3000`. Veja [offline](../../decisions/0004-offline.md).

## Painel administrativo

Crie `.env.local` na raiz do repositório:

```dotenv
NEXT_PUBLIC_API_URL=http://localhost:3333
NEXT_PUBLIC_USER_TOKEN=equinologyAdminToken
```

```bash
yarn install --frozen-lockfile
yarn dev -p 3001
```

Entre com o usuário `AdminUser` do ambiente. Ele é distinto do usuário profissional da clínica. Usar nomes diferentes para os cookies da web e do painel permite abrir ambos em `localhost`.

## Site institucional

Crie `.env.local` na raiz do repositório:

```dotenv
NEXT_PUBLIC_API_URL=http://localhost:3333
NEXT_PUBLIC_APP_URL=http://localhost:3000
SPONSORS_API_URL=http://localhost:3333/ads/sponsors
TUTORIALS_API_URL=http://localhost:3333/tutorials
```

```bash
yarn install --frozen-lockfile
yarn dev -p 3002
```

`src/lib/config.ts` centraliza as URLs. Patrocinadores e tutoriais também aceitam as variantes `NEXT_PUBLIC_SPONSORS_API_URL` e `NEXT_PUBLIC_TUTORIALS_API_URL`. As variáveis sem prefixo têm precedência.

## App do tutor

No `.env` do app, configure `EXPO_PUBLIC_API_URL` para o endereço que o dispositivo consegue acessar:

| Execução | Exemplo da URL da API |
|---|---|
| Simulador iOS no mesmo Mac | `http://localhost:3333` |
| Emulador Android padrão | `http://10.0.2.2:3333` |
| Celular físico na mesma rede | `http://IP-LOCAL-DO-COMPUTADOR:3333` |

```bash
yarn install --frozen-lockfile
yarn start
```

Os scripts `yarn android`, `yarn ios` e `yarn web` iniciam o Expo direcionado à plataforma. Para usar o simulador iOS, prepare Xcode no Mac. Após mudar a URL, reinicie o Metro; `npx expo start --clear` limpa seu cache.

O app usa `Client`, com primeiro acesso por telefone ou login por e-mail. A referência da API no build é definida antes da geração do binário. Para builds e publicação, consulte [build e publicação](../operations/deploy.md#app-expoeas).

## Conferência inicial

- API: abrir `http://localhost:3333/api`.
- Web: abrir `http://localhost:3000` e entrar como profissional.
- Painel: abrir `http://localhost:3001/login` e entrar como administrador.
- Institucional: abrir `http://localhost:3002`.
- App: verificar a API configurada no dispositivo e abrir a tela de acesso.

Os comandos de verificação e os fluxos de uso estão no [guia de validação](../operations/qa-checklist.md).

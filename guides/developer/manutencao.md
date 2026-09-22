# Mapa de manutenção

Use este mapa para localizar o código responsável pelo comportamento que deseja alterar. Os caminhos são relativos à raiz do repositório indicado.

## Por assunto

| Assunto | Aplicações e arquivos de entrada |
|---|---|
| Cadastro, login e primeiro acesso do tutor | App `app/(auth)/login.tsx`, `forgot-password.tsx`, `lib/api-routes.ts`; API `src/infra/http/controllers/client/client.controller.ts` e `src/domain/application/services/client/services/client.service.ts` |
| Sessão e autorização | API `src/infra/shared/auth/`; web `lib/auth.ts`, `lib/auth-cookies.ts`, `middleware.ts`; app `contexts/SessionContext.tsx`; painel `src/lib/auth-cookies.ts`, `src/middleware.ts` |
| Clientes, animais e propriedades | Web `app/(dashboard)/clients-equines/`; API controllers e serviços de `client`, `animal` e `stud-farm` |
| Atendimentos e fichas | Web `app/(dashboard)/services/`, `services/boardRecordService.ts`; API `Appointment` → `AppointmentAnimal` e controllers clínicos em `src/infra/http/controllers/animal/` |
| Anexos | API `src/infra/shared/storage/`, model `Attachment`; web `lib/upload.ts`; app `contexts/ApiContext.tsx` |
| Conteúdo ao tutor | API `src/infra/http/controllers/client/clientPortal.controller.ts` e `src/infra/http/controllers/animal/ownerNote.controller.ts`; app `app/(animal)/vet/`, `app/(animal)/notes.tsx`, `lib/api-mappers.ts` |
| Faturas e caixa | API `src/domain/application/services/invoice/invoice.service.ts`; web `app/(dashboard)/financial/`; app `components/sheets/InvoicePaymentSheet.tsx` |
| Assinaturas, cupons e repasses | API `src/domain/application/services/signature/`, `src/infra/shared/bank/asaas.ts`; web `app/(auth)/checkout/`, `app/(dashboard)/subscription/`; painel `src/app/(private)/subscriptions/` |
| Operação offline | Web `lib/offline/`, `context/OfflineContext.tsx`, `app/(dashboard)/sync/page.tsx`, `public/sw.js`; API `src/infra/shared/interceptors/` |
| IA e transcrição | Web `app/api/chat/route.ts`, `app/api/audio/`, `lib/audio-transcribe.ts` |
| Tutoriais | Painel `src/app/(private)/tutorials/`; API `src/infra/http/controllers/admin/adminTutorials.controller.ts` e `publicTutorials.controller.ts`; institucional `src/app/tutoriais/`, `src/lib/tutorials.ts` |
| Patrocinadores e anúncios | Painel `src/app/(private)/ads/`; API controllers `ads`; institucional `src/lib/sponsors.ts` |
| Páginas informativas | Institucional `src/lib/legal.ts`, `src/app/termos/`, `src/app/privacidade/`, `src/app/excluir-conta/` |
| Exclusão da própria conta | API `client.controller.ts`, `client.service.ts`, `src/infra/shared/database/prisma/repositories/prismaClient.repository.ts`; app `app/(tabs)/profile.tsx` |
| Modelo e migrations | API `prisma/schema.prisma`, `prisma/migrations/` |

## Sequência para uma alteração

1. Leia o guia do sistema em [systems](../../README.md) e localize a tela, a chamada e o controller.
2. Confira o DTO de entrada e o presenter/retorno. Alguns endpoints devolvem envelopes como `{ animal: ... }` ou `{ client: ... }`.
3. Acompanhe o serviço até o repositório, incluindo escopo de empresa, vínculo do tutor e regras de situação do registro.
4. Para alterar persistência, siga o fluxo de migrations em [convenções](convencoes.md).
5. Confira os consumidores da mesma API: web, app, admin e institucional.
6. Nas rotas clínicas usadas offline, revise também reconhecimento de rotas, resposta local, IDs temporários e sincronização.
7. Execute as verificações correspondentes e atualize o guia afetado.

## Encontrar contratos no código

```bash
rg -n "@Controller|@Get|@Post|@Put|@Patch|@Delete" src/infra/http/controllers
rg -n "model |enum " prisma/schema.prisma
rg -n "firstAccess|ClientPortal|Invoice" lib/api-routes.ts
```

Os dois primeiros comandos são executados na API; o último, no app. Para a lista de rotas exposta em um ambiente iniciado, use Swagger `/api`.

## Dados e exclusão de conta

O arquivamento pela clínica e a exclusão solicitada pelo tutor são operações com contratos distintos. O comportamento atual de `DELETE /client/me` remove dados de acesso e conteúdo pessoal associado em uma transação, preservando referências clínicas e financeiras sob a identificação `Conta excluída`.

Ao trabalhar nessa área, leia o serviço, o repositório, `session-validity.ts`, `lock-active-client.ts` e o teste `test/account-deletion.integration.ts` da API. A preservação de documentos clínicos, registros financeiros e serviços externos faz parte do escopo da operação, descrito em [API](../../systems/api.md).

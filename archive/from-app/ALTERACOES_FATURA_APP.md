# Fatura pagável no app — 18/05/2026

## Objetivo

Permitir que **faturas** criadas no painel web (botão "Criar Fatura" → `POST /invoice`) apareçam **misturadas** com movimentações na lista do app cliente, e possam ser **pagas via PIX ou cartão** pelo mesmo `InvoicePaymentSheet` que já paga movimentações.

**Restrição:** sem quebrar nada do que já existe — `Payment`, `Transaction`, `Invoice` continuam intactos no schema; web continua igual; movimentação continua pagável como sempre.

---

## ⚙️ Migration necessária (rodar antes de subir o back)

```sh
cd vetequus-api
npx prisma migrate dev --name invoice_bank_payment_id
# Em prod:
# npx prisma migrate deploy
```

Adiciona **1 coluna opcional** em `invoices`:
```sql
ALTER TABLE "invoices" ADD COLUMN "bankPaymentId" TEXT;
```

Sem perda de dados. Faturas existentes ficam com `bankPaymentId: null` (correto — não foram pagas pelo app, então não têm id do Asaas).

---

## Arquitetura: por que esta abordagem

**Decisão:** não unificar Invoice e Payment na mesma tabela. Cada um continua com seu fluxo. O que muda é que o app passa a entender as duas como "coisa pagável" e o backend ganha endpoints de pagamento gêmeos.

**Por quê:**
- `Transaction.paymentId` é `NOT NULL` no schema atual e está referenciado em muitos lugares. Mudar pra opcional + adicionar `invoiceId` ou criar uma "transaction sintética" pra cada invoice seria mudança grande e arriscada.
- A web já trata Invoice como entidade fiscal separada (com `number`, `dueDate`, `status: PENDING|PAID|CANCELED`). Mexer nisso quebraria o painel.
- A solução escolhida: **endpoints simétricos** — `POST /transaction/pix/:id` (já existe) ↔ `POST /invoice/:id/pay/pix` (novo). Internamente os dois fazem a mesma chamada Asaas; o que muda é onde gravam o `bankPaymentId` (Transaction ou Invoice).

---

## Backend — arquivos alterados

### `prisma/schema.prisma` ([linha 1882](../New%20Equinollogy/vetequus-api/prisma/schema.prisma))
Adicionado em `model Invoice`:
```prisma
bankPaymentId String?
```
ID que o Asaas devolve quando o cliente paga via app. `null` = fatura não foi paga pelo app.

### `src/domain/enterprise/entities/invoice.ts`
- Adicionado `bankPaymentId?: string | null` em `InvoiceProps`
- Getter + setter

### `src/infra/shared/database/prisma/mappers/PrismaInvoiceMapper.ts`
- `toDomain` e `toPrisma` propagam `bankPaymentId`

### `src/domain/application/repositories/invoice.repository.ts`
- `InvoiceWhere.companyId` virou opcional (era obrigatório). Necessário porque o app cliente busca invoices dele em todas as empresas, não em uma só.

### `src/domain/application/repositories/company.repository.ts` + `prismaCompany.repository.ts`
- Novo método `findByInvoiceId(invoiceId)` — busca a empresa dona da fatura. Usado pelos handlers de pagamento de fatura para ler `walletId`.

### `src/domain/application/services/invoice/invoice.service.ts` — **reescrito**
Adicionados 3 métodos espelhando `TransactionService`:
- `payPix({ invoiceId, clientId })`
- `payExistingCreditCard({ invoiceId, clientId, creditCardId, installmentCount })`
- `payNewCreditCard({ invoiceId, clientId, installmentCount, creditCard, creditCardHolderInfo })`

Lógica idêntica ao `TransactionService` (busca client/company, valida `walletId` e `paymentId`, chama Asaas, persiste `bankPaymentId`). Diferenças:
- Fatura é sempre `installmentCount: 1` (não tem parcelas como Payment)
- Validação extra: bloqueia pagamento se `invoice.status === "PAID"` (idempotência)
- Grava em `Invoice.bankPaymentId` em vez de `Transaction.bankPaymentId`
- Marca `invoice.status = "PAID"` + `invoice.paidAt = now()` quando o pagamento de cartão fecha

Construtor agora injeta: `ClientRepository, CompanyRepository, CreditCardRepository, PixPayment, CreditCardPayment` (além de `InvoiceRepository`).

### `src/infra/http/controllers/invoice/dto/invoice.dto.ts`
Adicionados:
- `PayInvoiceWithNewCardDto` (creditCard + creditCardHolderInfo + installmentCount)
- `PayInvoiceWithExistingCardDto` (creditCardId + installmentCount)

PIX não tem DTO (só path param).

### `src/infra/http/controllers/invoice/invoice.controller.ts` — **adicionados 3 endpoints**
- `POST /invoice/:id/pay/pix` → retorna `{ payment: { encodedImage, payload } }` (mesmo formato de `/transaction/pix/:id`)
- `POST /invoice/:id/pay/credit/new` → mesma resposta de `/transaction/credit/new`
- `POST /invoice/:id/pay/credit/existing` → mesma resposta de `/transaction/credit/existing`

Os endpoints antigos (`POST /invoice`, `PUT /invoice/:id`, etc) continuam idênticos.

### `src/infra/http/controllers/invoice/clientInvoice.controller.ts` — **NOVO**
`@Controller('client-invoice')` análogo a `ClientPaymentController`:
- `GET /client-invoice?page=1&animalId=&status=` — exige token de `client`
- Filtra automaticamente por `clientId === userId` (token)
- Resolve a `Company` de cada fatura (para popular `walletId/payable`)
- Devolve com `ClientInvoicePresenter`

### `src/infra/http/presenters/clientInvoice.presenter.ts` — **NOVO**
Serializa `Invoice` no **mesmo formato** que `PaymentDetailsPresenter` usa para `Payment`. Estratégia:
- Cria 1 "pseudo-transaction" cujo `id` é o próprio `invoice.id` (id que o app usa pra chamar o pagamento)
- Adiciona `_kind: "invoice"` para o app diferenciar
- `payable: true` apenas se `invoice.status === "PENDING"` E `company.walletId` populado

Assim o app renderiza Movimentação e Fatura **com o mesmo componente**, sem mappers diferentes.

### `src/infra/http/modules/invoice.module.ts`
- Importa `BankModule.register()` (para PixPayment + CreditCardPayment Asaas) e `DatabaseModule` (para todos os repositórios)
- Registra `InvoiceController` + `ClientInvoiceController`

---

## Frontend (app-v2) — arquivos alterados

### `lib/api-routes.ts`
Adicionado:
```ts
ClientInvoice: { list: "/client-invoice" },
Invoice: {
  payPix: (id) => `/invoice/${id}/pay/pix`,
  payCreditNew: (id) => `/invoice/${id}/pay/credit/new`,
  payCreditExisting: (id) => `/invoice/${id}/pay/credit/existing`,
},
```

### `data/types.ts`
- `ClientPayment._kind?: "payment" | "invoice"` — novo campo opcional

### `lib/api-mappers.ts`
- `mapClientPayment` agora propaga `_kind` (default "payment")
- `mapClientPayment.status` deriva de `transactions` se `raw.status` não vier (corrige o caso da fatura já paga)
- **Nova função** `mapClientInvoicesList(body)` — mesma normalização, lê a chave `payments` do response

### Telas `app/(tabs)/index.tsx`, `app/(tabs)/finances.tsx`, `app/(animal)/payments.tsx`
- Buscam `GET /client-payment` E `GET /client-invoice` em paralelo via `Promise.allSettled`
- Mesclam as duas listas em uma única, ordenando por `firstDueDate` descendente
- Se um endpoint falhar, o outro renderiza normalmente (resiliência)
- Filtro por animal (em `payments.tsx`) é aplicado depois do merge

### `components/sheets/InvoicePaymentSheet.tsx`
- `handleGeneratePixQr` — se `_kind === "invoice"`, chama `POST /invoice/:id/pay/pix`; senão, `POST /transaction/pix/:transactionId` (como antes)
- `handlePayWithExistingCard` — análogo: invoice usa novo endpoint, sem `transactionId` no body
- `handlePayWithNewCard` — análogo

A UI do sheet é **idêntica** para os dois casos — o cliente nem vê diferença ao tocar em PIX/cartão.

### `components/cards/InvoiceCard.tsx`
- Nova prop `kind?: "payment" | "invoice"`
- Quando `kind === "invoice"`, mostra um pequeno badge violeta **"FATURA"** no topo do card. Movimentação não tem badge (fica visualmente neutra como sempre).

### `app/(tabs)/index.tsx`
- Passa `kind={item._kind}` para o `<InvoiceCard>`

---

## Fluxos end-to-end

### Cliente paga uma Movimentação (igual antes)
1. Veterinário cria movimentação no web → `POST /payment` → cria `Payment` + N `Transaction` parcelas
2. App cliente lista via `GET /client-payment` (também recebe `/client-invoice` em paralelo, mas vazio)
3. Cliente toca em fatura, sheet abre → escolhe PIX
4. App chama `POST /transaction/pix/:transactionId`
5. Asaas devolve QR code → app exibe

### Cliente paga uma Fatura (novo)
1. Veterinário cria fatura no web → `POST /invoice` → cria `Invoice` (sem Transaction)
2. App cliente lista via `GET /client-invoice` → recebe a fatura **no formato de Payment** (com pseudo-transaction)
3. Mesclada com movimentações na tela Home/Finanças (badge violeta "FATURA")
4. Cliente toca, sheet abre → escolhe PIX
5. App detecta `_kind === "invoice"` → chama `POST /invoice/:id/pay/pix`
6. Asaas devolve QR code → app exibe (mesmo componente!)
7. Quando o cliente paga, backend grava `Invoice.bankPaymentId` + (no caso de cartão) `status = "PAID"` e `paidAt = now()`

---

## Como verificar

### Antes de subir
- [ ] `cd vetequus-api && npx prisma migrate dev --name invoice_bank_payment_id` rodou sem erro
- [ ] `npx tsc --noEmit` no back retorna 0
- [ ] `npx nest build` no back retorna 0
- [ ] `npx tsc --noEmit` no `equinology-app-v2` retorna 0

### Smoke test
- [ ] Veterinário: criar fatura no painel web (`Criar Fatura` em `/financial`) com `clientId` preenchido
- [ ] App cliente: abrir Finanças → fatura aparece com badge **"FATURA"** roxo
- [ ] Tocar na fatura → sheet abre normalmente, valor correto, 1 "parcela" listada
- [ ] Tocar em "PIX" → "Gerar QR Code PIX" → QR aparece
- [ ] (Em sandbox Asaas) confirmar pagamento → fatura vira `status: "PAID"` no painel web

### Edge cases
- [ ] Fatura sem `clientId` (criada sem cliente): não aparece pra ninguém no app (esperado)
- [ ] Fatura cuja empresa não tem `walletId`: aparece no app mas `payable: false` → mostra aviso âmbar "Pagamento não disponível"
- [ ] Fatura já paga (`status: PAID`): aparece com badge verde "Pago", sem botão de pagar
- [ ] Cliente sem `paymentId`: tentativa de pagamento retorna toast "Cliente não possui cadastro de pagamento..."
- [ ] Endpoint `/client-invoice` indisponível (500/timeout): `/client-payment` ainda funciona; a tela não trava

---

## O que **não** mudou

- ✅ `POST /invoice` continua igual (web cria fatura do mesmo jeito)
- ✅ `PUT /invoice/:id`, `DELETE /invoice/:id`, `GET /invoice`, `GET /invoice/:id` continuam idênticos
- ✅ Painel web `InvoicesTable` e `NewInvoiceSheet` continuam idênticos
- ✅ Tabela `transactions` continua intacta (nenhum campo novo)
- ✅ `POST /transaction/pix/:id` e `/credit/*` continuam idênticos
- ✅ `Payment` + `Transaction` + `/client-payment` continuam idênticos

---

**Arquivos novos:** 2 (`clientInvoice.controller.ts`, `clientInvoice.presenter.ts`)
**Arquivos editados (back):** 8 (`schema.prisma`, `invoice.ts` entity, `PrismaInvoiceMapper.ts`, `invoice.repository.ts` interface, `company.repository.ts` interface + impl, `invoice.service.ts`, `invoice.controller.ts`, `invoice.dto.ts`, `invoice.module.ts`)
**Arquivos editados (app):** 6 (`api-routes.ts`, `types.ts`, `api-mappers.ts`, `(tabs)/index.tsx`, `(tabs)/finances.tsx`, `(animal)/payments.tsx`, `InvoicePaymentSheet.tsx`, `InvoiceCard.tsx`)

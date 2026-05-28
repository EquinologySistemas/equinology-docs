# Modelo de dados

> **Fonte da verdade:** `vetequus-api/prisma/schema.prisma` (~81 models). Este
> documento é um mapa de leitura — não duplica colunas. Para campos exatos, leia
> o schema.

## Atores

| Model | Quem é | Token (payload) |
|---|---|---|
| `User` | Vet / gestor / colaborador (enum `UserRole`) | `{ sub, companyId, type: 'user' }` |
| `Client` | Tutor (dono do cavalo) | `{ sub, companyId: 'no-company', type: 'client' }` |
| `AdminUser` | Equipe interna (`super_admin`/`support`) | `{ sub, type: 'admin' }` |

## Núcleo multi-tenant

- `Company` (tenant) ⟶ tem muitos `User`, `Animal`, `Client` (via `ClientCompany` N:N).
- `Animal` pertence a um `Client` e (opcionalmente) a um `StudFarm` (haras) e `Company`.
- `Appointment` → `AppointmentAnimal` (pivô) — **âncora de todos os registros clínicos**.

## Grupos de entidades

| Grupo | Models | Observação |
|---|---|---|
| Clínico (~41 tipos) | `Dentistry*` (6), `General*` (4), `Orthopedic*` (6), `Reproduction*` (24) | Todos keyados em `appointmentAnimalId` |
| Saúde recorrente | `Exam`, `Vaccine`, `Deworming`, `Shoeing`, `SanitaryProtocol(+Item)` | |
| Financeiro | `Invoice` (fatura), `Payment` (movimentação), `Transaction`, `TransactionCategory`, `BankAccount`, `CreditCard` | Ponte fatura→caixa: [ADR 0001](../decisions/0001-fatura-caixa.md) |
| Assinatura/billing | `SignaturePlan`, `CompanySignature`, `Coupon` | Integra Asaas |
| Estoque | `Product`, `ProductCategory`, `ProductStock`, `ProductUsage`, `FieldStock`, `Tag`, `ProductTag` | `FieldStock` = estoque volante |
| CRM | `Board`, `Lead` | Kanban |
| Marketing/admin | `Advertisement(+State/+City)`, `AdminUser` | Anúncios com escopo geo |
| Util | `Note`, `Reminder`, `RecoverPasswordCode` | |

## Convenções

- PK `uuid` (`@db.Uuid`); `@@map` define o nome real da tabela (snake_case).
- Quase toda entidade de negócio carrega `companyId` para isolamento.
- `Invoice.payments` (relação reversa, `onDelete: SetNull`) liga fatura à
  movimentação de caixa gerada — ver [ADR 0001](../decisions/0001-fatura-caixa.md).

## Drift de schema (atenção)

Há sinais históricos de divergência código↔banco: migration
`20260310134228_recreate_drift`, migrations de nome vazio, e
`prisma/update_boards_leads.sql` (patch fora do Prisma). Ao mexer no schema,
prefira `prisma migrate` e evite SQL manual. Ver [auditoria](../archive/AUDITORIA-VETEQUUS-2026-05-28.md), Fase 2 item 13.

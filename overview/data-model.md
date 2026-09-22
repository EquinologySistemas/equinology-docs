# Modelo de dados

Os campos, tipos, índices e relações são definidos em `prisma/schema.prisma`, no repositório da API.

## Identidade e relações

| Model | Responsabilidade |
|---|---|
| `Company` | Clínica/empresa do SaaS |
| `User` | Profissional vinculado à empresa, com perfil de acesso |
| `Client` | Tutor, com telefone/e-mail/CPF únicos quando preenchidos e código de vínculo |
| `ClientCompany` | Associação entre tutor e clínica |
| `ClientStudFarm` | Associação entre tutor e propriedade |
| `AdminUser` | Usuário da equipe administrativa |
| `Animal` | Cadastro do animal e vínculos com proprietário, empresa e propriedade |
| `StudFarm` | Propriedade, endereço e contato do responsável |

O telefone do tutor é normalizado em dígitos e identifica o cadastro no primeiro acesso. E-mail e CPF são opcionais no modelo; os fluxos de acesso e pagamento solicitam os dados necessários para cada operação.

## Grupos funcionais

| Área | Entidades e relações principais |
|---|---|
| Atendimento | `Appointment` reúne participantes `AppointmentAnimal`; os registros clínicos usam esse vínculo |
| Clínico | Famílias `Dentistry*`, `General*`, `Orthopedic*`, `Reproduction*`; diagnóstico de receptora usa `ReproductionDiagnosisStage` |
| Compartilhamento | `OwnerNote` por atendimento/animal; prescrições com `sharedWithOwner`; `AnimalNote` distingue autores `VET` e `OWNER` |
| Saúde | `Vaccine`, `Deworming`, `Exam`, `Shoeing`, `SanitaryProtocol`, `SanitaryProtocolItem` |
| Arquivos | `Attachment` identifica tipo/ID do registro, URL, nome, ordem e metadados |
| Financeiro | `Invoice`, `Payment`, `Transaction`, `TransactionCategory`, `BankAccount`, `CreditCard` |
| SaaS | `SignaturePlan`, `CompanySignature`, `Coupon` |
| Estoque | `Product`, `ProductCategory`, `ProductStock`, `ProductUsage`, `FieldStock`, `Tag`, `ProductTag` |
| CRM | `Board`, `Lead` |
| Conteúdo | `Advertisement` e segmentação; `Tutorial`, `TutorialChapter` |
| Organização | `Note`, `Reminder` com recorrência |
| Infraestrutura | `RecoverPasswordCode`, `IdempotencyKey` |

## Financeiro

`Invoice` representa a fatura. `Payment` representa a movimentação e `Transaction`, seus lançamentos/parcelas. `Payment.invoiceId` associa o recebimento ao caixa. As cobranças externas são relacionadas por `bankPaymentId`. Leia [fatura e caixa](../decisions/0001-fatura-caixa.md) antes de alterar esse fluxo.

## Situação dos registros

Os campos `deletedAt` preservam referências ao arquivar registros nos fluxos que os utilizam. A exclusão solicitada pelo próprio tutor também limpa seus dados de cadastro, credenciais e conteúdo pessoal associado. O estado do portador do token é conferido em `src/infra/shared/auth/session-validity.ts`.

Consulte o serviço e o repositório da operação para determinar exatamente quais dados são alterados; o nome de um campo isolado não substitui o contrato do fluxo.

## Evolução do banco

O histórico está em `prisma/migrations/`. Entre as migrations recentes estão telefone único do tutor, anexos, conteúdo ao proprietário, auditoria de estoque, associação de cobranças e `20260831120000_add_idempotency_keys`.

A situação de um banco é conferida com `prisma migrate status` no ambiente escolhido. O repositório registra o histórico a aplicar; o estado da instância é consultado durante a operação. Veja [setup](../guides/developer/setup.md) e [deploy](../guides/operations/deploy.md).

# Personas e fluxos críticos

## Personas

| Persona | App | O que faz |
|---|---|---|
| **Veterinário / gestor** | web (`equinology-web-v2`) | Cadastra clientes/animais, faz atendimentos clínicos, emite faturas, gere estoque, CRM, assinatura |
| **Tutor** (dono do cavalo) | app (`equinology-app-v2`) | Vê seus animais e histórico (somente leitura clínica), paga faturas (PIX/cartão) |
| **Equipe Equinology** | admin (`equinology-adm`) | Gere tenants, planos, cupons, anúncios; vê financeiro do SaaS |

## Fluxos críticos

### 1. Cadastro e acesso (vet)
`POST /user/register` → JWT (90 dias) → middleware da web valida assinatura
(`GET /signature/validation`); se inválida, redireciona para `/plans`.

### 2. Atendimento clínico
`POST /appointment` → `AppointmentAnimal` → registros clínicos (41 tipos), com
upload opcional via `POST /file` (autenticado). Ver [VET-TIPOS-ATENDIMENTO](../archive/from-app/VET-TIPOS-ATENDIMENTO.md).

### 3. Fatura → Caixa (ponte automática)
Vet emite `Invoice` → tutor paga no app (Asaas) → ao receber, a API cria
automaticamente uma `Payment` (movimentação de caixa), idempotente por `invoiceId`.
Detalhes e justificativa: [ADR 0001](../decisions/0001-fatura-caixa.md).

### 4. Assinatura do SaaS
Empresa assina plano → `CompanySignature` (subscription recorrente no Asaas, PIX
ou cartão) → webhook (`POST /signature/webhook`, autenticado por token) valida o
pagamento e ativa/renova. Endurecimento: [ADR 0003](../decisions/0003-webhook-asaas.md).

### 5. Primeiro acesso do tutor (app)
Valida email + CPF → recebe código → define senha (`/client/*`).

## Limites conhecidos (ver auditoria)

- Validação de acesso considera `status` mas **não checa `expirationDate`** para
  assinaturas `ACTIVE` (assinatura vencida ainda libera até webhook/cron mudar o status).
- App é **somente leitura** para registros de saúde/clínicos.

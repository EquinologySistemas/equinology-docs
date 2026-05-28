# API - Registro de Usuário e Empresa (Company)

Documentação do fluxo de registro com suporte a criar nova empresa ou vincular a uma existente.

---

## 📋 Índice

- [Visão Geral](#visão-geral)
- [Criar Nova Empresa](#criar-nova-empresa)
- [Vincular a Empresa Existente](#vincular-a-empresa-existente)
- [Buscar Dados da Empresa](#buscar-dados-da-empresa)
- [Fluxo Visual](#fluxo-visual)

---

## Visão Geral

O sistema suporta dois fluxos de registro:

| Cenário | Campo `newCompany` | Campos Obrigatórios |
|---------|-------------------|---------------------|
| Criar nova empresa | `true` | `cpfCnpj`, `address`, `number`, `postalCode` |
| Vincular a empresa existente | `false` | `companyCode` |

---

## Criar Nova Empresa

**Endpoint:** `POST /user/register`

Quando o usuário não tem um código de empresa e quer criar uma nova.

### Request

```json
{
  "name": "João Silva",
  "email": "joao@email.com",
  "password": "123456",
  "phone": "11999999999",
  "newCompany": true,
  "cpfCnpj": "12345678901",
  "address": "Rua Teste",
  "number": "123",
  "postalCode": "12345678",
  "companyName": "Clínica Vet ABC"
}
```

### Campos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `name` | string | ✅ Sim | Nome do usuário |
| `email` | string | ✅ Sim | Email do usuário |
| `password` | string | ✅ Sim | Senha |
| `phone` | string | ✅ Sim | Telefone |
| `newCompany` | boolean | ✅ Sim | Deve ser `true` |
| `cpfCnpj` | string | ✅ Sim | CPF ou CNPJ (com ou sem formatação) |
| `address` | string | ✅ Sim | Endereço |
| `number` | string | ✅ Sim | Número |
| `postalCode` | string | ✅ Sim | CEP |
| `companyName` | string | ❌ Não | Nome da empresa (usa nome do usuário se não informado) |

### Response

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

### O que acontece internamente

1. Sistema valida se o email já existe
2. Cria a `Company` com um `code` UUID gerado automaticamente
3. Cria o `User` vinculado à Company
4. Retorna o `accessToken` JWT

---

## Vincular a Empresa Existente

**Endpoint:** `POST /user/register`

Quando o usuário tem um código de convite de uma empresa existente.

### Request

```json
{
  "name": "Maria Santos",
  "email": "maria@email.com",
  "password": "123456",
  "phone": "11888888888",
  "newCompany": false,
  "companyCode": "550e8400-e29b-41d4-a716-446655440000"
}
```

### Campos

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `name` | string | ✅ Sim | Nome do usuário |
| `email` | string | ✅ Sim | Email do usuário |
| `password` | string | ✅ Sim | Senha |
| `phone` | string | ✅ Sim | Telefone |
| `newCompany` | boolean | ✅ Sim | Deve ser `false` |
| `companyCode` | string | ✅ Sim | Código UUID da empresa |

> **Nota:** Os campos `cpfCnpj`, `address`, `number`, `postalCode` e `companyName` são **opcionais** neste cenário.

### Response

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

### Erros Possíveis

| Erro | Descrição |
|------|-----------|
| `ResourceNotFoundError` | Código da empresa não encontrado |
| `ResourceAlreadyExistsError` | Email já cadastrado |

---

## Buscar Dados da Empresa

**Endpoint:** `GET /company/id`

Rota para o usuário logado buscar os dados da sua empresa, incluindo o código para compartilhar.

### Request

```
GET /company/id
Authorization: Bearer <token>
```

### Response

```json
{
  "company": {
    "id": "uuid-da-empresa",
    "name": "Clínica Vet ABC",
    "code": "550e8400-e29b-41d4-a716-446655440000",
    "cnpj": "12345678901234",
    "paymentId": "cus_xxx",
    "paymentType": "CPF",
    "address": "Rua Teste",
    "number": "123",
    "postalCode": "12345678",
    "createdAt": "2024-01-01T00:00:00.000Z",
    "updatedAt": "2024-01-01T00:00:00.000Z"
  }
}
```

### Como usar o código

O campo `code` é o código de convite que pode ser compartilhado com outros usuários para que eles se vinculem à empresa durante o registro.

---

## Fluxo Visual

```
┌─────────────────────────────────────────────────────────────────┐
│                    POST /user/register                          │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
              ┌─────────────────────────────┐
              │    newCompany = true?       │
              └─────────────┬───────────────┘
                  SIM │           │ NÃO
                      ▼           ▼
     ┌────────────────────┐   ┌────────────────────────┐
     │  CRIAR EMPRESA     │   │  VINCULAR EMPRESA      │
     │                    │   │                        │
     │ • Valida cpfCnpj   │   │ • Busca pelo           │
     │ • Cria Company     │   │   companyCode          │
     │ • Gera code UUID   │   │ • Valida se existe     │
     └─────────┬──────────┘   └───────────┬────────────┘
               │                          │
               └────────────┬─────────────┘
                            ▼
              ┌─────────────────────────────┐
              │   Cria User vinculado       │
              │   à Company                 │
              └─────────────┬───────────────┘
                            ▼
              ┌─────────────────────────────┐
              │   Retorna accessToken       │
              └─────────────────────────────┘


┌─────────────────────────────────────────────────────────────────┐
│              USUÁRIO LOGADO - COMPARTILHAR CÓDIGO               │
└─────────────────────────────────────────────────────────────────┘
                            │
                            ▼
              ┌─────────────────────────────┐
              │   GET /company/id           │
              │   Authorization: Bearer xxx │
              └─────────────┬───────────────┘
                            ▼
              ┌─────────────────────────────┐
              │   Response:                 │
              │   {                         │
              │     name: "Clínica ABC",    │
              │     code: "uuid-xxx"  ← ─ ─ ┼ ─ ─ Compartilhar!
              │   }                         │
              └─────────────────────────────┘
```

---

## Exemplo de Uso no Frontend

```typescript
// 1. Após login, buscar dados da empresa
const getCompanyData = async (token: string) => {
  const response = await fetch('/company/id', {
    headers: { Authorization: `Bearer ${token}` }
  });
  return response.json();
};

// 2. Mostrar código para o usuário compartilhar
const showInviteCode = async () => {
  const { company } = await getCompanyData(token);
  
  Alert.alert(
    'Código de Convite',
    `Compartilhe este código para novos colaboradores:\n\n${company.code}`,
    [{ text: 'Copiar', onPress: () => Clipboard.setString(company.code) }]
  );
};

// 3. Novo usuário se registrando com código
const registerWithCode = async (code: string, userData: UserData) => {
  const response = await fetch('/user/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      ...userData,
      newCompany: false,
      companyCode: code
    })
  });
  return response.json();
};
```

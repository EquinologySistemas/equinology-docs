> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# VetEquus API - Documentação de Rotas

Esta documentação lista todas as rotas disponíveis na API VetEquus, organizadas por categoria e com detalhes sobre métodos HTTP, parâmetros e respostas.

---

## 📋 Índice

- [Autenticação e Conta](#autenticação-e-conta)
  - [User](#user)
  - [Company](#company)
  - [Password Code](#password-code)
  - [Client](#client)
- [Animais](#animais)
  - [Animal](#animal)
  - [Animal Note](#animal-note)
  - [Vaccine](#vaccine)
  - [Deworming](#deworming)
  - [Exam](#exam)
  - [Shoeing (Ferrageamento)](#shoeing-ferrageamento)
  - [Sanitary Protocol](#sanitary-protocol-protocolos-sanitários-da-fazenda)
- [Procedimentos Gerais](#procedimentos-gerais)
  - [General Info](#general-info)
  - [General Prescription](#general-prescription)
  - [General Service](#general-service)
  - [General Test](#general-test)
- [Procedimentos Ortopédicos](#procedimentos-ortopédicos)
  - [Orthopedic Info](#orthopedic-info)
  - [Orthopedic Prescription](#orthopedic-prescription)
  - [Orthopedic Service](#orthopedic-service)
  - [Orthopedic Test](#orthopedic-test)
  - [Orthopedic Blockage](#orthopedic-blockage)
  - [Orthopedic Extra](#orthopedic-extra)
- [Procedimentos Odontológicos](#procedimentos-odontológicos)
  - [Dentistry Assessment](#dentistry-assessment)
  - [Dentistry Exam](#dentistry-exam)
  - [Dentistry Odontogram](#dentistry-odontogram)
  - [Dentistry Oral](#dentistry-oral)
  - [Dentistry Report](#dentistry-report)
  - [Dentistry Sedation](#dentistry-sedation)
- [Reprodução](#reprodução)
  - [Breeding (Fêmeas Prenhas)](#breeding-fêmeas-prenhas)
  - [Donor (Doadoras)](#donor-doadoras)
  - [Receptor (Receptoras)](#receptor-receptoras)
  - [Stallion (Garanhões)](#stallion-garanhões)
- [Agendamentos](#agendamentos)
  - [Appointment](#appointment)
  - [Appointment Animal](#appointment-animal)
- [Financeiro](#financeiro)
  - [Payment](#payment)
  - [Transaction](#transaction)
  - [Transaction Category](#transaction-category)
  - [Bank Account](#bank-account)
  - [Credit Card](#credit-card)
- [Estoque](#estoque)
  - [Product](#product)
  - [Product Category](#product-category)
  - [Product Stock](#product-stock)
  - [Product Usage](#product-usage)
  - [Field Stock](#field-stock)
  - [Stock Statistics](#stock-statistics)
- [CRM](#crm)
  - [Board](#board)
  - [Lead](#lead)
- [Outros](#outros)
  - [Stud Farm (Haras)](#stud-farm-haras)
  - [Tag](#tag)
  - [File](#file)
  - [Note](#note)
  - [Signature](#signature)

---

## Autenticação e Conta

### User
Rota base: `/user`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/signin` | Autenticar usuário | Pública |
| `POST` | `/register` | Registrar novo usuário e empresa | Pública |
| `POST` | `/` | Criar usuário (admin) | Requer token |
| `PUT` | `/:userId` | Atualizar usuário | Requer token |
| `PUT` | `/profile` | Atualizar perfil próprio | Requer token |
| `PATCH` | `/token` | Renovar token | Requer token |
| `PUT` | `/password` | Recuperar senha | Pública |
| `GET` | `/` | Listar usuários da empresa | Requer token |

#### POST `/signin` - Autenticar Usuário
**Body:**
```json
{
  "email": "string (obrigatório)",
  "password": "string (obrigatório)"
}
```
**Resposta:**
```json
{
  "accessToken": "string"
}
```

#### POST `/register` - Registrar Usuário
**Body:**
```json
{
  "name": "string (obrigatório)",
  "email": "string (obrigatório)",
  "password": "string (obrigatório)",
  "phone": "string (obrigatório)",
  "cpfCnpj": "string (obrigatório)",
  "address": "string (opcional)",
  "number": "string (opcional)",
  "postalCode": "string (opcional)",
  "companyName": "string (opcional)"
}
```
**Resposta:**
```json
{
  "accessToken": "string"
}
```

#### POST `/` - Criar Usuário
**Body:**
```json
{
  "name": "string (obrigatório)",
  "email": "string (obrigatório)",
  "password": "string (obrigatório)",
  "phone": "string (obrigatório)"
}
```

#### PUT `/:userId` - Atualizar Usuário
**Parâmetros de rota:**
- `userId`: UUID do usuário

**Body:**
```json
{
  "name": "string (opcional)",
  "email": "string (opcional)",
  "phone": "string (opcional)"
}
```

#### GET `/` - Listar Usuários
**Resposta:**
```json
{
  "users": [
    {
      "id": "string",
      "name": "string",
      "email": "string",
      "phone": "string"
    }
  ]
}
```

---

### Company
Rota base: `/company`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `PUT` | `/` | Atualizar empresa | Requer token |
| `GET` | `/id` | Obter dados da empresa | Requer token |

#### PUT `/` - Atualizar Empresa
**Body:** Campos conforme `EditCompanyDto`

#### GET `/id` - Obter Empresa
**Resposta:**
```json
{
  "company": {
    "id": "string",
    "name": "string",
    ...
  }
}
```

---

### Password Code
Rota base: `/password-code`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Solicitar código de recuperação de senha | Pública |
| `GET` | `/:code` | Validar código de recuperação | Pública |

#### POST `/` - Solicitar Código
**Body:**
```json
{
  "email": "string (obrigatório)"
}
```

#### GET `/:code` - Validar Código
**Parâmetros de rota:**
- `code`: Código de recuperação recebido por email

---

### Client
Rota base: `/client`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar cliente | Requer token |
| `POST` | `/auth` | Autenticar cliente | Pública |
| `POST` | `/register` | Registrar cliente | Pública |
| `POST` | `/token` | Gerar token para cliente | Requer token |
| `POST` | `/recover-password` | Recuperar senha | Requer token |
| `PUT` | `/:clientId` | Atualizar cliente | Requer token |
| `DELETE` | `/:clientId` | Deletar cliente | Requer token |
| `GET` | `/` | Listar clientes | Requer token |
| `GET` | `/cpf/:cpf` | Buscar cliente por CPF | Requer token |

#### POST `/` - Criar Cliente
**Body:**
```json
{
  "name": "string (obrigatório)",
  "phone": "string (obrigatório)",
  "email": "string (obrigatório)",
  "cpf": "string (obrigatório)",
  "password": "string (opcional, min 6 caracteres)"
}
```

#### POST `/auth` - Autenticar Cliente
**Body:**
```json
{
  "email": "string (obrigatório)",
  "password": "string (obrigatório)"
}
```

#### GET `/` - Listar Clientes
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Número da página |
| `query` | string | Não | Termo de busca |

**Resposta:**
```json
{
  "clients": [...],
  "pages": "number"
}
```

---

## Animais

### Animal
Rota base: `/animal`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar animal | Requer token |
| `POST` | `/register/:code` | Registrar animal por código | Requer token |
| `PUT` | `/:id` | Atualizar animal | Requer token |
| `GET` | `/` | Listar animais | Requer token |
| `GET` | `/:code` | Buscar animal por código | Requer token |

#### POST `/` - Criar Animal
**Body:**
```json
{
  "name": "string (obrigatório)",
  "breed": "string (obrigatório)",
  "gender": "STALLION | BREEDING | DONOR | RECEPTOR | CASTRATED (obrigatório)",
  "pureBlood": "boolean (obrigatório)",
  "color": "string (obrigatório)",
  "birthDate": "date string (obrigatório)",
  "studFarmId": "string (opcional)",
  "clientId": "string (opcional)",
  "photoUrl": "string (opcional)"
}
```

#### PUT `/:id` - Atualizar Animal
**Parâmetros de rota:**
- `id`: UUID do animal

**Body:** Todos os campos são opcionais (mesmos campos do POST)

#### GET `/` - Listar Animais
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página da listagem |
| `query` | string | Não | Busca textual |
| `clientId` | UUID | Não | Filtrar por cliente |
| `studFarmId` | UUID | Não | Filtrar por haras |
| `gender` | array | Não | Filtrar por gênero(s) |
| `breed` | string | Não | Filtrar por raça |
| `color` | string | Não | Filtrar por cor |
| `pureBlood` | boolean | Não | Filtrar por puro sangue |
| `birthDateStart` | date | Não | Data nascimento início |
| `birthDateEnd` | date | Não | Data nascimento fim |

**Resposta:**
```json
{
  "animals": [...],
  "pages": "number"
}
```

---

### Animal Note
Rota base: `/animal-note`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar nota | Requer token |
| `PUT` | `/:animalNoteId` | Atualizar nota | Requer token |
| `DELETE` | `/:animalNoteId` | Deletar nota | Requer token |
| `GET` | `/animal/:animalId` | Listar notas por animal | Requer token |

#### POST `/` - Criar Nota
**Body:**
```json
{
  "content": "string (obrigatório)",
  "animalId": "string (obrigatório)"
}
```

---

### Vaccine
Rota base: `/vaccine`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar vacina | Requer token |
| `PUT` | `/:id` | Atualizar vacina | Requer token |
| `DELETE` | `/:id` | Deletar vacina | Requer token |
| `GET` | `/:animalId` | Listar vacinas por animal | Requer token |
| `GET` | `/soon/:animalId` | Listar vacinas próximas | Requer token |

#### POST `/` - Criar Vacina
**Body:**
```json
{
  "name": "string (obrigatório)",
  "animalId": "string (obrigatório)",
  "date": "date (obrigatório)",
  "nextDate": "date (opcional)",
  "description": "string (opcional)",
  "userId": "string (opcional)",
  "location": "string (opcional)"
}
```

#### GET `/:animalId` - Listar Vacinas
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `query` | string | Não | Termo de busca |

---

### Deworming
Rota base: `/deworming`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar vermifugação | Requer token |
| `PUT` | `/:id` | Atualizar vermifugação | Requer token |
| `DELETE` | `/:id` | Deletar vermifugação | Requer token |
| `GET` | `/:animalId` | Listar vermifugações | Requer token |
| `GET` | `/soon/:animalId` | Listar vermifugações próximas | Requer token |

#### POST `/` - Criar Vermifugação
**Body:**
```json
{
  "name": "string (obrigatório)",
  "animalId": "string (obrigatório)",
  "date": "date (obrigatório)",
  "nextDate": "date (opcional)",
  "description": "string (opcional)",
  "userId": "string (opcional)"
}
```

---

### Exam
Rota base: `/exam`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar exame | Requer token |
| `PUT` | `/:id` | Atualizar exame | Requer token |
| `DELETE` | `/:id` | Deletar exame | Requer token |
| `GET` | `/:animalId` | Listar exames por animal | Requer token |

#### POST `/` - Criar Exame
**Body:**
```json
{
  "name": "string (obrigatório)",
  "animalId": "string (obrigatório)",
  "date": "date (obrigatório)",
  "laboratory": "string (opcional)",
  "result": "string (opcional)",
  "resultFileUrl": "string (opcional)",
  "userId": "string (opcional)"
}
```

---

### Shoeing (Ferrageamento)
Rota base: `/shoeing`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar registro de ferrageamento | Requer token |
| `PUT` | `/:id` | Atualizar registro | Requer token |
| `DELETE` | `/:id` | Deletar registro | Requer token |
| `GET` | `/:animalId` | Listar por animal | Requer token |
| `GET` | `/soon/:animalId` | Listar próximos (vencendo) | Requer token |

#### POST `/` - Criar Ferrageamento
**Body:**
```json
{
  "type": "TRIMMING | SHOEING | ORTHOPEDIC (obrigatório)",
  "animalId": "string (obrigatório)",
  "date": "date (obrigatório)",
  "nextDate": "date (opcional)",
  "farrierName": "string (opcional)",
  "description": "string (opcional)",
  "photoUrl": "string (opcional)",
  "userId": "string (opcional)"
}
```

#### PUT `/:id` - Atualizar Ferrageamento
**Parâmetros de rota:**
- `id`: UUID do registro de ferrageamento

**Body:**
```json
{
  "type": "TRIMMING | SHOEING | ORTHOPEDIC (opcional)",
  "date": "date (opcional)",
  "nextDate": "date (opcional)",
  "farrierName": "string (opcional)",
  "description": "string (opcional)",
  "photoUrl": "string (opcional)"
}
```

#### GET `/:animalId` - Listar Ferrageamentos
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `query` | string | Não | Busca (ex: nome do ferreiro) |
| `type` | string | Não | Filtrar (TRIMMING/SHOEING/ORTHOPEDIC) |
| `startDate` | date | Não | Data início |
| `endDate` | date | Não | Data fim |

---

### Sanitary Protocol (Protocolos Sanitários da Fazenda)
Rota base: `/sanitary-protocol`

Esta rota gerencia os protocolos (pai) e permite a gestão dos itens (filhos) que compõem cada protocolo.

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar protocolo completo (com itens) | Requer token |
| `POST` | `/item` | Adicionar item a um protocolo existente | Requer token |
| `PUT` | `/:protocolId` | Atualizar dados do protocolo | Requer token |
| `PUT` | `/item/:itemId` | Atualizar um item específico | Requer token |
| `DELETE` | `/:protocolId` | Deletar protocolo (e seus itens) | Requer token |
| `DELETE` | `/item/:itemId` | Deletar item do protocolo | Requer token |
| `GET` | `/` | Listar protocolos da fazenda | Requer token |
| `GET` | `/:protocolId` | Obter detalhes e itens do protocolo | Requer token |

#### POST `/` - Criar Protocolo
**Body:**
```json
{
  "name": "string (obrigatório)",
  "description": "string (opcional)",
  "studFarmId": "string (obrigatório)",
  "targetCategory": "FOAL | MARE | STALLION (opcional)",
  "items": [
    {
      "name": "string (obrigatório)",
      "type": "VACCINE | DEWORMING | EXAM (obrigatório)",
      "periodDays": "number (opcional)",
      "isRecurrent": "boolean (obrigatório)",
      "observation": "string (opcional)"
    }
  ]
}
```

#### POST `/item` - Adicionar Item Extra
**Body:**
```json
{
  "protocolId": "string (obrigatório)",
  "name": "string (obrigatório)",
  "type": "VACCINE | DEWORMING | EXAM (obrigatório)",
  "periodDays": "number (opcional)",
  "isRecurrent": "boolean (obrigatório)",
  "observation": "string (opcional)"
}
```

#### PUT `/:protocolId` - Atualizar Protocolo (Dados Gerais)
**Body:**
```json
{
  "name": "string (opcional)",
  "description": "string (opcional)",
  "targetCategory": "string (opcional)"
}
```

#### PUT `/item/:itemId` - Atualizar Item
**Body:**
```json
{
  "name": "string (opcional)",
  "type": "string (opcional)",
  "periodDays": "number (opcional)",
  "isRecurrent": "boolean (opcional)",
  "observation": "string (opcional)"
}
```

#### GET `/` - Listar Protocolos
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `studFarmId` | string | Sim | ID do Haras |
| `query` | string | Não | Busca por nome do protocolo |

#### GET `/:protocolId` - Detalhes do Protocolo
**Resposta:**
```json
{
  "id": "uuid",
  "name": "Protocolo de Potros 2024",
  "description": "Protocolo padrão para nascidos em 2024",
  "targetCategory": "FOAL",
  "items": [
    {
      "id": "uuid",
      "name": "Vacina Tétano",
      "type": "VACCINE",
      "periodDays": 90,
      "isRecurrent": false,
      "observation": null
    },
    {
      "id": "uuid",
      "name": "Revisão Geral",
      "type": "EXAM",
      "periodDays": null,
      "isRecurrent": true,
      "observation": "Realizar se houver alteração comportamental"
    }
  ]
}
```

---

## Procedimentos Gerais

> **Padrão comum para todas as rotas de procedimentos:**
> Todas seguem o mesmo padrão de CRUD com `appointmentId` como parâmetro.

### General Info
Rota base: `/general-info`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/:appointmentId` | Criar informação geral | Requer token |
| `PUT` | `/:id` | Atualizar | Requer token |
| `DELETE` | `/:id` | Deletar | Requer token |
| `GET` | `/` | Listar | Requer token |

#### GET `/` - Listar
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `animalId` | UUID | Não | Filtrar por animal |
| `appointmentId` | UUID | Não | Filtrar por agendamento |

---

### General Prescription
Rota base: `/general-prescription`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar prescrição |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### General Service
Rota base: `/general-service`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar serviço |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### General Test
Rota base: `/general-test`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar teste |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

## Procedimentos Ortopédicos

### Orthopedic Info
Rota base: `/orthopedic-info`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar informação ortopédica |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Orthopedic Prescription
Rota base: `/orthopedic-prescription`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar prescrição |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Orthopedic Service
Rota base: `/orthopedic-service`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar serviço |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Orthopedic Test
Rota base: `/orthopedic-test`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar teste |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Orthopedic Blockage
Rota base: `/orthopedic-blockage`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar bloqueio |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Orthopedic Extra
Rota base: `/orthopedic-extra`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar extra |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

## Procedimentos Odontológicos

### Dentistry Assessment
Rota base: `/dentistry-assessment`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar avaliação |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Dentistry Exam
Rota base: `/dentistry-exam`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar exame |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Dentistry Odontogram
Rota base: `/dentistry-odontogram`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar odontograma |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Dentistry Oral
Rota base: `/dentistry-oral`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro oral |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Dentistry Report
Rota base: `/dentistry-report`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar relatório |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Dentistry Sedation
Rota base: `/dentistry-sedation`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar sedação |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

## Reprodução

### Breeding (Fêmeas Prenhas)

#### Reproduction Breeding Initial
Rota base: `/reproduction-breeding-initial`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro inicial |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Breeding Intermediate
Rota base: `/reproduction-breeding-intermediate`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro intermediário |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Breeding Pregnancy
Rota base: `/reproduction-breedingPregnancy`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de prenhez |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Breeding Post
Rota base: `/reproduction-breeding-post`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro pós-parto |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Breeding Birth
Rota base: `/reproduction-breeding-birth`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de nascimento |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Breeding Vaccines
Rota base: `/reproduction-breeding-vaccines`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de vacinas |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Donor (Doadoras)

#### Reproduction Donor Gyno
Rota base: `/reproduction-donor-gyno`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro ginecológico |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Donor Heat
Rota base: `/reproduction-donor-heat`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de cio |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Donor Ovulation
Rota base: `/reproduction-donor-ovulation`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de ovulação |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Donor Insemination
Rota base: `/reproduction-donor-insemination`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de inseminação |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Donor Embryo
Rota base: `/reproduction-donor-embryo`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de embrião |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Receptor (Receptoras)

#### Reproduction Receptor Gyno
Rota base: `/reproduction-receptor-gyno`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro ginecológico |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Heat
Rota base: `/reproduction-receptor-heat`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de cio |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Hormones
Rota base: `/reproduction-receptor-hormones`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de hormônios |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Inovulation
Rota base: `/reproduction-receptor-inovulation`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de inovulação |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Embryo
Rota base: `/reproduction-receptor-embryo`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de embrião |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Diagnosis
Rota base: `/reproduction-receptor-diagnosis`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar diagnóstico |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Monitoring
Rota base: `/reproduction-receptor-monitoring`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar monitoramento |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Final
Rota base: `/reproduction-receptor-final`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro final |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Receptor Vaccines
Rota base: `/reproduction-receptor-vaccines`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de vacinas |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

### Stallion (Garanhões)

#### Reproduction Stallion Collection
Rota base: `/reproduction-stallion-collection`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de coleta |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Stallion Physical
Rota base: `/reproduction-stallion-physical`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar exame físico |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Stallion Storage
Rota base: `/reproduction-stallion-storage`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de armazenamento |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

#### Reproduction Stallion Shipping
Rota base: `/reproduction-stallion-shipping`

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/:appointmentId` | Criar registro de envio |
| `PUT` | `/:id` | Atualizar |
| `DELETE` | `/:id` | Deletar |
| `GET` | `/` | Listar (page, animalId, appointmentId) |

---

## Agendamentos

### Appointment
Rota base: `/appointment`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar agendamento | Requer token |
| `PUT` | `/:id` | Atualizar agendamento | Requer token |
| `DELETE` | `/:id` | Deletar agendamento | Requer token |
| `GET` | `/details/:id` | Obter detalhes | Requer token |
| `GET` | `/monthly` | Listar agendamentos mensais | Requer token |
| `GET` | `/daily` | Listar agendamentos diários | Requer token |
| `GET` | `/fetch` | Listar agendamentos | Requer token |

#### POST `/` - Criar Agendamento
**Body:**
```json
{
  "startDate": "date (obrigatório)",
  "endDate": "date (obrigatório)",
  "type": "SERVICE | ACTIVITY (obrigatório)",
  "studFarmId": "UUID (opcional)",
  "userId": "UUID (obrigatório)",
  "description": "string (obrigatório, max 1000)",
  "animals": [
    {
      "animalId": "UUID (obrigatório)",
      "appointmentType": "string (obrigatório)"
    }
  ]
}
```

#### GET `/monthly` - Agendamentos Mensais
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `month` | number | Sim | Mês (1-12) |
| `year` | number | Sim | Ano (4 dígitos) |
| `userId` | UUID | Não | Filtrar por usuário |

#### GET `/daily` - Agendamentos Diários
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `day` | date | Sim | Data do dia |
| `userId` | UUID | Não | Filtrar por usuário |

#### GET `/fetch` - Listar Agendamentos
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `query` | string | Não | Termo de busca |
| `animalId` | UUID | Não | Filtrar por animal |

---

### Appointment Animal
Rota base: `/appointment-animal`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `PUT` | `/:id` | Atualizar agendamento animal | Requer token |
| `PUT` | `/details/:id` | Atualizar detalhes | Requer token |
| `GET` | `/` | Listar agendamentos animais | Requer token |
| `GET` | `/details/:id` | Obter detalhes | Requer token |

#### PUT `/:id` - Atualizar
**Body:**
```json
{
  "appointmentType": "string (opcional)",
  "status": "PENDING | IN_PROGRESS | FINISHED (opcional)"
}
```

#### GET `/` - Listar
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `userId` | string | Sim | ID do usuário |
| `state` | string | Não | Estado |
| `gender` | string | Não | Gênero |
| `breed` | string | Não | Raça |
| `city` | string | Não | Cidade |
| `query` | string | Não | Busca textual |
| `startDate` | string | Não | Data início |
| `endDate` | string | Não | Data fim |

---

## Financeiro

### Payment
Rota base: `/payment`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar pagamento | Requer token |
| `PUT` | `/:paymentId` | Atualizar pagamento | Requer token |
| `GET` | `/` | Listar pagamentos | Requer token |

#### POST `/` - Criar Pagamento
**Body:**
```json
{
  "name": "string (obrigatório)",
  "amount": "number (obrigatório)",
  "type": "INCOME | OUTCOME (obrigatório)",
  "firstDueDate": "date (obrigatório)",
  "isTotalValue": "boolean (obrigatório)",
  "quantity": "number (obrigatório)",
  "categoryId": "string (obrigatório)",
  "status": "PENDING | PAID (obrigatório)",
  "appointmentAnimalId": "string (opcional)",
  "animalId": "string (opcional)"
}
```

#### GET `/` - Listar Pagamentos
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `query` | string | Não | Termo de busca |
| `type` | INCOME/OUTCOME | Não | Tipo de pagamento |
| `animalId` | string | Não | Filtrar por animal |
| `clientId` | string | Não | Filtrar por cliente |
| `startDate` | date | Não | Data início |
| `endDate` | date | Não | Data fim |

---

### Transaction
Rota base: `/transaction`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar transação | Requer token |
| `POST` | `/pix/:transactionId` | Pagar via PIX | Requer token |
| `POST` | `/credit/new` | Pagar com novo cartão | Requer token |
| `POST` | `/credit/existing` | Pagar com cartão existente | Requer token |
| `PUT` | `/:transactionId` | Atualizar transação | Requer token |
| `GET` | `/` | Listar transações | Requer token |
| `GET` | `/statistics` | Obter estatísticas | Requer token |

#### POST `/` - Criar Transação
**Body:**
```json
{
  "name": "string (obrigatório)",
  "value": "number (obrigatório)",
  "type": "INCOME | OUTCOME (obrigatório)",
  "bankAccountId": "string (opcional)",
  "categoryId": "string (opcional)",
  "paymentId": "string (opcional)",
  "paymentDate": "date (opcional)",
  "dueDate": "date (opcional)",
  "status": "string (opcional)"
}
```

#### GET `/` - Listar Transações
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `bankAccountId` | string | Não | Conta bancária |
| `categoryId` | string | Não | Categoria |
| `paymentId` | string | Não | Pagamento |
| `type` | INCOME/OUTCOME | Não | Tipo |

#### GET `/statistics` - Estatísticas
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `startDate` | date | Não | Data início |
| `endDate` | date | Não | Data fim |
| `animalId` | string | Não | ID do animal |

---

### Transaction Category
Rota base: `/transaction-category`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar categoria | Requer token |
| `PUT` | `/:categoryId` | Atualizar categoria | Requer token |
| `GET` | `/` | Listar categorias | Requer token |
| `GET` | `/with-value` | Listar com valores | Requer token |

#### POST `/` - Criar Categoria
**Body:**
```json
{
  "name": "string (obrigatório)",
  "type": "string (obrigatório)"
}
```

#### GET `/with-value` - Listar com Valores
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `startDate` | date | Não | Data início |
| `endDate` | date | Não | Data fim |

---

### Bank Account
Rota base: `/bank-account`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar conta bancária | Requer token |
| `PUT` | `/:accountId` | Atualizar conta | Requer token |
| `GET` | `/` | Listar contas | Requer token |
| `GET` | `/balance` | Obter saldo total | Requer token |

#### POST `/` - Criar Conta
**Body:**
```json
{
  "name": "string (obrigatório)",
  "initialBalance": "number (obrigatório)"
}
```

---

### Credit Card
Rota base: `/credit-card`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `GET` | `/` | Listar cartões | Requer token |

---

## Estoque

### Product
Rota base: `/product`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar produto | Requer token |
| `PUT` | `/:productId` | Atualizar produto | Requer token |
| `DELETE` | `/:productId` | Deletar produto | Requer token |
| `GET` | `/` | Listar produtos | Requer token |

#### POST `/` - Criar Produto
**Body:**
```json
{
  "name": "string (obrigatório)",
  "categoryId": "string (obrigatório)",
  "unity": "string (obrigatório)",
  "minimumStock": "number (obrigatório)",
  "maximumStock": "number (obrigatório)",
  "observation": "string (opcional)",
  "tags": "array (opcional)"
}
```

---

### Product Category
Rota base: `/product-category`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar categoria | Requer token |
| `PUT` | `/:productCategoryId` | Atualizar categoria | Requer token |
| `DELETE` | `/:productCategoryId` | Deletar categoria | Requer token |
| `GET` | `/` | Listar categorias | Requer token |

#### POST `/` - Criar Categoria
**Body:**
```json
{
  "name": "string (obrigatório)",
  "color": "string (opcional)"
}
```

---

### Product Stock
Rota base: `/product-stock`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar entrada de estoque | Requer token |

#### POST `/` - Criar Entrada
**Body:**
```json
{
  "productId": "string (obrigatório)",
  "quantity": "number (obrigatório)",
  "date": "date (obrigatório)",
  "unitValue": "number (opcional)",
  "totalValue": "number (opcional)"
}
```

---

### Product Usage
Rota base: `/product-usage`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/usage` | Registrar uso de produto | Requer token |
| `GET` | `/appointment/:appointmentAnimalId` | Listar usos por agendamento | Requer token |

#### POST `/usage` - Registrar Uso
**Body:**
```json
{
  "productId": "string (obrigatório)",
  "quantity": "number (obrigatório)",
  "appointmentAnimalId": "string (opcional)"
}
```

---

### Field Stock
Rota base: `/field-stock`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar estoque de campo | Requer token |
| `PUT` | `/:fieldStockId` | Retornar ao estoque | Requer token |
| `GET` | `/` | Listar estoque de campo | Requer token |

#### POST `/` - Criar Estoque de Campo
**Body:**
```json
{
  "productId": "string (obrigatório)",
  "quantity": "number (obrigatório)"
}
```

#### GET `/` - Listar
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `query` | string | Não | Termo de busca |

---

### Stock Statistics
Rota base: `/stock-statistics`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `GET` | `/` | Obter estatísticas de estoque | Requer token |

---

## CRM

### Board
Rota base: `/board`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar quadro | Requer token |
| `PUT` | `/:boardId` | Atualizar quadro | Requer token |
| `DELETE` | `/:boardId` | Deletar quadro | Requer token |
| `GET` | `/` | Listar quadros com leads | Requer token |

#### POST `/` - Criar Quadro
**Body:**
```json
{
  "name": "string (obrigatório)",
  "color": "string (obrigatório)",
  "position": "number (obrigatório)",
  "isLast": "boolean (opcional)",
  "isLost": "boolean (opcional)"
}
```

#### GET `/` - Listar Quadros
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `query` | string | Não | Termo de busca |
| `startDate` | date | Não | Data início |
| `endDate` | date | Não | Data fim |

---

### Lead
Rota base: `/lead`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar lead | Requer token |
| `PUT` | `/:leadId` | Atualizar lead | Requer token |
| `DELETE` | `/:leadId` | Deletar lead | Requer token |
| `GET` | `/` | Listar leads | Requer token |
| `GET` | `/board/:boardId` | Listar leads por quadro | Requer token |

#### POST `/` - Criar Lead
**Body:**
```json
{
  "name": "string (obrigatório)",
  "phone": "string (obrigatório)",
  "boardId": "string (obrigatório)",
  "city": "string (opcional)",
  "state": "string (opcional)",
  "animalQuantity": "number (opcional)",
  "category": "string (opcional)",
  "procedure": "string (opcional)"
}
```

#### GET `/` - Listar Leads
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `query` | string | Não | Termo de busca |
| `filter` | string | Não | Filtro |
| `startDate` | date | Não | Data início |
| `endDate` | date | Não | Data fim |

---

## Outros

### Stud Farm (Haras)
Rota base: `/stud-farm`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar haras | Requer token |
| `PUT` | `/:id` | Atualizar haras | Requer token |
| `GET` | `/` | Listar haras | Requer token |

#### POST `/` - Criar Haras
**Body:**
```json
{
  "name": "string (obrigatório)",
  "address": "string (opcional)",
  "city": "string (opcional)",
  "state": "string (opcional)",
  "location": "string (opcional)"
}
```

#### GET `/` - Listar Haras
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |
| `query` | string | Não | Termo de busca |
| `city` | string | Não | Cidade |
| `state` | string | Não | Estado |

---

### Tag
Rota base: `/tag`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Criar tag | Requer token |
| `PUT` | `/:tagId` | Atualizar tag | Requer token |
| `DELETE` | `/:tagId` | Deletar tag | Requer token |
| `GET` | `/` | Listar tags | Requer token |

#### POST `/` - Criar Tag
**Body:**
```json
{
  "name": "string (obrigatório)",
  "color": "string (obrigatório)"
}
```

---

### File
Rota base: `/file`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/` | Upload de arquivo | Pública |

#### POST `/` - Upload
**Content-Type:** `multipart/form-data`

**Form Data:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `file` | File | Arquivo (máx 200MB) |

**Resposta:**
```json
{
  "url": "string",
  "fullUrl": "string"
}
```

---

### Note
Rota base: `/note`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/:appointmentId` | Criar nota | Requer token |
| `PUT` | `/:id` | Atualizar nota | Requer token |
| `DELETE` | `/:id` | Deletar nota | Requer token |
| `GET` | `/` | Listar notas | Requer token |

#### POST `/:appointmentId` - Criar Nota
**Body:**
```json
{
  "name": "string (obrigatório)",
  "description": "string (obrigatório)",
  "date": "date (obrigatório)"
}
```

#### GET `/` - Listar Notas
**Query Parameters:**
| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | Sim | Página |

---

### Signature
Rota base: `/signature`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `POST` | `/pix/:planId` | Assinar com PIX | Requer token |
| `POST` | `/credit/new` | Assinar com novo cartão | Requer token |
| `POST` | `/credit/existing` | Assinar com cartão existente | Requer token |
| `POST` | `/webhook` | Webhook de pagamento | Pública |
| `GET` | `/validation` | Validar assinatura ativa | Requer token |
| `PUT` | `/cancel/:signatureId` | Cancelar assinatura | Requer token |
| `PUT` | `/refound/:signatureId` | Reembolsar assinatura | Requer token |

---

### Signature Plan
Rota base: `/signature-plan`

| Método | Rota | Descrição | Autenticação |
|--------|------|-----------|--------------|
| `GET` | `/` | Listar planos disponíveis | Pública |

---

## Observações Gerais

### Autenticação
- Rotas marcadas como "Requer token" necessitam do header `Authorization: Bearer <token>`
- Rotas marcadas como "Pública" não requerem autenticação

### Paginação
- A maioria das rotas de listagem utiliza paginação com o parâmetro `page`
- A resposta inclui o campo `pages` indicando o número total de páginas

### IDs
- Todos os IDs são UUIDs v4
- IDs são passados como parâmetros de rota (`:id`) ou query parameters

### Status Codes
- `200`: Sucesso
- `201`: Criado com sucesso
- `400`: Erro de validação
- `401`: Não autorizado
- `404`: Recurso não encontrado
- `500`: Erro interno do servidor

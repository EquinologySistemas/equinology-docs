# API - Ortopedia (Orthopedic)

Documentação das rotas de atendimentos ortopédicos.

---

## 📋 Índice

- [Orthopedic Blockage](#orthopedic-blockage)
- [Orthopedic Extra](#orthopedic-extra)
- [Orthopedic Info](#orthopedic-info)
- [Orthopedic Prescription](#orthopedic-prescription)
- [Orthopedic Service](#orthopedic-service)
- [Orthopedic Test](#orthopedic-test)

---

## Orthopedic Blockage

**Descrição:** Bloqueio anestésico ortopédico.

### POST - Criar

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal |
| `userId` | string (UUID) | ✅ Sim | ID do usuário |
| `observation` | string | ✅ Sim | Observações |
| `fileUrl` | string | ❌ Não | URL do arquivo |

### PUT - Editar

Todos os campos são opcionais, incluindo `companyId`.

### GET - Fetch

| Parâmetro | Tipo | Obrigatório |
|-----------|------|-------------|
| `page` | number | ✅ Sim |
| `animalId` | string | ✅ Sim |
| `appointmentId` | string | ❌ Não |

---

## Orthopedic Extra

**Descrição:** Procedimentos extras ortopédicos.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string (UUID) | ✅ Sim |
| `userId` | string (UUID) | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

### PUT - Editar

Todos os campos são opcionais.

### GET - Fetch

| Parâmetro | Tipo | Obrigatório |
|-----------|------|-------------|
| `page` | number | ✅ Sim |
| `animalId` | string | ✅ Sim |
| `appointmentId` | string | ❌ Não |

---

## Orthopedic Info

**Descrição:** Informações ortopédicas gerais.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string (UUID) | ✅ Sim |
| `userId` | string (UUID) | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

### PUT - Editar

Todos os campos são opcionais.

### GET - Fetch

| Parâmetro | Tipo | Obrigatório |
|-----------|------|-------------|
| `page` | number | ✅ Sim |
| `animalId` | string | ✅ Sim |
| `appointmentId` | string | ❌ Não |

---

## Orthopedic Prescription

**Descrição:** Prescrição ortopédica.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string (UUID) | ✅ Sim |
| `userId` | string (UUID) | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

### PUT - Editar

Todos os campos são opcionais.

### GET - Fetch

| Parâmetro | Tipo | Obrigatório |
|-----------|------|-------------|
| `page` | number | ✅ Sim |
| `animalId` | string | ✅ Sim |
| `appointmentId` | string | ❌ Não |

---

## Orthopedic Service

**Descrição:** Atendimento ortopédico completo.

### POST - Criar

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal |
| `userId` | string (UUID) | ✅ Sim | ID do usuário |
| `problem` | string | ✅ Sim | Problema relatado |
| `inspection` | string | ✅ Sim | Resultados da inspeção |
| `neck` | string | ✅ Sim | Condição do pescoço |
| `back` | string | ✅ Sim | Condição das costas |
| `rump` | string | ✅ Sim | Condição da garupa |
| `members` | string | ✅ Sim | Condição dos membros |
| `hoof` | string | ✅ Sim | Condição dos cascos |
| `sensibility` | boolean | ✅ Sim | Sensibilidade ao exame |
| `observation` | string | ✅ Sim | Observações |
| `fileUrl` | string | ❌ Não | URL do arquivo |

### PUT - Editar

Todos os campos são opcionais.

### GET - Fetch

| Parâmetro | Tipo | Obrigatório |
|-----------|------|-------------|
| `page` | number | ✅ Sim |
| `animalId` | string | ✅ Sim |
| `appointmentId` | string | ❌ Não |

---

## Orthopedic Test

**Descrição:** Exame ortopédico.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string (UUID) | ✅ Sim |
| `userId` | string (UUID) | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

### PUT - Editar

Todos os campos são opcionais.

### GET - Fetch

| Parâmetro | Tipo | Obrigatório |
|-----------|------|-------------|
| `page` | number | ✅ Sim |
| `animalId` | string | ✅ Sim |
| `appointmentId` | string | ❌ Não |

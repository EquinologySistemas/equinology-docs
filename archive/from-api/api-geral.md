> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# API - Geral (General)

Documentação das rotas de atendimentos veterinários gerais.

---

## 📋 Índice

- [General Info (Informações Gerais)](#general-info)
- [General Prescription (Prescrição Geral)](#general-prescription)
- [General Service (Atendimento Geral)](#general-service)
- [General Test (Exame Clínico Geral)](#general-test)

---

## General Info

**Descrição:** Observações gerais sobre o atendimento.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `observation` | string | ✅ Sim | Observações adicionais | `"Observações gerais"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `animalId` | string | ❌ Não | ID do animal |
| `companyId` | string | ❌ Não | ID da empresa |
| `userId` | string | ❌ Não | ID do usuário |
| `observation` | string | ❌ Não | Observações adicionais |
| `fileUrl` | string | ❌ Não | URL do arquivo |

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## General Prescription

**Descrição:** Prescrições médicas para o animal.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `observation` | string | ✅ Sim | Observações da prescrição | `"Observações da prescrição"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `animalId` | string | ❌ Não | ID do animal |
| `companyId` | string | ❌ Não | ID da empresa |
| `userId` | string | ❌ Não | ID do usuário |
| `observation` | string | ❌ Não | Observações da prescrição |
| `fileUrl` | string | ❌ Não | URL do arquivo |

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## General Service

**Descrição:** Registro de atendimento geral com descrição do problema.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `problem` | string | ✅ Sim | Problema relatado no atendimento | `"Descrição do problema"` |

### PUT - Editar (Edit)

| Campo | Tipo | Obrigatório | Descrição |
|-------|------|-------------|-----------|
| `animalId` | string | ❌ Não | ID do animal |
| `companyId` | string | ❌ Não | ID da empresa |
| `userId` | string | ❌ Não | ID do usuário |
| `problem` | string | ❌ Não | Problema relatado |

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## General Test

**Descrição:** Exame clínico completo do animal.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `temperature` | string | ✅ Sim | Temperatura do animal | `"38.5"` |
| `heartRate` | string | ✅ Sim | Frequência cardíaca | `"80"` |
| `breathRate` | string | ✅ Sim | Frequência respiratória | `"20"` |
| `mucous` | string | ✅ Sim | Condição da mucosa | `"rosada"` |
| `tpc` | string | ✅ Sim | Tempo de preenchimento capilar | `"2s"` |
| `weight` | string | ✅ Sim | Peso do animal | `"450kg"` |
| `attitude` | string | ✅ Sim | Atitude do animal | `"alerta"` |
| `lymphNodes` | string | ✅ Sim | Linfonodos | `"normais"` |
| `nose` | string | ✅ Sim | Condição nasal | `"limpo"` |
| `cough` | string | ✅ Sim | Presença de tosse | `"ausente"` |
| `pulmonary` | string | ✅ Sim | Ausculta pulmonar | `"normal"` |
| `pulse` | string | ✅ Sim | Pulso | `"normal"` |
| `intestine` | string | ✅ Sim | Condição intestinal | `"normal"` |
| `feces` | string | ✅ Sim | Condição das fezes | `"normal"` |
| `urine` | string | ✅ Sim | Condição da urina | `"normal"` |
| `observation` | string | ✅ Sim | Observações gerais | `"Observações gerais"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

Todos os campos acima são opcionais na edição, incluindo `companyId`.

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

# API - Odontologia (Dentistry)

Documentação das rotas de atendimentos odontológicos.

---

## 📋 Índice

- [Dentistry Assessment (Avaliação Periodontal)](#dentistry-assessment)
- [Dentistry Exam (Exame Clínico)](#dentistry-exam)
- [Dentistry Odontogram (Odontograma)](#dentistry-odontogram)
- [Dentistry Oral (Exame Oral)](#dentistry-oral)
- [Dentistry Report (Relatório)](#dentistry-report)
- [Dentistry Sedation (Sedação)](#dentistry-sedation)

---

## Dentistry Assessment

**Descrição:** Avaliação periodontal completa do animal.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `localization` | string | ✅ Sim | Localização do dente | `"Localização"` |
| `mobility` | string | ✅ Sim | Mobilidade dentária | `"normal"` |
| `cement` | string | ✅ Sim | Cemento dental | `"ok"` |
| `gums` | string | ✅ Sim | Condição das gengivas | `"saudável"` |
| `bag` | string | ✅ Sim | Bolsa periodontal | `"sem bolsa"` |
| `xRay` | boolean | ✅ Sim | Indica se possui raio-x | `true` |
| `stage` | string | ✅ Sim | Estágio da doença periodontal | `"I"` |
| `observation` | string | ✅ Sim | Observações adicionais | `"Observações do exame"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

Todos os campos são opcionais na edição, incluindo `companyId`.

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## Dentistry Exam

**Descrição:** Exame clínico odontológico.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `mucous` | string | ✅ Sim | Condição da mucosa | `"rosada"` |
| `intestinal` | string | ✅ Sim | Função intestinal | `"normal"` |
| `heartRate` | string | ✅ Sim | Frequência cardíaca | `"80 bpm"` |
| `breathRate` | string | ✅ Sim | Frequência respiratória | `"20 rpm"` |
| `weight` | string | ✅ Sim | Peso do animal | `"450kg"` |
| `body` | string | ✅ Sim | Condição corporal | `"5/5"` |
| `historic` | string | ✅ Sim | Histórico do animal | `"Histórico clínico"` |
| `observation` | string | ✅ Sim | Observações do exame | `"Observações adicionais"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

Todos os campos são opcionais na edição, incluindo `companyId`.

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## Dentistry Odontogram

**Descrição:** Registro do odontograma do animal.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `observation` | string | ✅ Sim | Observações do odontograma | `"Observações adicionais"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `companyId` | string | ❌ Não |
| `userId` | string | ❌ Não |
| `observation` | string | ❌ Não |
| `fileUrl` | string | ❌ Não |

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## Dentistry Oral

**Descrição:** Exame oral completo.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `mucous` | string | ✅ Sim | Condição da mucosa | `"rosada"` |
| `vestibule` | string | ✅ Sim | Vestíbulo oral | `"normal"` |
| `tongue` | string | ✅ Sim | Condição da língua | `"normal"` |
| `incisors` | string | ✅ Sim | Incisivos | `"normais"` |
| `canines` | string | ✅ Sim | Caninos | `"normais"` |
| `wolf` | string | ✅ Sim | Dente lobo (wolf tooth) | `"presente"` |
| `molars` | string | ✅ Sim | Molares | `"normais"` |
| `diseases` | string | ✅ Sim | Doenças orais | `"nenhuma"` |
| `observation` | string | ✅ Sim | Observações adicionais | `"Observações"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

Todos os campos são opcionais na edição, incluindo `companyId`.

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## Dentistry Report

**Descrição:** Relatório odontológico do atendimento.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `observation` | string | ✅ Sim | Observações do relatório | `"Observações adicionais"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ❌ Não |
| `companyId` | string | ❌ Não |
| `userId` | string | ❌ Não |
| `observation` | string | ❌ Não |
| `fileUrl` | string | ❌ Não |

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

---

## Dentistry Sedation

**Descrição:** Registro de sedação odontológica.

### POST - Criar (Create)

| Campo | Tipo | Obrigatório | Descrição | Exemplo |
|-------|------|-------------|-----------|---------|
| `animalId` | string (UUID) | ✅ Sim | ID do animal | `"uuid..."` |
| `userId` | string (UUID) | ✅ Sim | ID do usuário | `"uuid..."` |
| `observation` | string | ✅ Sim | Observações sobre a sedação | `"Observações sobre a sedação"` |
| `fileUrl` | string | ❌ Não | URL do arquivo | `"https://exemplo.com/arquivo.jpg"` |

### PUT - Editar (Edit)

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ❌ Não |
| `companyId` | string | ❌ Não |
| `userId` | string | ❌ Não |
| `observation` | string | ❌ Não |
| `fileUrl` | string | ❌ Não |

### GET - Buscar (Fetch)

| Parâmetro | Tipo | Obrigatório | Descrição |
|-----------|------|-------------|-----------|
| `page` | number | ✅ Sim | Página de resultados |
| `animalId` | string | ✅ Sim | ID do animal para filtro |
| `appointmentId` | string | ❌ Não | ID do agendamento |

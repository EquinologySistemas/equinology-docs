> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# Documentação de Integração API – Equinology App V2

Este documento descreve **todas as chamadas de API** que precisam ser implementadas no **equinology-app-v2**, por tela e por fluxo. A referência é o **app antigo (equinollogy-app)** e a **equinology-api** (NestJS).

---

## Índice

1. [Visão geral](#1-visão-geral)
2. [Autenticação e sessão](#2-autenticação-e-sessão)
3. [Telas (auth)](#3-telas-auth)
4. [Telas (tabs)](#4-telas-tabs)
5. [Telas do animal](#5-telas-do-animal)
6. [Sheets / Modais](#6-sheets--modais)
7. [Resumo de endpoints por recurso](#7-resumo-de-endpoints-por-recurso)

---

## 1. Visão geral

| Item | Descrição |
|------|-----------|
| **App novo** | `equinology-app-v2` (Expo/React Native) – atualmente usa apenas dados **mock** em `@/data/mock` e `SessionContext`. |
| **App antigo** | `equinollogy-app` – utiliza **Axios** via `ApiContext` e `lib/api-routes.ts`. |
| **API** | `equinology-api` (NestJS). Base URL: configurável via `EXPO_PUBLIC_API_URL`. |
| **Autenticação** | Token Bearer no header `Authorization`. O app é **cliente** (client), portanto usa rotas `/client/*` para login, perfil e recuperação de senha. |

---

## 2. Autenticação e sessão

- **Após login:** guardar `accessToken` e, se necessário, chamar **GET `/client/profile`** para obter os dados do usuário (nome, email, telefone, CPF, código, foto).
- **Ao reabrir o app:** validar token (ex.: GET `/client/profile`) e, se válido, preencher o `SessionContext` com o usuário retornado.
- **Logout:** apenas limpar token e estado local; não há endpoint específico.

---

## 3. Telas (auth)

### 3.1 Login — `(auth)/login.tsx`

| Ação | Método | Endpoint | Body / Params | Observação |
|------|--------|----------|---------------|------------|
| Login (email/senha) | POST | `/client/auth` | `{ email, password }` | Retorno: `accessToken`. Em seguida chamar GET `/client/profile` para preencher usuário. |
| Primeiro acesso (solicitar código) | POST | `/client/password-code` | `{ email }` | Envia código por e-mail. |
| Validar código (primeiro acesso) | GET | `/client/password-code/:code` | — | Verifica se o código é válido. |
| Redefinir senha com código | PUT | `/client/password-code` | Body com código e nova senha | Conforme contrato da API. |

**Implementar:** substituir mock de login por `POST /client/auth` + `GET /client/profile` e fluxo de código de recuperação usando os endpoints acima.

---

### 3.2 Cadastro — `(auth)/signup.tsx`

| Ação | Método | Endpoint | Body | Observação |
|------|--------|----------|------|------------|
| Registrar cliente | POST | `/client/register` | `{ name, email, phone, cpf, password, ... }` | Campos conforme DTO da API. Retorno tipicamente inclui token. |

**Implementar:** envio do formulário para `POST /client/register` e tratamento da resposta (token + redirecionamento).

---

### 3.3 Recuperar senha — `(auth)/forgot-password.tsx`

| Ação | Método | Endpoint | Body / Params | Observação |
|------|--------|----------|---------------|------------|
| Solicitar código | POST | `/client/password-code` | `{ email }` | |
| Validar código | GET | `/client/password-code/:code` | — | |
| Redefinir senha | PUT | `/client/password-code` | Código + nova senha | Conforme DTO da API. |

**Implementar:** fluxo completo (solicitar → validar código → nova senha) usando esses endpoints.

---

## 4. Telas (tabs)

### 4.1 Home — `(tabs)/index.tsx`

| Dados exibidos | Método | Endpoint | Query / Params | Observação |
|----------------|--------|----------|----------------|------------|
| Lista de animais (ex.: primeiros 6) | GET | `/animal` | `page=1`, `clientId` (ou implícito pelo token) | Filtrar por cliente quando a API usar token de cliente. |
| Faturas/pagamentos recentes | GET | `/client-payment` | `page=1`, etc. | A API já filtra por `clientId` pelo token. |
| Totais (pendente, total animais) | — | Derivado dos listados | — | Calcular no cliente ou usar endpoint de estatísticas se existir. |

**Implementar:**  
- Trocar `mockAnimals` e `mockPayments` por chamadas a `/animal` e `/client-payment`.  
- Manter navegação para “Ver todos” (animais → `/(tabs)/animals`, faturas → `/(tabs)/finances`) e para detalhe do animal e sheet de fatura.

---

### 4.2 Animais — `(tabs)/animals.tsx`

| Ação / Dados | Método | Endpoint | Query / Params | Observação |
|--------------|--------|----------|----------------|------------|
| Listar animais | GET | `/animal` | `page`, `query` (busca), `gender` (filtro) | `clientId` pode vir do token. |
| Abrir sheet de cadastro | — | — | — | Ver [AnimalRegistrationSheet](#61-animalregistrationsheet). |

**Implementar:** listagem paginada e filtros (busca, gênero) via query params; substituir `mockAnimals`.

---

### 4.3 Agenda — `(tabs)/agenda.tsx`

| Dados exibidos | Método | Endpoint | Query / Params | Observação |
|----------------|--------|----------|----------------|------------|
| Lista de compromissos por mês/dia | GET | `/appointment/fetch` | `page`, `clientId` (se suportado) | No app antigo: `clientId` na query. |
| Alternativa (agenda mensal) | GET | `/appointment/monthly` | `month`, `year`, `userId`/`clientId` | Se a API expuser por cliente. |
| Alternativa (agenda diária) | GET | `/appointment/daily` | `day`, `userId`/`clientId` | Idem. |

**Implementar:** substituir `mockAppointments` por uma das rotas acima, garantindo filtro por cliente (token ou `clientId`). Exibir eventos por data selecionada no calendário.

---

### 4.4 Finanças — `(tabs)/finances.tsx`

| Dados / Ação | Método | Endpoint | Query / Params | Observação |
|--------------|--------|----------|----------------|------------|
| Listar faturas/pagamentos | GET | `/client-payment` | `page`, `startDate`, `endDate` | Filtro por cliente via token. |
| Filtros (Todas / Pendentes / Pagas) | — | Mesmo GET | Filtrar no cliente por status ou usar query se a API suportar | Ex.: filtrar por status da transação. |
| Abrir sheet de pagamento | — | — | — | Ver [InvoicePaymentSheet](#62-invoicepaymentsheet). |

**Implementar:** listagem paginada a partir de `/client-payment`; filtros no cliente ou na API conforme disponível.

---

### 4.5 Perfil — `(tabs)/profile.tsx`

| Ação / Dados | Método | Endpoint | Body / Params | Observação |
|--------------|--------|----------|---------------|------------|
| Dados do perfil | GET | `/client/profile` | — | Header Bearer. Usado ao iniciar sessão. |
| Atualizar perfil (nome, telefone) | PUT | `/client/:clientId` | `{ name, phone, ... }` | |
| Lista de animais (Fichas / código) | GET | `/animal` | `clientId` (token) | Para exibir códigos e “Fichas de Animais”. |
| Logout | — | — | Limpar token e estado | Sem chamada à API. |

**Implementar:**  
- Carregar perfil com GET `/client/profile` (ou manter do login).  
- Salvar alterações com PUT `/client/:clientId`.  
- Listar animais do cliente para a seção de fichas/códigos.

---

## 5. Telas do animal

Todas as telas abaixo recebem o **id do animal** (ex.: `useLocalSearchParams<{ id: string }>()`). Esse `id` deve ser usado nas chamadas como `animalId` ou na rota conforme indicado.

### 5.1 Detalhe do animal — `(animal)/[id].tsx`

| Dados / Ação | Método | Endpoint | Observação |
|--------------|--------|----------|------------|
| Detalhe do animal | GET | `/animal/:id` ou `/animal/:code` | API usa `/:code`; verificar se `id` do app é código ou UUID. |
| Copiar código de compartilhamento | — | — | Apenas UI; dado vem do GET acima. |

**Implementar:** substituir `getAnimalById(id)` (mock) por GET `/animal/:id` (ou `/animal/:code`). Garantir que fotos, haras, raça, pelagem, nascimento, gênero e `shareCode` venham na resposta.

---

### 5.2 Pagamentos do animal — `(animal)/payments.tsx`

| Dados | Método | Endpoint | Query | Observação |
|-------|--------|----------|-------|------------|
| Faturas/pagamentos do animal | GET | `/client-payment` ou `/payment` | `animalId=<id>` | Conforme filtro disponível na API para cliente. |

**Implementar:** listar pagamentos filtrando por `animalId` (e cliente pelo token). Substituir mock.

---

### 5.3 Anotações do animal — `(animal)/notes.tsx`

| Ação | Método | Endpoint | Body / Params | Observação |
|------|--------|----------|---------------|------------|
| Listar notas | GET | `/animal-note/animal/:animalId` | — | |
| Criar nota | POST | `/animal-note` | `{ content, animalId }` | |
| Atualizar nota | PUT | `/animal-note/:animalNoteId` | `{ content }` | Se a tela tiver edição. |
| Excluir nota | DELETE | `/animal-note/:animalNoteId` | — | Se a tela tiver exclusão. |

**Implementar:** substituir `mockNotes` e estado local por essas chamadas; manter `animalId` da rota.

---

### 5.4 Manejo sanitário (Health) — `(animal)/health/index.tsx`

| Dados | Método | Endpoint | Observação |
|-------|--------|----------|------------|
| Contagens (vacinas, vermífugos, exames, ferrageamento) | GET | Cada recurso por animal (ver abaixo) | Ou usar listagens e contar no cliente. |
| Lista de protocolos (haras) | GET | `/sanitary-protocol` | Query: `page`, `studFarmId` (e.g. do animal). |

Para as sub-telas abaixo, usar o **animalId** da rota.

---

### 5.5 Vacinas — `(animal)/health/vaccines.tsx`

| Ação | Método | Endpoint | Query / Body | Observação |
|------|--------|----------|--------------|------------|
| Listar | GET | `/vaccine/:animalId` | `page`, `query` | |
| Próximas (opcional) | GET | `/vaccine/soon/:animalId` | — | |
| Criar (sheet) | POST | `/vaccine` | `{ name, animalId, date, nextDate, description, location }` | Ver [HealthRecordSheet](#63-healthrecordsheet). |
| Atualizar (sheet) | PUT | `/vaccine/:id` | Body com campos alterados | |
| Excluir | DELETE | `/vaccine/:id` | — | Se a UI permitir. |

**Implementar:** substituir mock por GET/POST/PUT/DELETE conforme acima.

---

### 5.6 Vermífugos — `(animal)/health/dewormings.tsx`

| Ação | Método | Endpoint | Query / Body | Observação |
|------|--------|----------|--------------|------------|
| Listar | GET | `/deworming/:animalId` | `page`, `query` | |
| Próximas (opcional) | GET | `/deworming/soon/:animalId` | — | |
| Criar | POST | `/deworming` | `{ name, animalId, date, nextDate, description }` | |
| Atualizar | PUT | `/deworming/:id` | — | |
| Excluir | DELETE | `/deworming/:id` | — | |

**Implementar:** idem ao padrão do app antigo; substituir mock.

---

### 5.7 Exames — `(animal)/health/exams.tsx`

| Ação | Método | Endpoint | Query / Body | Observação |
|------|--------|----------|--------------|------------|
| Listar | GET | `/exam/:animalId` | `page`, `query` | |
| Criar | POST | `/exam` | `{ name, animalId, date, laboratory, result, resultFileUrl }` | |
| Atualizar | PUT | `/exam/:id` | — | |
| Excluir | DELETE | `/exam/:id` | — | |

**Implementar:** substituir mock por essas rotas.

---

### 5.8 Ferrageamento — `(animal)/health/shoeing.tsx`

| Ação | Método | Endpoint | Query / Body | Observação |
|------|--------|----------|--------------|------------|
| Listar | GET | `/shoeing/:animalId` | `page`, `query`, `type`, `startDate`, `endDate` | |
| Próximos (opcional) | GET | `/shoeing/soon/:animalId` | — | |
| Criar | POST | `/shoeing` | `{ type: TRIMMING \| SHOEING \| ORTHOPEDIC, animalId, date, nextDate, farrierName, description, photoUrl }` | |
| Atualizar | PUT | `/shoeing/:id` | — | |
| Excluir | DELETE | `/shoeing/:id` | — | |

**Implementar:** idem; tipos conforme enum da API.

---

### 5.9 Protocolos sanitários — `(animal)/health/protocols.tsx`

| Ação | Método | Endpoint | Query / Body | Observação |
|------|--------|----------|--------------|------------|
| Listar protocolos | GET | `/sanitary-protocol` | `page`, `studFarmId` (ex.: do animal) | |
| Detalhe de um protocolo | GET | `/sanitary-protocol/:protocolId` | — | Inclui itens. |
| Criar protocolo | POST | `/sanitary-protocol` | `{ name, description, studFarmId, targetCategory, items[] }` | |
| Atualizar protocolo | PUT | `/sanitary-protocol/:protocolId` | — | |
| Adicionar item | POST | `/sanitary-protocol/item` | `{ protocolId, name, type, periodDays, isRecurrent, observation }` | |
| Atualizar item | PUT | `/sanitary-protocol/item/:itemId` | — | |
| Excluir protocolo / item | DELETE | `/sanitary-protocol/:protocolId` ou `/sanitary-protocol/item/:itemId` | — | |

**Implementar:** listagem e detalhe; criar/editar se houver fluxo no app (ex.: sheet).

---

### 5.10 Atendimentos veterinários (Vet) — dashboard e sub-telas

O app V2 tem:  
- **Vet dashboard** — `(animal)/vet/index.tsx`  
- **Geral** — `(animal)/vet/general.tsx`  
- **Odontologia** — `(animal)/vet/dentistry.tsx`  
- **Ortopedia** — `(animal)/vet/orthopedics.tsx`  
- **Reprodução** — `(animal)/vet/reproduction.tsx` (placeholder “Em desenvolvimento”).

Todos os recursos abaixo aceitam **query** `page`, `animalId`, `appointmentId` (conforme documentação da API). Listar com `animalId` para exibir registros do animal.

#### Geral (general-info, prescription, service, test)

| Recurso | GET (listar) | POST (criar) | PUT (atualizar) | DELETE |
|---------|----------------|-------------|------------------|--------|
| Informação geral | GET `/general-info` | POST `/general-info/:appointmentId` | PUT `/general-info/:id` | DELETE `/general-info/:id` |
| Prescrição | GET `/general-prescription` | POST `/general-prescription/:appointmentId` | PUT `/general-prescription/:id` | DELETE `/general-prescription/:id` |
| Serviço | GET `/general-service` | POST `/general-service/:appointmentId` | PUT `/general-service/:id` | DELETE `/general-service/:id` |
| Teste | GET `/general-test` | POST `/general-test/:appointmentId` | PUT `/general-test/:id` | DELETE `/general-test/:id` |

**Implementar:** em `vet/general.tsx`, buscar por `animalId` (e opcionalmente `appointmentId`) e exibir por aba (info, prescrição, serviço, teste).

#### Ortopedia (orthopedic-*)

| Recurso | GET | POST | PUT | DELETE |
|---------|-----|------|-----|--------|
| Info, Prescription, Service, Test, Blockage, Extra | GET `/<recurso>` | POST `/<recurso>/:appointmentId` | PUT `/<recurso>/:id` | DELETE `/<recurso>/:id` |

Recursos: `orthopedic-info`, `orthopedic-prescription`, `orthopedic-service`, `orthopedic-test`, `orthopedic-blockage`, `orthopedic-extra`.

**Implementar:** em `vet/orthopedics.tsx`, listar todos com `animalId` e exibir (e criar/editar se houver formulário).

#### Odontologia (dentistry-*)

| Recurso | GET | POST | PUT | DELETE |
|---------|-----|------|-----|--------|
| Assessment, Exam, Odontogram, Oral, Report, Sedation | GET `/<recurso>` | POST `/<recurso>/:appointmentId` | PUT `/<recurso>/:id` | DELETE `/<recurso>/:id` |

**Implementar:** em `vet/dentistry.tsx`, listar por `animalId` (e `appointmentId` se necessário) e exibir por tipo.

#### Reprodução (reproduction-*)

Vários sub-recursos (breeding-initial, breeding-intermediate, donor-gyno, receptor-embryo, stallion-collection, etc.). Padrão: GET lista com `page`, `animalId`, `appointmentId`; POST `/:appointmentId`; PUT/DELETE `/:id`.

**Implementar:** em `vet/reproduction.tsx`, quando sair do placeholder, usar os endpoints de reprodução da API com `animalId` (e `appointmentId` quando aplicável).

---

## 6. Sheets / Modais

### 6.1 AnimalRegistrationSheet

| Ação / Dados | Método | Endpoint | Observação |
|--------------|--------|----------|------------|
| Listar haras (para select) | GET | `/stud-farm` | Query: `page`, `query`. No app cliente pode ser `/stud-farm/client` ou com filtro por cliente. |
| Criar haras (se tiver “novo haras”) | POST | `/stud-farm` | Body conforme API. |
| Registrar animal por código | POST | `/animal/register/:code` | Código compartilhado. |
| Criar animal | POST | `/animal` | `{ name, breed, gender, pureBlood, color, birthDate, studFarmId, clientId?, photoUrl? }` |
| Upload de foto | POST | `/file` | Multipart. Retorna URL para enviar em `photoUrl` do animal. |

**Implementar:**  
- Select de haras com GET `/stud-farm` (ou endpoint “meus haras” se existir).  
- Fluxo por código: POST `/animal/register/:code`.  
- Fluxo manual: POST `/animal` (e opcionalmente POST `/file` para foto).  
- Catálogos (raças, cores) podem permanecer fixos no app ou vir de API futura.

---

### 6.2 InvoicePaymentSheet

| Ação / Dados | Método | Endpoint | Observação |
|--------------|--------|----------|------------|
| Cartões salvos (para pagamento) | GET | `/credit-card` | Lista cartões do cliente. |
| Pagar com PIX | POST | `/transaction/pix/:transactionId` | Conforme API. |
| Pagar com novo cartão | POST | `/transaction/credit/new` | Body com dados do cartão e transação. |
| Pagar com cartão existente | POST | `/transaction/credit/existing` | Body com id do cartão e transação. |

**Implementar:** ao abrir o sheet com uma fatura/transação, usar os endpoints acima para registrar pagamento. Dados da fatura já vêm de `/client-payment`.

---

### 6.3 HealthRecordSheet

Usado para criar (e eventualmente editar) registro de **vacina**, **vermífugo**, **exame** ou **ferrageamento** (e possivelmente protocolo). O **animalId** deve vir do contexto (tela do animal).

| Tipo | Criar | Atualizar |
|------|-------|-----------|
| Vacina | POST `/vaccine` | PUT `/vaccine/:id` |
| Vermífugo | POST `/deworming` | PUT `/deworming/:id` |
| Exame | POST `/exam` | PUT `/exam/:id` |
| Ferrageamento | POST `/shoeing` | PUT `/shoeing/:id` |
| Protocolo | POST `/sanitary-protocol` (ou item) | PUT conforme recurso |

**Implementar:** ao salvar no sheet, enviar POST (ou PUT se for edição) para o recurso correto com `animalId` e campos do formulário.

---

## 7. Resumo de endpoints por recurso

| Recurso | GET | POST | PUT | DELETE |
|---------|-----|------|-----|--------|
| **Client (auth/perfil)** | `/client/profile` | `/client/auth`, `/client/register`, `/client/password-code` | `/client/:clientId`, `/client/password-code` | — |
| **Client password code** | `/client/password-code/:code` | — | — | — |
| **Animal** | `/animal`, `/animal/:id` ou `/:code` | `/animal`, `/animal/register/:code` | `/animal/:id` | — |
| **Animal note** | `/animal-note/animal/:animalId` | `/animal-note` | `/animal-note/:animalNoteId` | `/animal-note/:animalNoteId` |
| **Vaccine** | `/vaccine/:animalId`, `/vaccine/soon/:animalId` | `/vaccine` | `/vaccine/:id` | `/vaccine/:id` |
| **Deworming** | `/deworming/:animalId`, `/deworming/soon/:animalId` | `/deworming` | `/deworming/:id` | `/deworming/:id` |
| **Exam** | `/exam/:animalId` | `/exam` | `/exam/:id` | `/exam/:id` |
| **Shoeing** | `/shoeing/:animalId`, `/shoeing/soon/:animalId` | `/shoeing` | `/shoeing/:id` | `/shoeing/:id` |
| **Sanitary protocol** | `/sanitary-protocol`, `/sanitary-protocol/:protocolId` | `/sanitary-protocol`, `/sanitary-protocol/item` | `/sanitary-protocol/:protocolId`, `/sanitary-protocol/item/:itemId` | `/sanitary-protocol/:protocolId`, `/sanitary-protocol/item/:itemId` |
| **Stud farm** | `/stud-farm`, `/stud-farm/client` (se existir) | `/stud-farm` | `/stud-farm/:id` | — |
| **Appointment** | `/appointment/fetch`, `/appointment/monthly`, `/appointment/daily`, `/appointment/details/:id` | `/appointment` | `/appointment/:id` | `/appointment/:id` |
| **Client payment** | `/client-payment` | — | — | — |
| **Credit card** | `/credit-card` | — | — | — |
| **Transaction** | `/transaction` | `/transaction/pix/:transactionId`, `/transaction/credit/new`, `/transaction/credit/existing` | `/transaction/:transactionId` | — |
| **File** | — | `/file` (multipart) | — | — |
| **General / Orthopedic / Dentistry / Reproduction** | GET `/<recurso>` com `animalId`, `appointmentId` | POST `/<recurso>/:appointmentId` | PUT `/<recurso>/:id` | DELETE `/<recurso>/:id` |

---

## Checklist de implementação por tela

- [ ] **Auth:** Login, Signup, Forgot password – trocar mock por `/client/*` e `/client/password-code`.
- [ ] **Session:** Após login, GET `/client/profile`; ao reabrir app, validar token com `/client/profile`.
- [ ] **Home:** GET `/animal` e GET `/client-payment`; remover mock.
- [ ] **Animais:** GET `/animal` com paginação e filtros.
- [ ] **Agenda:** GET `/appointment/fetch` (ou monthly/daily) com clientId.
- [ ] **Finanças:** GET `/client-payment` com paginação e filtros.
- [ ] **Perfil:** GET `/client/profile`, PUT `/client/:clientId`, GET `/animal` para fichas.
- [ ] **Animal [id]:** GET `/animal/:id` (ou `/:code`).
- [ ] **Animal payments:** GET `/client-payment` com `animalId`.
- [ ] **Animal notes:** GET/POST/PUT/DELETE `/animal-note/*`.
- [ ] **Health (vacinas, vermífugos, exames, ferrageamento, protocolos):** GET/POST/PUT/DELETE nos recursos correspondentes.
- [ ] **Vet (geral, ortopedia, odontologia, reprodução):** GET (e POST/PUT quando houver formulário) nos recursos listados.
- [ ] **AnimalRegistrationSheet:** GET/POST `/stud-farm`, POST `/animal`, POST `/animal/register/:code`, POST `/file`.
- [ ] **InvoicePaymentSheet:** GET `/credit-card`, POST `/transaction/pix/:id`, POST `/transaction/credit/new`, POST `/transaction/credit/existing`.
- [ ] **HealthRecordSheet:** POST/PUT para vaccine, deworming, exam, shoeing (e sanitary-protocol se aplicável).

Para detalhes de body e query de cada endpoint, consultar **equinology-api/API_ROUTES.md** e os DTOs nos controllers da API.

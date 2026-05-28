# API - Reprodução (Reproduction)

Documentação das rotas de reprodução equina.

---

## 📋 Índice

### Cria (Breeding)
- [Birth (Nascimento)](#breeding-birth) - `/reproduction-breeding-birth`
- [Initial (Diagnóstico Inicial)](#breeding-initial) - `/reproduction-breeding-initial`
- [Intermediate (Exame Intermediário)](#breeding-intermediate) - `/reproduction-breeding-intermediate`
- [Post (Pós-Parto)](#breeding-post) - `/reproduction-breeding-post`
- [Pregnancy (Gestação)](#breeding-pregnancy) - `/reproduction-breedingPregnancy`
- [Vaccines (Vacinas)](#breeding-vaccines) - `/reproduction-breeding-vaccines`

### Doadora (Donor)
- [Embryo (Coleta de Embrião)](#donor-embryo) - `/reproduction-donor-embryo`
- [Gyno (Ginecológico)](#donor-gyno) - `/reproduction-donor-gyno`
- [Heat (Cio)](#donor-heat) - `/reproduction-donor-heat`
- [Insemination (Inseminação)](#donor-insemination) - `/reproduction-donor-insemination`
- [Ovulation (Ovulação)](#donor-ovulation) - `/reproduction-donor-ovulation`

### Receptora (Receptor)
- [Diagnosis (Diagnóstico)](#receptor-diagnosis) - `/reproduction-receptor-diagnosis`
- [Embryo (Transferência)](#receptor-embryo) - `/reproduction-receptor-embryo`
- [Final (Parto)](#receptor-final) - `/reproduction-receptor-final`
- [Gyno (Ginecológico)](#receptor-gyno) - `/reproduction-receptor-gyno`
- [Heat (Cio)](#receptor-heat) - `/reproduction-receptor-heat`
- [Hormones (Hormônios)](#receptor-hormones) - `/reproduction-receptor-hormones`
- [Inovulation (Inovulação)](#receptor-inovulation) - `/reproduction-receptor-inovulation`
- [Monitoring (Monitoramento)](#receptor-monitoring) - `/reproduction-receptor-monitoring`
- [Vaccines (Vacinas)](#receptor-vaccines) - `/reproduction-receptor-vaccines`

### Garanhão (Stallion)
- [Collection (Coleta)](#stallion-collection) - `/reproduction-stallion-collection`
- [Physical (Exame Físico)](#stallion-physical) - `/reproduction-stallion-physical`
- [Shipping (Envio)](#stallion-shipping) - `/reproduction-stallion-shipping`
- [Storage (Armazenamento)](#stallion-storage) - `/reproduction-stallion-storage`

---

# Cria (Breeding)

## Breeding Birth

**Endpoint:** `POST /reproduction-breeding-birth`

**Descrição:** Registro de nascimento.

### Enums

- **BirthType:** `Normal`, `Assistido`, `Cesárea`
- **BirthSituation:** `Vivo`, `Morto`, `Aborto`
- **BirthGender:** `Macho`, `Fêmea`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `type` | BirthType | ✅ Sim |
| `situation` | BirthSituation | ✅ Sim |
| `gender` | BirthGender | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Breeding Initial

**Endpoint:** `POST /reproduction-breeding-initial`

**Descrição:** Diagnóstico inicial de gestação.

### Enums

- **BreedingInitialResult:** `Positivo`, `Negativo`, `Gemelar`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `result` | BreedingInitialResult | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Breeding Intermediate

**Endpoint:** `POST /reproduction-breeding-intermediate`

**Descrição:** Exame intermediário de gestação.

### Enums

- **YesNo:** `Sim`, `Não`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `heartbeat` | YesNo | ✅ Sim |
| `compatible` | YesNo | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Breeding Post

**Endpoint:** `POST /reproduction-breeding-post`

**Descrição:** Avaliação pós-parto.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `foal` | string | ✅ Sim |
| `placenta` | string | ✅ Sim |
| `mare` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Breeding Pregnancy

**Endpoint:** `POST /reproduction-breedingPregnancy`

**Descrição:** Diagnóstico de gestação.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `ultrasound` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Breeding Vaccines

**Endpoint:** `POST /reproduction-breeding-vaccines`

**Descrição:** Vacinação durante a gestação.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `type` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

# Doadora (Donor)

## Donor Embryo

**Endpoint:** `POST /reproduction-donor-embryo`

**Descrição:** Coleta de embrião.

### Enums

- **EmbryoCollectionResult:** `Positivo`, `Negativo`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `time` | string (HH:mm) | ✅ Sim |
| `collection` | EmbryoCollectionResult | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Donor Gyno

**Endpoint:** `POST /reproduction-donor-gyno`

**Descrição:** Exame ginecológico da doadora.

### Enums

- **ParityEnum:** `Primípara`, `Multípara`
- **VulvaEnum:** `Ótima`, `Mediana`, `Ruim`
- **Vulva2Enum:** `Eficiente`, `Ineficiente`
- **YesNoEnum:** `Sim`, `Não`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `ultrasound` | string | ✅ Sim |
| `ultrasoundFile` | string | ❌ Não |
| `utero` | string | ✅ Sim |
| `bodyScore` | string | ✅ Sim |
| `parity` | ParityEnum | ✅ Sim |
| `vulva` | VulvaEnum | ✅ Sim |
| `angle` | string | ✅ Sim |
| `vulva2` | Vulva2Enum | ✅ Sim |
| `vulvoplastia` | YesNoEnum | ✅ Sim |
| `cervix` | string | ✅ Sim |
| `cyto` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Donor Heat

**Endpoint:** `POST /reproduction-donor-heat`

**Descrição:** Exame de cio da doadora.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `leftOvary` | string | ✅ Sim |
| `rightOvary` | string | ✅ Sim |
| `uterus` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Donor Insemination

**Endpoint:** `POST /reproduction-donor-insemination`

**Descrição:** Inseminação da doadora.

### Enums

- **SemenType:** `Congelado`, `Refrigerado`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `time` | string (HH:mm) | ✅ Sim |
| `semen` | SemenType | ✅ Sim |
| `stallionId` | string | ✅ Sim |
| `volume` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Donor Ovulation

**Endpoint:** `POST /reproduction-donor-ovulation`

**Descrição:** Indução de ovulação.

### Enums

- **AdministrationType:** `Intravenoso`, `Intramuscular`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `time` | string (HH:mm) | ✅ Sim |
| `hormones` | string | ✅ Sim |
| `dosage` | string | ✅ Sim |
| `administration` | AdministrationType | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

# Receptora (Receptor)

## Receptor Diagnosis

**Endpoint:** `POST /reproduction-receptor-diagnosis`

**Descrição:** Diagnóstico da receptora.

### Enums

- **DiagnosisResult:** `Positivo`, `Negativo`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `result` | DiagnosisResult | ✅ Sim |
| `heartRate` | string | ✅ Sim |
| `embryo` | string | ✅ Sim |
| `expectancyDate` | Date (ISO) | ✅ Sim |
| `expectancyConditional` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Embryo

**Endpoint:** `POST /reproduction-receptor-embryo`

**Descrição:** Transferência de embrião.

### Enums

- **ReceptorEmbryoResult:** `Positivo`, `Negativo`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | string (ISO) | ✅ Sim |
| `result` | ReceptorEmbryoResult | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Final

**Endpoint:** `POST /reproduction-receptor-final`

**Descrição:** Parto da receptora.

### Enums

- **DeliveryType:** `Normal`, `Assistido`, `Cesárea`
- **DeliverySituation:** `Vivo`, `Morto`, `Aborto`
- **GenderType:** `Macho`, `Fêmea`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `type` | DeliveryType | ✅ Sim |
| `situation` | DeliverySituation | ✅ Sim |
| `gender` | GenderType | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Gyno

**Endpoint:** `POST /reproduction-receptor-gyno`

**Descrição:** Exame ginecológico da receptora.

Utiliza os mesmos enums do Donor Gyno.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `ultrasound` | string | ✅ Sim |
| `ultrasoundFile` | string | ❌ Não |
| `utero` | string | ✅ Sim |
| `bodyScore` | string | ✅ Sim |
| `parity` | ParityEnum | ✅ Sim |
| `vulva` | VulvaEnum | ✅ Sim |
| `angle` | string | ✅ Sim |
| `vulva2` | Vulva2Enum | ✅ Sim |
| `vulvoplastia` | YesNoEnum | ✅ Sim |
| `cervix` | string | ✅ Sim |
| `cyto` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Heat

**Endpoint:** `POST /reproduction-receptor-heat`

**Descrição:** Exame de cio da receptora.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `leftOvary` | string | ✅ Sim |
| `rightOvary` | string | ✅ Sim |
| `uterus` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Hormones

**Endpoint:** `POST /reproduction-receptor-hormones`

**Descrição:** Administração hormonal.

### Enums

- **AdministrationEnum:** `Intravenoso`, `Intramuscular`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `time` | string (HH:mm) | ✅ Sim |
| `hormones` | string | ✅ Sim |
| `dosage` | string | ✅ Sim |
| `administration` | AdministrationEnum | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Inovulation

**Endpoint:** `POST /reproduction-receptor-inovulation`

**Descrição:** Inovulação da receptora.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `time` | string (HH:mm) | ✅ Sim |
| `embryo` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Monitoring

**Endpoint:** `POST /reproduction-receptor-monitoring`

**Descrição:** Monitoramento da receptora.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `ultrasound` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Receptor Vaccines

**Endpoint:** `POST /reproduction-receptor-vaccines`

**Descrição:** Vacinação da receptora.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `date` | Date (ISO) | ✅ Sim |
| `type` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

# Garanhão (Stallion)

## Stallion Collection

**Endpoint:** `POST /reproduction-stallion-collection`

**Descrição:** Coleta de sêmen.

### Enums

- **StallionCollectionState:** `Refrigerado`, `Congelado`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `state` | StallionCollectionState | ✅ Sim |
| `spermogramVolume` | string | ✅ Sim |
| `spermograMotility` | string | ✅ Sim |
| `vigor` | string | ✅ Sim |
| `totalConcentration` | string | ✅ Sim |
| `mlConcentration` | string | ✅ Sim |
| `mobileConcentration` | string | ✅ Sim |
| `amount` | string | ✅ Sim |
| `diluent` | string | ✅ Sim |
| `dilution` | string | ✅ Sim |
| `destination` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Stallion Physical

**Endpoint:** `POST /reproduction-stallion-physical`

**Descrição:** Exame físico do garanhão.

### Enums

- **StallionCollectionEnum:** `Feita`, `Não`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `inspection` | string | ✅ Sim |
| `behavior` | string | ✅ Sim |
| `ultrasound` | string | ✅ Sim |
| `collection` | StallionCollectionEnum | ✅ Sim |
| `spermogramVolume` | string | ✅ Sim |
| `spermogramMotility` | string | ✅ Sim |
| `vigor` | string | ✅ Sim |
| `totalConcentration` | string | ✅ Sim |
| `mlConcentration` | string | ✅ Sim |
| `mobileConcentration` | string | ✅ Sim |
| `integrity` | string | ✅ Sim |
| `pathology` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Stallion Shipping

**Endpoint:** `POST /reproduction-stallion-shipping`

**Descrição:** Envio de material genético.

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `type` | string | ✅ Sim |
| `recipient` | string | ✅ Sim |
| `place` | string | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Stallion Storage

**Endpoint:** `POST /reproduction-stallion-storage`

**Descrição:** Armazenamento de sêmen.

### Enums

- **StallionStorageResultEnum:** `Refrigerado`, `Congelado`

### POST - Criar

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| `animalId` | string | ✅ Sim |
| `userId` | string | ✅ Sim |
| `result` | StallionStorageResultEnum | ✅ Sim |
| `observation` | string | ✅ Sim |
| `fileUrl` | string | ❌ Não |

---

## Padrão de Fetch (Todas as rotas)

Todas as rotas de reprodução seguem o mesmo padrão para busca:

**Endpoint:** `GET /{rota}/fetch`

| Parâmetro | Tipo | Obrigatório |
|-----------|------|-------------|
| `page` | number | ✅ Sim |
| `animalId` | string | ✅ Sim |
| `appointmentId` | string | ❌ Não |

## Padrão de Edit (Todas as rotas)

**Endpoint:** `PUT /{rota}/:id`

Todas as rotas de edição tornam os campos opcionais e adicionam `companyId` como campo opcional.

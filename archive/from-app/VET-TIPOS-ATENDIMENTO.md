> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# Tipos de Atendimento Veterinário — Checklist App

Documento de referência para implementar cada tipo de atendimento no app (rotas API, mappers, dashboard vet e tela de lista), alinhado ao painel web (`ServiceRecords.tsx` e `boardRecordService`).

**Como implementar cada um (igual Reprodução):**
1. Adicionar em `lib/api-routes.ts`: rota `listByAnimal(animalId)` com query `page=1&animalId=...`
2. Adicionar em `lib/api-mappers.ts`: função que extrai a lista do body (ex.: `body.reproductionDonorGynos`) e normaliza com `id`, `createdAt`, `_subtype` se for reprodução
3. No dashboard `app/(animal)/vet/index.tsx`: carregar o endpoint no `load()`, incluir no merge da categoria correspondente e exibir no preview
4. Na tela de lista (ex.: `vet/orthopedics.tsx`, `vet/dentistry.tsx`, `vet/reproduction.tsx`): carregar e listar os registros

---

## Geral (4)

| # | Section Key       | Título (web)     | API path           | FetchKey             | App |
|---|-------------------|------------------|--------------------|----------------------|-----|
| 1 | general-service   | Atendimento Geral| general-service    | generalServices      | [x] |
| 2 | general-test      | Exame Físico     | general-test       | generalTests         | [x] |
| 3 | general-info      | Info. Adicionais | general-info       | generalInfos         | [x] |
| 4 | general-prescription | Prescrições   | general-prescription | generalPrescriptions | [x] |

---

## Odontologia (6)

| # | Section Key        | Título (web)      | API path            | FetchKey              | App |
|---|--------------------|-------------------|---------------------|------------------------|-----|
| 5 | dentistry-exam     | Exame Físico      | dentistry-exam      | dentistryExams        | [x] |
| 6 | dentistry-assessment | Avaliação Periodontal | dentistry-assessment | dentistryAssessments | [x] |
| 7 | dentistry-odontogram | Odontograma     | dentistry-odontogram | dentistryOdontograms | [x] |
| 8 | dentistry-oral     | Exame Intra Oral | dentistry-oral     | dentistryOrals        | [x] |
| 9 | dentistry-sedation | Sedação          | dentistry-sedation  | dentistrySedations    | [x] |
|10 | dentistry-report   | Laudo            | dentistry-report    | dentistryReports      | [x] |

---

## Ortopedia (6)

| # | Section Key      | Título (web)           | API path             | FetchKey               | App |
|---|-----------------|------------------------|-----------------------|------------------------|-----|
|11 | ortho-service   | Atendimento Ortopédico | orthopedic-service    | orthopedicServices     | [x] |
|12 | ortho-dynamic   | Exame Dinâmico e Flexão | orthopedic-test     | orthopedicTests       | [x] |
|13 | ortho-block     | Bloqueios Perineurais  | orthopedic-blockage  | orthopedicBlockages   | [x] |
|14 | ortho-info      | Info. Adicionais       | orthopedic-info      | orthopedicInfos       | [x] |
|15 | ortho-extra     | Exames Complementares  | orthopedic-extra     | orthopedicExtras      | [x] |
|16 | ortho-prescription | Prescrições         | orthopedic-prescription | orthopedicPrescriptions | [x] |

---

## Reprodução — Doadora (5)

| # | Section Key     | Título (web)        | API path                 | FetchKey                 | App |
|---|----------------|---------------------|--------------------------|--------------------------|-----|
|17 | donor-gyno     | Avaliação Ginecológica | reproduction-donor-gyno | reproductionDonorGynos   | [x] |
|18 | donor-heat     | Acompanhamento do CIO | reproduction-donor-heat | reproductionDonorHeats   | [x] |
|19 | donor-ovulation | Indução da Ovulação | reproduction-donor-ovulation | reproductionDonorOvulations | [x] |
|20 | donor-insemination | Inseminação      | reproduction-donor-insemination | reproductionDonorInseminations | [x] |
|21 | donor-embryo   | Coleta de Embrião   | reproduction-donor-embryo | reproductionDonorEmbryos | [x] |

---

## Reprodução — Receptora (10)

| # | Section Key              | Título (web)       | API path                          | FetchKey                        | App |
|---|--------------------------|--------------------|-----------------------------------|---------------------------------|-----|
|22 | receptor-gyno            | Avaliação Ginecológica | reproduction-receptor-gyno   | reproductionReceptorGynos       | [x] |
|23 | receptor-heat            | Acompanhamento de CIO  | reproduction-receptor-heat   | reproductionReceptorHeats       | [x] |
|24 | receptor-hormones        | Indução Hormonal   | reproduction-receptor-hormones    | reproductionReceptorHormones    | [x] |
|25 | receptor-inovulation    | Inovulação         | reproduction-receptor-inovulation | reproductionReceptorInovulations | [x] |
|26 | receptor-embryo          | Embrião Receptora  | reproduction-receptor-embryo      | reproductionReceptorEmbryos     | [x] |
|27 | receptor-diagnosis-initial | Diagnóstico Inicial | reproduction-receptor-diagnosis | reproductionReceptorDiagnosiss | [x] |
|28 | receptor-diagnosis-final | Diagnóstico Final  | reproduction-receptor-diagnosis   | reproductionReceptorDiagnosiss  | [x] |
|29 | receptor-vaccines        | Vacinas Gestacionais | reproduction-receptor-vaccines  | reproductionReceptorVaccines     | [x] |
|30 | receptor-monitoring      | Acomp. Gestacional | reproduction-receptor-monitoring | reproductionReceptorMonitorings | [x] |
|31 | receptor-final           | Acomp. Final       | reproduction-receptor-final       | reproductionReceptorFinals      | [x] |

---

## Reprodução — Cobertura (6)

| # | Section Key       | Título (web)        | API path                            | FetchKey                         | App |
|---|-------------------|---------------------|-------------------------------------|----------------------------------|-----|
|32 | breeding-initial  | Diag. Inicial Gestação | reproduction-breeding-initial   | reproductionBreedingInitials     | [x] |
|33 | breeding-final    | Diagnóstico Final   | reproduction-breeding-intermediate  | reproductionBreedingIntermediates | [x] |
|34 | breeding-vaccines | Vacinas Gestacionais | reproduction-breeding-vaccines   | reproductionBreedingVaccines      | [x] |
|35 | breeding-pregnancy| Acomp. Gestacional  | reproduction-breedingPregnancy     | reproductionBreedingPregnancies   | [x] |
|36 | breeding-birth    | Parto               | reproduction-breeding-birth        | reproductionBreedingBirths       | [x] |
|37 | breeding-post     | Pós-parto / Neonatal| reproduction-breeding-post         | reproductionBreedingPosts        | [x] |

---

## Reprodução — Garanhão (4)

| # | Section Key         | Título (web)     | API path                           | FetchKey                        | App |
|---|--------------------|------------------|------------------------------------|---------------------------------|-----|
|38 | stallion-physical  | Exame Andrológico| reproduction-stallion-physical     | reproductionStallionPhysicals   | [x] |
|39 | stallion-collections | Coletas de Envio | reproduction-stallion-collection   | reproductionStallionCollections | [x] |
|40 | stallion-storage   | Teste Armazenamento | reproduction-stallion-storage   | reproductionStallionStorages    | [x] |
|41 | stallion-shipping  | Envio            | reproduction-stallion-shipping     | reproductionStallionShippings   | [x] |

---

## Resumo

- **Total:** 41 tipos.
- **Implementados no app:** 41 (Geral 4, Odontologia 6, Ortopedia 6, Reprodução 25).
- **Pendentes:** 0.

Atualize os `[x]` neste arquivo conforme cada tipo for implementado no app.

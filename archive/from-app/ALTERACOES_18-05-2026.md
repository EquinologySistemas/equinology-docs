> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# Alterações no equinology-app-v2 — 18/05/2026

Refatoração de alinhamento do app mobile com a web (`equinology-web-v2`) e a API (`vetequus-api`) após os Sprints 1–5 + round QA de 16/05/2026. Todas as alterações abaixo foram aplicadas diretamente no código.

> **Update 18/05 (2ª rodada):** alinhamento completo do `AnimalRegistrationSheet` com o `CreateAnimalSheet` da web — campo Sexo adicionado, categoria renomeada para `MATRIX` (era `BREEDING`), catálogo completo de raças/pelagens (30 raças, 22 cores), remoção do campo Cliente (o usuário do app **é** o cliente). Detalhes na seção **10**.

**Premissa adotada:** o app mobile é **somente leitura** para registros de saúde (vacinas, vermífugos, exames, ferrageamento) e atendimentos veterinários. Cadastro/edição desses registros só acontece no painel web pelo veterinário. O app continua sendo de escrita para anotações, animais (cadastro/registro por código), perfil do cliente e pagamento de faturas.

---

## 1. Tipos e mappers

### 1.1 `data/types.ts`
- **`Exam.nextDate?: string`** — campo novo, alinhado com o back (round QA 16/05/2026 item A.6). Permite mostrar a "próxima realização" na tela de exames e alimenta lembretes de vencimento.
- **`genderLabels.BREEDING`** → `"Reprodução"` (era `"Égua"`). Mesma nomenclatura usada na web e em todos os relatórios/laudos.
- **`genderCategories[BREEDING]`** → label `"Reprodução"` e plural `"Reprodução"` (era `"Égua/Éguas"`).

### 1.2 `lib/api-mappers.ts`
- **`mapExam`** agora propaga `nextDate: raw.nextDate`. Antes o campo era ignorado mesmo quando a API o retornava.

---

## 2. Telas de saúde — visualização do nextDate em exames

### 2.1 `app/(animal)/health/exams.tsx`
- Importa `Badge` e adiciona `Linking`/`TouchableOpacity`/`Paperclip`.
- Renderiza badge `"Próxima realização: DD/MM/AAAA"` (verde-âmbar) quando `exam.nextDate` existe — idêntico ao padrão das vacinas/vermífugos/ferrageamento.
- Renderiza chip clicável **"Ver resultado"** quando `exam.resultFileUrl` existe (abre PDF/imagem via `Linking.openURL`).

---

## 3. Remoção do `HealthRecordSheet` (mock 100% morto)

Como o cadastro de saúde só acontece na web, o sheet — que era um mock com `setTimeout(800ms)` sem nenhuma chamada de API — foi removido completamente.

### 3.1 Arquivo deletado
- `components/sheets/HealthRecordSheet.tsx` — removido com `rm`.

### 3.2 `components/sheets/index.ts`
- Removido `export { HealthRecordSheet }`.

### 3.3 `app/_layout.tsx`
- Removido `import { HealthRecordSheet }`.
- Removido `<HealthRecordSheet />` da árvore de providers.

### 3.4 `contexts/ActionSheetContext.tsx`
- Removida toda a API do health sheet do contexto: `isHealthRecordSheetOpen`, `openHealthRecordSheet`, `closeHealthRecordSheet`, `healthRecordType`, `selectedHealthRecord` e o tipo `HealthRecordType`.
- Removidos os imports de `Vaccine/Deworming/Exam/Shoeing/SanitaryProtocol` que só serviam para esse tipo.
- Verificado que nenhuma tela chama `openHealthRecordSheet` — busca global retornou 0 ocorrências.

---

## 4. Limpeza de `console.log` residuais (13 logs removidos)

- **`app/(animal)/[id].tsx`** — 1 log (`console.log(photos)` na linha de antes do return).
- **`app/(animal)/notes.tsx`** — 1 log (`console.log(res.body)` em `load`).
- **`components/sheets/InvoicePaymentSheet.tsx`** — 11 logs (`[CARTÃO]`, `[PIX]`, `[SHEET]`, `[CARTÃO NOVO]`, `[CARTÃO EXISTENTE]`, todos de debug do fluxo de pagamento) + 2 `useEffect` que existiam só para logar `selectedTx`/`selectedInvoice`.

Busca global por `console.log` em `.ts/.tsx` agora retorna **zero ocorrências**.

---

## 5. Dashboard veterinário (`vet/index.tsx`) — resiliência + visualização de anexos

### 5.1 `Promise.all` → `Promise.allSettled` (37 endpoints)
Antes: se 1 dos 37 endpoints falhasse, o `Promise.all` rejeitava tudo e a dashboard não renderizava nenhum registro. Agora cada endpoint é processado independentemente:
- Cria um array `settled` via `Promise.allSettled`.
- Helper `pick(idx)` retorna `body` apenas se aquele índice teve `status === "fulfilled"` e response `status === 200`.
- 21 endpoints "principais" + array `resRepRest` para os 16 endpoints adicionais de reprodução, mantendo a mesma lógica de merge de antes.

### 5.2 Componente compartilhado `<AttachmentChip />`
Novo: `components/ui/AttachmentChip.tsx` — chip clicável com ícone de clipes que abre o anexo (foto/vídeo/PDF) via `Linking.openURL`. Exporta também o helper `pickAttachmentUrl(rec)` que tenta `fileUrl ?? attachmentUrl ?? resultFileUrl` (cobre todas as convenções da API).

Exportado em `components/ui/index.ts` e `components/index.ts`.

### 5.3 AttachmentChip aplicado em 5 telas vet
- `app/(animal)/vet/index.tsx` — 4 render functions (geral/orto/odonto/reprodução).
- `app/(animal)/vet/general.tsx`.
- `app/(animal)/vet/dentistry.tsx`.
- `app/(animal)/vet/orthopedics.tsx`.
- `app/(animal)/vet/reproduction.tsx`.

Resultado: em qualquer registro vet que o veterinário tenha anexado (na web) uma foto/vídeo/PDF, o cliente vê o chip **"Ver anexo"** e abre no browser/visualizador do celular.

---

## 6. Upload de foto do animal — usar `fullUrl`

### 6.1 `components/sheets/AnimalRegistrationSheet.tsx`
A API `POST /file` retorna `{ url, fullUrl }`. A web salva `fullUrl` (URL absoluta, pronta para `<img>`/`<Image>`). O app estava priorizando `url` (caminho relativo), o que podia quebrar a exibição da foto:

```diff
- photoUrl = (up.body as any)?.url ?? (up.body as any)?.fileUrl;
+ photoUrl = (up.body as any)?.fullUrl ?? (up.body as any)?.url;
```

---

## 7. Notas adicionais

- **`data/mock.ts`** está órfão — não é importado por nenhum arquivo `.ts/.tsx` (busca global confirmou). Não foi deletado para preservar como referência de seed, mas pode ser removido com segurança a qualquer momento.
- **`AnimalRegistrationSheet`** continua com `breeds` e `colors` hardcoded (7 raças + 10 cores). Não foi alterado pois é decisão de produto (não há endpoint `/breed` ou `/color` na API). Manter alinhado com o painel web quando a cliente cadastrar novas raças.
- **`InvoicePaymentSheet`** está totalmente funcional (PIX + cartão novo + cartão existente). Apenas a sujeira de console.log foi removida.
- **Endpoints novos da API ainda não consumidos pelo app** (decisão de produto pendente):
  - `GET /invoice` (entidade Fatura separada criada no Sprint 5/A.2) — o app continua usando `/client-payment` que já consolida pagamentos.
  - `GET /reminder/health-due` (agregador de vencimentos da web) — o app usa `/vaccine/soon/:animalId`, `/deworming/soon/:animalId`, `/shoeing/soon/:animalId` individualmente.

---

## 8. Arquivos tocados (resumo)

```
DELETED:
  components/sheets/HealthRecordSheet.tsx

NEW:
  components/ui/AttachmentChip.tsx
  data/breeds_colors.ts          ← catálogo igual ao da web (Sprint 10)
  ALTERACOES_18-05-2026.md (este arquivo)
  CHECKLIST_QA_APP.md (versão refeita após fixes)

EDITED:
  data/types.ts
  lib/api-mappers.ts
  contexts/ActionSheetContext.tsx
  components/sheets/index.ts
  components/sheets/AnimalRegistrationSheet.tsx   (reescrito para alinhar com a web)
  components/sheets/InvoicePaymentSheet.tsx
  components/cards/AnimalCard.tsx                  (badge MATRIX)
  components/ui/index.ts
  components/index.ts
  app/_layout.tsx
  app/(animal)/[id].tsx                            (mostra Sexo + Categoria)
  app/(animal)/notes.tsx
  app/(animal)/health/exams.tsx
  app/(animal)/vet/index.tsx
  app/(animal)/vet/general.tsx
  app/(animal)/vet/dentistry.tsx
  app/(animal)/vet/orthopedics.tsx
  app/(animal)/vet/reproduction.tsx
  app/(tabs)/animals.tsx                           (agrega BREEDING legado em MATRIX)
```

---

## 10. Alinhamento do cadastro de animal com a web (2ª rodada do dia)

Cliente apontou que o cadastro de animal no app estava com nomenclatura, opções e campos errados em relação ao painel web. Auditei o `CreateAnimalSheet` da web (`app/(dashboard)/_components/sheets/CreateAnimalSheet.tsx`) e refiz o equivalente do app.

### 10.1 Campo "Cliente" removido
O `CreateAnimalSheet` da web tem um select de Cliente porque o veterinário pode cadastrar um animal de qualquer cliente. **No app, o usuário logado é o próprio cliente** — o `clientId` é resolvido automaticamente pela API através do token JWT. Portanto:

- O sheet do app **não tem** mais campo "Cliente".
- O body do `POST /animal` **não envia** `clientId` (a API auto-popula).

### 10.2 Campo "Sexo" (MALE/FEMALE) adicionado
A web e o `CreateAnimalDto` da API separam **categoria reprodutiva** (gender) de **sexo biológico** (sex). O app só tinha `gender` — agora tem ambos.

- `data/types.ts`: novo `AnimalSex = "MALE" | "FEMALE"` + `sexLabels` + `sexOptions`.
- `lib/api-mappers.ts`: `mapAnimal` propaga `sex: raw.sex`.
- `AnimalRegistrationSheet`: novo Select "Sexo" (Macho/Fêmea).
- **Auto-coerência:** quando o usuário escolhe Categoria Garanhão/Castrado, o Sexo é setado para Macho automaticamente; Matriz/Doadora/Receptora → Fêmea automaticamente. O usuário ainda pode trocar manualmente.
- `app/(animal)/[id].tsx`: ficha do animal agora mostra "Sexo" + "Categoria" (antes era só "Gênero" misturando os dois).

### 10.3 Categoria `MATRIX` (em vez de `BREEDING`)
A web canonizou `MATRIX` como categoria principal de égua reprodutora. `BREEDING` ficou só para compat legada de animais antigos.

- `AnimalGender` no app agora tem **6 valores**: `STALLION | CASTRATED | MATRIX | DONOR | RECEPTOR | BREEDING`.
- `genderCategories` (lista selecionável no Sheet) **não inclui BREEDING** — só os 5 ativos: Garanhão, Castrado, Matriz, Doadora, Receptora.
- `genderLabels.BREEDING` aponta para `"Matriz"` (mesmo label da categoria nova) — animais legados continuam aparecendo com nomenclatura correta.
- `(tabs)/animals.tsx`: ao agrupar animais por categoria, a coluna "Matrizes" inclui animais com `gender === "MATRIX"` **e** `gender === "BREEDING"` (caso contrário sumiriam da view).
- `AnimalCard`: variant do badge mapeado para `MATRIX` (info / azul) e mantém `BREEDING` no mesmo variant.

### 10.4 Catálogo completo de raças e pelagens
A web tem **30 raças** e **22 pelagens** em `constants/breads_colors.ts` (ids = nomes, formato consistente entre painel e relatórios). O app tinha 7 raças hardcoded com IDs falsos (`b1, b2…`) e 10 cores idem.

- **Novo arquivo `data/breeds_colors.ts`** no app, idêntico ao da web (copiado conteúdo). Ids = nomes — quando salvo na API, o registro do animal fica idêntico entre painéis.
- `AnimalRegistrationSheet` importa de `@/data/breeds_colors` e renderiza as listas dinâmicas.
- Body do POST/PUT envia `breed: breedId` e `color: colorId` (igual à web). **Antes:** o app fazia `breeds.find(b => b.id === breedId)?.name` — salvava o nome humano. Agora salva direto o ID (que coincide com o nome no catálogo novo, mas é tecnicamente o ID).

### 10.5 Outros ajustes no Sheet
- Label "Gênero" → **"Categoria"** (alinhado com a web).
- Pelagem marcada como **"(opcional)"** no label (idem web).
- Propriedade (Haras) marcada como **"(opcional)"**.
- Data de nascimento marcada como **"(opcional)"**.
  - **Máscara DD/MM/AAAA** aplicada conforme o usuário digita (`maskDateInput` injeta as barras nas posições corretas, descarta caracteres não-numéricos).
  - **Conversão automática para ISO** no submit: helper `dateBrToIso` transforma `DD/MM/AAAA` em `YYYY-MM-DD` antes de enviar ao back (a API exige ISO).
  - Validação: data incompleta/inválida bloqueia o submit com toast "Data de nascimento inválida (use DD/MM/AAAA)".
  - `maxLength={10}` no `Input` impede digitação além do formato.
- Endpoint de Haras mudou: era `GET /stud-farm?page=1` (lista tudo), agora é `GET /stud-farm/client?page=1` — endpoint exclusivo do cliente que filtra automaticamente pelas propriedades vinculadas a ele.

### 10.6 Auto-refresh das listas após cadastro

Antes: depois de cadastrar/vincular um animal, o sheet fechava com sucesso mas as telas que listavam animais (Home, Animais, modal "Fichas de Animais" no Perfil) **não recarregavam** — o usuário precisava fazer pull-to-refresh ou trocar de aba para ver o animal recém-criado.

Fix:
- `AnimalContext` ganhou `animalsVersion: number` (contador monotônico) e `triggerAnimalsRefresh()` (incrementa).
- `AnimalRegistrationSheet` chama `triggerAnimalsRefresh()` após `POST /animal` (manual) ou `POST /animal/register/:code` (por código) retornarem com sucesso.
- Cada tela que lista animais (`(tabs)/index.tsx`, `(tabs)/animals.tsx`, `(tabs)/profile.tsx`) inclui `animalsVersion` nas deps do `useEffect(load)`. Quando o contador muda, o `load` é disparado novamente.

Resultado: cadastrou animal → sheet fecha → todas as listas atualizam automaticamente sem ação do usuário.

### 10.7 Mapeamento DTO vs campos no app

| Campo no Sheet do app | Campo no body do POST `/animal` | Equivalente na web |
|-----------------------|-------------------------------|---------------------|
| Nome (input)          | `name`                        | `name` ✓            |
| Foto (upload)         | `photoUrl`                    | `photoUrl` ✓ (usa fullUrl) |
| Raça (Select)         | `breed`                       | `breed` ✓ (id do catálogo) |
| Sexo (Select) **NOVO**| `sex`                         | `sex` ✓             |
| Categoria (Select)    | `gender`                      | `gender` ✓ (MATRIX) |
| Pelagem (Select)      | `color`                       | `color` ✓ (opcional) |
| Propriedade (Select)  | `studFarmId`                  | `studFarmId` ✓ (opcional) |
| Data nasc. (input)    | `birthDate`                   | `birthDate` ✓ (opcional) |
| ~~Cliente~~ removido  | — (auto via token)            | `clientId` (web tem, app não) |
| (constante hardcoded) | `pureBlood: false`            | — (web não envia)   |

---

## 9. Smoke check estrutural sugerido

Antes do QA manual rodar:

```sh
cd equinology-app-v2
npx tsc --noEmit                 # type check
npx expo doctor                  # sanidade do Expo
npx expo start --clear           # subir com cache limpo
```

Garantir que o `EXPO_PUBLIC_API_URL` aponte para o back correto (`http://<host>:3333` em dev, URL de prod no release).

---

**Última atualização:** 2026-05-18.

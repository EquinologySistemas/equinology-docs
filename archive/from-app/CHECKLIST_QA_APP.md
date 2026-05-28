# Checklist QA — Equinology App V2

**Versão:** 18/05/2026 (pós-fixes).
**Como usar:** percorrer **na ordem** (fluxo end-to-end). Marcar `[x]` em cada item validado e anotar embaixo qualquer divergência. O percurso supõe que o backend (`vetequus-api`) está rodando e o `EXPO_PUBLIC_API_URL` está apontando corretamente.

> 📝 **Premissa do app cliente:** O app é **read-only** para registros de saúde (vacinas/vermífugos/exames/ferrageamento) e atendimentos veterinários — esses são cadastrados pelo veterinário no painel web. O app cria/edita: anotações, animais (manual ou por código), perfil do cliente e pagamentos.

---

## 🔴 0. Smoke test (antes de tudo)

- [ x] `EXPO_PUBLIC_API_URL` aponta para o back correto (verificar `.env`)
- [x ] `npx tsc --noEmit` em `equinology-app-v2` retorna 0
- [ x] `npx expo start --clear` sobe sem warnings críticos
- [ x] App abre na tela de splash → login (sem crash)

---

## 🔑 1. Autenticação (fluxo end-to-end)

### 1.1 Cadastro de cliente novo (`/(auth)/signup`)

**Endpoint:** `POST /client/register`

- [x ] Acessar pela tela de login → "Cadastre-se"
- [x ] Preencher: nome, email, telefone, CPF, senha (≥ 6 chars), confirmar senha
- [x ] Marcar os 2 checkboxes (Termos + Privacidade)
- [ x] Submeter → recebe `accessToken` → redireciona para `/(tabs)`
- [ x] **Validar erros:**
  - [ x] Email duplicado → toast vermelho "já cadastrado"
  - [ x] CPF inválido (< 14 chars formatados) → validação local
  - [ x] Senhas diferentes → toast "Senhas não coincidem"
  - [ x] Sem aceitar termos → toast "Aceite os termos e a política de privacidade"

### 1.2 Login com conta existente (`/(auth)/login` — aba "Já tenho conta")

**Endpoint:** `POST /client/auth` + `GET /client/profile`

- [ ] Email + senha → "Entrar"
- [ ] Login OK → busca perfil → redireciona para `/(tabs)`
- [ ] Senha errada → toast "Email ou senha incorretos"
- [ ] Toggle "Manter conectado":
  - [ ] Marcado: ao fechar e abrir o app, continua logado
  - [ ] Desmarcado: idem (o app sempre persiste no SecureStore, mas a flag fica registrada)

### 1.3 Primeiro acesso (`/(auth)/login` — aba "Primeiro Acesso")

**Endpoints:** `POST /client/password-code` + `PUT /client/password-code`

- [ ] Preencher email + CPF (do cadastro feito no painel web)
- [ ] "Validar e continuar" → recebe código de recuperação (já vem na resposta) → avança para "password"
- [ ] Definir nova senha + confirmar (≥ 6 chars)
- [ ] "Criar senha e entrar" → loga automaticamente e cai em `/(tabs)`
- [ ] Botão "Voltar e editar email ou CPF" funciona

### 1.4 Recuperar senha (`/(auth)/forgot-password`)

**Endpoints:** `POST /client/password-code` + `GET /client/password-code/:code` + `PUT /client/password-code`

- [ ] Passo 1: digitar email → "Enviar código" → progress bar avança
- [ ] Passo 2: digitar código recebido → "Validar" → avança
- [ ] Passo 3: nova senha + confirmação → "Redefinir senha" → avança
- [ ] Passo 4 (done): mensagem "Senha redefinida!" → botão "Ir para login" volta para `/(auth)/login`
- [ ] Conseguir logar com a nova senha

### 1.5 Logout

- [ ] Em `Profile`, botão vermelho "Sair da conta" → confirma → volta para `/(auth)/login`
- [ ] SecureStore foi limpa (reabrir app → cai em login, não loga automaticamente)

### 1.6 Reabrir app autenticado

- [ ] Sem logout, fechar app → reabrir → cai direto em `/(tabs)` sem pedir login (token revalidado via `GET /client/profile`)
- [ ] Se o back retornar 401 nesse revalidate, o app faz logout automático e cai em `/(auth)/login`

---

## 🏠 2. Tabs principais (após login)

### 2.1 Home (`/(tabs)/index.tsx`)

**Endpoints:** `GET /animal?page=1`, `GET /client-payment?page=1`, `GET /ads`

- [ ] Carrossel de anúncios (`AdsCarousel`) aparece no topo
- [ ] Stats: "Total animais" e "Pendente (R$)" mostram valores reais
- [ ] Seção "Meus Animais": até 6 animais em scroll horizontal
- [ ] Botão `+` ao lado do título "Meus Animais" abre o `AnimalRegistrationSheet`
- [ ] "Ver todos" navega para `/(tabs)/animals`
- [ ] Tap em animal abre `/(animal)/[id]`
- [ ] Seção "Faturas Recentes": até 5 faturas em scroll horizontal
- [ ] Tap em fatura abre `InvoicePaymentSheet`
- [ ] "Ver todas" navega para `/(tabs)/finances`
- [ ] Estado vazio: mensagens claras (sem animais / sem faturas)

### 2.2 Animais (`/(tabs)/animals.tsx`)

**Endpoint:** `GET /animal?page=1`

- [ ] Lista agrupada por **5 categorias**: Garanhão, Castrado, **Matriz** (alinhado com web; também agrega animais legados com `gender=BREEDING`), Doadora, Receptora
- [ ] Cada categoria mostra contagem + scroll horizontal de até 5 animais
- [ ] Botão `+` por categoria pré-seleciona a categoria ao abrir o `AnimalRegistrationSheet`
- [ ] Busca: tocar no ícone de lupa → digitar nome → resultados filtram client-side
- [ ] Tap em animal abre detalhe
- [ ] **Validar:** animais antigos com `gender=BREEDING` aparecem na seção "Matrizes" (não somem)

### 2.3 Agenda (`/(tabs)/agenda.tsx`)

**Endpoint:** `GET /appointment/client?page=1`

- [ ] Cabeçalho: navegação mês anterior/próximo
- [ ] Slider de dias do mês selecionado mostra ponto de marcação quando há evento
- [ ] Tap em dia → carrega eventos daquela data (formato `YYYY-MM-DD`)
- [ ] Card de evento mostra: horário formatado (HH:mm), tipo, local (haras), animal
- [ ] Tap em evento abre modal de detalhe com badge azul (SERVICE) ou info (ACTIVITY) + lista de animais com avatar

### 2.4 Finanças (`/(tabs)/finances.tsx`)

**Endpoint:** `GET /client-payment?page=1`

- [ ] Stats: "Pendente" + "Pago" em valores totais
- [ ] Filtros Todas / Pendentes / Pagas funcionam
- [ ] Card de fatura mostra: nome, animal, X/Y parcelas, valor total, badge de status (Pago/Pendente/Vencido)
- [ ] Tap em fatura abre `InvoicePaymentSheet`
- [ ] Botão "Carregar mais" pagina client-side (10 por página)

### 2.5 Perfil (`/(tabs)/profile.tsx`)

**Endpoints:** `GET /client/profile` (via session), `PUT /client/:clientId`, `GET /animal?page=1`

- [ ] Card de perfil mostra avatar (ou inicial do nome), nome, email, código pessoal
- [ ] Tap no código → copia para clipboard + toast "Código copiado!"
- [ ] Tap em "Meus dados" → modal:
  - [ ] Nome e telefone editáveis; email e CPF desabilitados
  - [ ] "Salvar Alterações" persiste via `PUT /client/:clientId`
- [ ] Tap em "Fichas de Animais" → modal lista os animais do cliente; cada um tem botão "Copiar" (copia `animal.code` para clipboard)
- [ ] "Política de Privacidade" e "Termos de Uso" — placeholders (sem ação ainda)
- [ ] "Sair da conta" → confirmação → logout

---

## 🐎 3. Cadastro de animal — fluxo manual + por código

> **Importante:** o sheet do app foi **alinhado** com o `CreateAnimalSheet` da web. Mesmas opções (30 raças, 22 cores), mesmo DTO (`sex` + `gender` separados, `MATRIX` como categoria correta). A única diferença é que o app **não** tem campo "Cliente" — o usuário do app **é** o cliente, então `clientId` é resolvido pela API automaticamente via token.

### 3.1 Cadastro manual (`AnimalRegistrationSheet` mode = "manual")

**Endpoints:** `GET /stud-farm/client?page=1`, `POST /file` (opcional), `POST /animal`

- [ ] Abrir o sheet (via Home `+` ou Animais `+` por categoria)
- [ ] Selecionar **"Cadastro Manual"**
- [ ] **Foto:**
  - [ ] Tap no placeholder de câmera abre seletor nativo de imagem
  - [ ] Após selecionar, preview aparece imediatamente
  - [ ] Ao salvar, o `POST /file` envia o arquivo e a URL absoluta (`fullUrl`) é gravada em `photoUrl`
- [ ] **Campos exibidos (em ordem):**
  - [ ] Nome (obrigatório, texto)
  - [ ] Raça (Select com **30 raças** — American Trotter, Andaluz, Appaloosa, Árabe, Lusitano, Mangalarga Marchador, Mini Horse, Paint Horse, Puro-Sangue Inglês, Quarto de Milha, SRD, etc.) — igual à web
  - [ ] **Sexo** (Macho / Fêmea) — campo novo, antes não existia
  - [ ] **Categoria** (Garanhão / Castrado / **Matriz** / Doadora / Receptora) — antes chamava "Gênero" e tinha "Égua" no lugar de "Matriz"
  - [ ] Pelagem (Select com **22 cores** — Alazão, Apalusa, Baio, Castanho, Tordilho, Palomino, Pampa, etc.) — opcional
  - [ ] Propriedade (Select carregado de `/stud-farm/client`) — opcional
  - [ ] Data de nascimento (formato **`DD/MM/AAAA`** com máscara automática enquanto digita) — opcional. App converte para ISO antes de mandar pra API. Data inválida bloqueia submit com toast.
- [ ] **Sem campo "Cliente"** (correto — o usuário do app é o cliente)
- [ ] **Auto-coerência Sexo × Categoria:**
  - [ ] Escolher Categoria "Garanhão" ou "Castrado" → Sexo muda para "Macho" automaticamente
  - [ ] Escolher Categoria "Matriz", "Doadora" ou "Receptora" → Sexo muda para "Fêmea" automaticamente
  - [ ] Usuário ainda pode trocar manualmente o Sexo se quiser
- [ ] **Submeter** → toast "Animal cadastrado com sucesso!" → sheet fecha
- [ ] **Validar:** voltar para a Home/Animais → o animal recém-criado aparece na lista
- [ ] **Validar foto:** abrir o animal recém-criado → foto carrega corretamente (a URL é absoluta, não relativa)
- [ ] **Validar campos:** abrir a ficha → mostra Sexo + Categoria com os valores corretos (não mais "Égua")

### 3.2 Cadastro por código (`AnimalRegistrationSheet` mode = "code")

**Endpoint:** `POST /animal/register/:code`

- [ ] Abrir o sheet → "Adicionar por Código"
- [ ] Inserir um código compartilhado (que outro cliente ou o veterinário forneceu)
- [ ] "Adicionar Animal" → toast de sucesso → sheet fecha
- [ ] Animal aparece na lista do cliente
- [ ] **Validar erro:** código inválido → toast com mensagem do back

### 3.3 Fechar/cancelar sheet

- [ ] Tap no X fecha e limpa o form (volta para mode "select")
- [ ] Botão "← Voltar" dentro de cada modo volta para a tela de seleção

---

## 🐴 4. Detalhe do animal (`/(animal)/[id]`)

**Endpoint:** `GET /animal/:id`

- [ ] Cabeçalho: carousel de fotos (foto principal ou fallback)
- [ ] Botão voltar (`ChevronLeft`) e share (copia `shareCode`)
- [ ] Indicadores (dots) se houver mais de uma foto
- [ ] Card com nome do animal + propriedade (haras)
- [ ] Badge de categoria exibe **"Matriz"** (não mais "Égua" nem "Reprodução"), inclusive para animais legados com `gender=BREEDING`
- [ ] Chip de `shareCode` clicável → copia para clipboard
- [ ] Cards de info: Raça, Pelagem, Nascimento (DD/MM/AAAA), **Sexo** (Macho/Fêmea), **Categoria** (Garanhão/Castrado/Matriz/Doadora/Receptora)
- [ ] Card de localização (haras) com nome, endereço, cidade/estado
- [ ] Menu de gestão (4 itens):
  - [ ] Manejo Sanitário → `/(animal)/health`
  - [ ] Atendimentos → `/(animal)/vet`
  - [ ] Anotações → `/(animal)/notes`
  - [ ] Custos → `/(animal)/payments`

---

## 📝 5. Anotações (`/(animal)/notes.tsx`)

**Endpoints:** `GET /animal-note/animal/:animalId`, `POST /animal-note`, `PUT /animal-note/:id`, `DELETE /animal-note/:id`

- [ ] Lista carrega anotações do animal
- [ ] Empty state quando não há anotações ("Toque no + para adicionar")
- [ ] Botão `+` (canto direito do header) abre modal de criação
- [ ] Criar nota: digitar texto → "Salvar anotação" → persiste + aparece no topo da lista
- [ ] Edição/exclusão: as opções existem no código mas estão **comentadas** na UI atual — confirmar se isso é intencional ou se você quer reativar
- [ ] Cada card mostra texto + data formatada PT-BR (data + hora)

---

## 💊 6. Manejo sanitário (`/(animal)/health/*` — read-only)

### 6.1 Hub (`/(animal)/health/index.tsx`)

**Endpoints:** `GET /vaccine/:animalId`, `GET /deworming/:animalId`, `GET /exam/:animalId`, `GET /shoeing/:animalId`, `GET /sanitary-protocol?page=1` (todas em paralelo)

- [ ] 4 stat cards (Vacinas, Exames, Vermífugos, Ferrageamento) com contagens reais
- [ ] 5 cards de menu navegam para cada sub-tela
- [ ] **Não há mais botão "Adicionar"** em nenhuma sub-tela (escopo é read-only)

### 6.2 Vacinas (`/(animal)/health/vaccines.tsx`)

- [ ] Lista todas as vacinas com nome, descrição, data, local
- [ ] Badge "Próxima: DD/MM/AAAA" quando `nextDate` existe
- [ ] Empty state se sem registros

### 6.3 Vermífugos (`/(animal)/health/dewormings.tsx`)

- [ ] Lista vermífugos com nome, descrição, data
- [ ] Badge "Próxima: DD/MM/AAAA" quando `nextDate` existe

### 6.4 Exames (`/(animal)/health/exams.tsx`) — **TELA COM FIX NOVO**

- [ ] Lista exames com nome, laboratório, data
- [ ] Card de resultado (`exam.result`) aparece em destaque
- [ ] **NOVO:** Chip verde **"Ver resultado"** quando `exam.resultFileUrl` existe — abrir abre PDF/imagem no browser
- [ ] **NOVO:** Badge âmbar **"Próxima realização: DD/MM/AAAA"** quando `exam.nextDate` existe

### 6.5 Ferrageamento (`/(animal)/health/shoeing.tsx`)

- [ ] Lista ferrageamentos com tipo (Ferrageamento/Casqueamento/Ortopédico), nome do ferreiro, descrição, data
- [ ] Badge "Próximo: DD/MM/AAAA" quando `nextDate` existe

### 6.6 Protocolos sanitários (`/(animal)/health/protocols.tsx`)

- [ ] Lista protocolos do haras
- [ ] Cada protocolo expandido mostra: nome, descrição, categoria-alvo, itens (Vacina/Vermífugo/Exame) com período e flag de recorrente

---

## 🩺 7. Atendimentos veterinários (`/(animal)/vet/*` — read-only)

### 7.1 Dashboard vet (`/(animal)/vet/index.tsx`) — **TELA REFEITA**

**Endpoints:** 37 endpoints em paralelo, agora via `Promise.allSettled` (resiliente a falhas individuais).

- [ ] Carrega sem timeout em rede normal
- [ ] **NOVO:** Se 1 endpoint falhar, os outros 36 continuam renderizando (não trava a tela)
- [ ] 4 stat cards (Geral, Reprodução, Ortopedia, Odontologia) com contagens reais
- [ ] Chips de categoria (Geral / Reprodução / Ortopedia / Odontologia) trocam a view
- [ ] Preview mostra os 3 últimos registros + botão "Ver todos" navega para a sub-tela
- [ ] **NOVO:** Em registros com anexo (`fileUrl`/`attachmentUrl`/`resultFileUrl`), aparece chip verde **"Ver anexo"** que abre o arquivo no browser
- [ ] Empty state com mensagem "Os formulários podem ser preenchidos no painel web durante o atendimento."

### 7.2 Atendimento Geral (`/(animal)/vet/general.tsx`)

**Endpoints:** `general-info`, `general-test`, `general-service`, `general-prescription`

- [ ] 4 abas (Informações, Exame Clínico, Atendimentos, Receitas)
- [ ] Aba "Exame Clínico" mostra os parâmetros (Temp/FC/FR/Mucosas/TPC/Peso)
- [ ] Aba "Atendimentos" mostra `problem` em destaque
- [ ] **NOVO:** Cada registro renderiza chip "Ver anexo" se houver `fileUrl`

### 7.3 Odontologia (`/(animal)/vet/dentistry.tsx`)

**Endpoints:** 6 (assessment, oral, exam, odontogram, sedation, report)

- [ ] Lista todos os subtipos ordenados por data
- [ ] Avaliação Periodontal e Intra Oral mostram parâmetros específicos
- [ ] Badge `Raio-X` aparece em Avaliação se `xRay` for true
- [ ] **NOVO:** Chip "Ver anexo" por registro

### 7.4 Ortopedia (`/(animal)/vet/orthopedics.tsx`)

**Endpoints:** 6 (service, test, blockage, info, extra, prescription)

- [ ] Lista todos os subtipos ordenados por data
- [ ] Atendimento Ortopédico mostra `problem` + parâmetros (Pescoço/Dorso/Garupa/Membros/Casco)
- [ ] Badge "Sensibilidade" em vermelho se `rec.sensibility` for true
- [ ] **NOVO:** Chip "Ver anexo" por registro

### 7.5 Reprodução (`/(animal)/vet/reproduction.tsx`)

**Endpoints:** 24 (donor-_, receptor-_, breeding-_, stallion-_)

- [ ] Lista todos os subtipos relevantes para o animal
- [ ] Cada card mostra subtipo, parâmetros (data, resultado, tipo, situação, sexo, batimentos, compatível) e observação
- [ ] **NOVO:** Chip "Ver anexo" por registro

---

## 💰 8. Pagamentos do animal (`/(animal)/payments.tsx`)

**Endpoint:** `GET /client-payment?page=1` (filtrado client-side por `animalId`)

- [ ] Lista mostra apenas pagamentos vinculados ao animal
- [ ] Cada card: nome, categoria, valor total, parcelas pagas/total, badge de status
- [ ] Detalhe expandido mostra cada parcela com data e valor (Pago/Pendente)
- [ ] Empty state quando sem pagamentos

---

## 💳 9. Pagamento de fatura (`InvoicePaymentSheet`)

**Endpoints:** `GET /credit-card`, `POST /transaction/pix/:transactionId`, `POST /transaction/credit/existing`, `POST /transaction/credit/new`

- [ ] Abrir uma fatura em Home/Finances/Animal Payments → sheet abre
- [ ] Header mostra nome da fatura, categoria, animal e valor total grande no card verde
- [ ] Lista de parcelas: tap em parcela não-paga seleciona (borda verde)
- [ ] Aviso âmbar **"Pagamento não disponível"** aparece se a empresa não tem PIX configurado (`payable: false`)
- [ ] **PIX (se `payable: true`):**
  - [ ] Selecionar parcela → "Gerar QR Code PIX" → mostra QR code (base64) + botão "Copiar código PIX"
  - [ ] Copiar → toast "Código PIX copiado!"
  - [ ] Tratamento de erro: backend 404/410 → mensagem específica de "PIX não configurado"
- [ ] **Cartão (se `payable: true`):**
  - [ ] Carrega lista de cartões salvos via `GET /credit-card`
  - [ ] Se tem cartão: seleciona + paga via `POST /transaction/credit/existing`
  - [ ] Se não tem: botão "Adicionar cartão e pagar" abre formulário completo (cartão + titular) → `POST /transaction/credit/new`
- [ ] Fechar sheet limpa estado de cartão, parcela selecionada e form
- [ ] **NOVO:** Nenhum `console.log` aparece no Metro logs ao usar o fluxo (todos os 11 logs de debug foram removidos)

---

## ⚙️ 10. Validações estruturais

### 10.1 Build

- [ ] `npx tsc --noEmit` → 0 erros
- [ ] Smoke run no simulador iOS/Android sem warnings vermelhos no overlay do Expo

### 10.2 Nomenclatura alinhada com a web

- [ ] Em **todos** os lugares do app onde aparece o gênero `BREEDING`, o label mostra **"Reprodução"** (não mais "Égua"). Conferir em:
  - [ ] Card do animal (Home, Animais, Detalhe)
  - [ ] Badge dentro de `/(animal)/[id]`
  - [ ] Filtro de gênero em `/(tabs)/animals`
  - [ ] Sheet de cadastro (campo "Gênero")

### 10.3 Console

- [ ] Abrir o Metro/Expo Dev Tools enquanto navega — **nenhum `console.log`** aparece (todos foram limpos)

### 10.4 Rotas removidas

- [ ] Busca por `openHealthRecordSheet`, `HealthRecordSheet`, `isHealthRecordSheetOpen` no código → 0 ocorrências
- [ ] Build não quebra após a remoção do sheet

---

## 🐛 11. Casos de borda

- [ ] Sem conexão (modo avião): app mostra erro em vez de tela branca
- [ ] Token expirado: `401` no `GET /client/profile` faz logout automático
- [ ] Animal sem foto: usa fallback (placeholder online — não quebra)
- [ ] Animal sem haras: detalhe não mostra o card de localização
- [ ] Vet dashboard com **1 endpoint timeoutando**: os outros 36 ainda renderizam (vantagem do `Promise.allSettled`)
- [ ] Registro vet sem `fileUrl`/`attachmentUrl`/`resultFileUrl`: o chip "Ver anexo" não aparece (não polui a UI)
- [ ] Fatura com `payable: false`: esconde formas de pagamento, mostra aviso âmbar
- [ ] Cliente sem cartão salvo: mostra "Adicionar cartão e pagar"

---

## 📋 12. Definições pendentes (não bloqueiam release, mas alinhar com cliente)

- [ ] **Lembretes de vencimento na Home do app?** A web tem card "Próximos vencimentos (7 dias)" (`GET /reminder/health-due?days=7`). O app **não** consome esse endpoint. Decidir se faz sentido para o cliente final.
- [ ] **Faturas via `/invoice` aparecem no app?** A web criou a entidade `Invoice` separada (Sprint 5/A.2). O app só consome `/client-payment`. Confirmar com backend se as faturas emitidas via `/invoice` aparecem também em `/client-payment` (geralmente sim, pois a API agrega).
- [ ] **Anotações: ativar edição/exclusão?** O código existe em `notes.tsx` mas está comentado. Reativar se for útil para o cliente.
- [ ] **Catálogo de raças/cores:** continua hardcoded em `AnimalRegistrationSheet` (7 raças, 10 cores). Manter ou criar `/breed` e `/color` na API?

---

## ✅ 13. Após tudo validado

- [ ] Itens 1–11 todos marcados
- [ ] Decisões da seção 12 alinhadas com a cliente
- [ ] `npx tsc --noEmit` retorna 0
- [ ] `eas build --profile preview` (ou similar) compila sem erro
- [ ] Versão de teste interno instalada e validada por 1 cliente real

---

## 📁 Referências

- `ALTERACOES_18-05-2026.md` — resumo das alterações desta refatoração
- `DOCUMENTACAO-API-INTEGRACAO.md` — referência tela × endpoint (original)
- `VET-TIPOS-ATENDIMENTO.md` — 41 tipos de atendimento (todos integrados)
- `../vetequus-api/API_ROUTES.md` — referência das rotas do back

---

**Última atualização:** 2026-05-18 (após aplicação dos fixes).

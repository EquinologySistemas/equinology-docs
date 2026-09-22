> Registro histórico. Para os procedimentos e o funcionamento da revisão atual, consulte a [documentação técnica](../../README.md).

# Auditoria das Requisições do Cliente — Caderno 16/04/2026

> Comparação item a item entre as anotações do cliente (`Anotações APP.docx`) e o estado atual do código em `equinology-web-v2`.
> Cada item traz **status**, **complexidade**, **evidência no código** (`arquivo:linha`) e **observação** quando necessário.
> Complementa o `PLANO_IMPLEMENTACAO_REQUISICOES.md` com checagem real do código.

## Legenda

| Status | Significado |
|---|---|
| ✅ FEITO | Implementado e atende ao pedido |
| 🟡 PARCIAL | Parte feita, parte falta |
| ⛔ PENDENTE | Não implementado |
| 🐞 BUG | Relato de comportamento incorreto a investigar |
| ❓ CONFUSO | Pedido ambíguo — precisa validação com o cliente |

| Complexidade | Significado |
|---|---|
| 🟢 BAIXA | Ajuste de copy / label / CSS / reorder |
| 🟡 MÉDIA | Mudança de formulário, novo campo, refactor pontual |
| 🔴 ALTA | Nova feature, integração, refactor amplo |

---

## Visão geral (56 itens)

| Status | Qtd | % |
|---|---|---|
| ✅ FEITO | 8 | 14% |
| 🟡 PARCIAL | 18 | 32% |
| ⛔ PENDENTE | 22 | 39% |
| 🐞 BUG | 4 | 7% |
| ❓ CONFUSO | 4 | 7% |

---

## 1. Cadastros

### Item 1 — Criar Propriedade
> Tirar obrigatoriedade de endereço · Rua/número antes de cidade/UF · Campo nome do cliente (dono)

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [NewPropertySheet.tsx:14-25](app/(dashboard)/_components/sheets/NewPropertySheet.tsx#L14-L25) (campo `ownerName` já existe) · [NewPropertySheet.tsx:101-171](app/(dashboard)/_components/sheets/NewPropertySheet.tsx#L101-L171) (endereço sem `required`; rua/número vêm antes de cidade/UF)
- **Falta:** confirmar visualmente a ordem dos campos e que nenhum endereço seja marcado obrigatório no submit.

### Item 2 — Criar Cliente
> Tirar obrigatoriedade de telefone e CPF · Adicionar Email

- **Status:** ✅ FEITO
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [CreateOwnerSheet.tsx:124-169](app/(dashboard)/_components/sheets/CreateOwnerSheet.tsx#L124-L169) — Email obrigatório; telefone e CPF sem `required`.

### Item 3 — Criar Animal
> Adicionar foto · Tirar palavra "proprietário" · Data de nascimento opcional

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** [CreateAnimalSheet.tsx:130-139](app/(dashboard)/_components/sheets/CreateAnimalSheet.tsx#L130-L139) (handler de foto existe) · [CreateAnimalSheet.tsx:181](app/(dashboard)/_components/sheets/CreateAnimalSheet.tsx#L181) (`birthDate` opcional) · label usa "Nome do cliente"
- **Falta:** renderizar `<input type="file">` visível no formulário de criação (hoje só há a lógica de upload sem UI clara).

### Item 4 — Criar Anotação
> Colocar Animal **e** Proprietário **e** Propriedade

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** [CreateNoteSheet.tsx:29-92](app/(dashboard)/notes/_components/CreateNoteSheet.tsx#L29-L92) — variante "animal" só tem `animalId`; variante "general" só tem `name`/`description`. Sem proprietário nem propriedade.

### Item 5 — Raça (SRD e American Trotter)
- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [constants/breads_colors.ts:1-30](constants/breads_colors.ts#L1-L30) — Lista de 28 raças, **sem SRD** e **sem American Trotter**.

### Item 6 — Tabela de Animais
> "Visualizar" abrir página geral · Tirar código · Colunas: Nome / Cliente / Propriedade / Raça / Categoria / Idade

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [AnimalsTable.tsx:337-344](app/(dashboard)/clients-equines/_components/AnimalsTable.tsx#L337-L344) — colunas corretas, sem coluna "código".
- **Falta:** confirmar destino do botão "Visualizar" — deve abrir a **página geral do animal**, não a ficha de criação em modo leitura.

### Item 7 — Ficha Animal ❓
> Nenhuma caixa deve ser obrigatória · "Atendimento" é o atendimento atual?

- **Status:** ❓ CONFUSO
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** [CreateAnimalSheet.tsx:213-238](app/(dashboard)/_components/sheets/CreateAnimalSheet.tsx#L213-L238) — atualmente `breed`, `name`, `clientId`, `gender`, `sex`, `studFarmId` são obrigatórios.
- **Validar com cliente:** está pedindo modo de visualização sem validação, ou que todos os campos passem a ser opcionais na própria criação? Esclarecer também o que ele chama de "Atendimento" na ficha.

### Item 8 — Tabela Propriedades
> Colunas: Nome / Cliente / Endereço / Cidade / Estado

- **Status:** 🟡 PARCIAL (não totalmente verificado)
- **Complexidade:** 🟢 BAIXA
- **Arquivo:** [StudFarmsTable.tsx](app/(dashboard)/clients-equines/_components/StudFarmsTable.tsx) — checar colunas atuais e adicionar Cliente caso ausente.

---

## 2. Agenda e Compromissos

### Item 9 — Agenda de Hoje (múltiplos pontos)
> Tirar botão calendário · Tirar "+ novo agendamento" · Tirar "+ agendar" · Tirar "ver calendário" · Tirar Obs. · Aparecer propriedade · BUG horário 2x · Botão "Reagendar" · Ordem: Animais, descrição, tipo de serviço, data

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🔴 ALTA
- **Evidência:** [DashboardAgendaCard.tsx:82-119](app/(dashboard)/_components/DashboardAgendaCard.tsx#L82-L119) — botões "Ver calendário" e "Novo agendamento" ainda presentes · [AppointmentTooltip.tsx:135-146](app/(dashboard)/calendar/_components/AppointmentTooltip.tsx#L135-L146) — botão "Reagendar" já existe no tooltip.
- **Falta:** remover botões enumerados, exibir propriedade, reordenar campos do card, reproduzir e corrigir bug de duplicação de horário.

### Item 10 — Novo Lembrete
> Tirar obrigatoriedade descrição · Microfone · Data como primeiro campo · Onde aparecem lembretes feitos · Mudar "+ novo"

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** [CreateReminderSheet.tsx:74-86](app/(dashboard)/_components/sheets/CreateReminderSheet.tsx#L74-L86) (microfone OK) · linhas 111-119 (descrição sem `required`) · linhas 88-96 (data **não** é o primeiro campo).
- **Falta:** mover data para o topo, renomear botão "+ novo", criar listagem/histórico de lembretes feitos.

### Item 11a — Compromisso → Compromissos Pessoais
- **Status:** ✅ FEITO
- **Evidência:** [DashboardAgendaCard.tsx:23](app/(dashboard)/_components/DashboardAgendaCard.tsx#L23) · [NewAppointmentSheet.tsx:69](app/(dashboard)/_components/sheets/NewAppointmentSheet.tsx#L69)

### Item 11b — Agenda → Iniciar Atendimento
> Caixa "Iniciar atendimento? Sim/Não" · Ir direto ao Kanban

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA
- **Falta:** modal de confirmação + navegação ao Kanban do atendimento.

### Item 44 — Calendário (abril 2026)
> Tirar abrir calendário no final da janela

- **Status:** ⛔ PENDENTE (ligado ao item 9)
- **Complexidade:** 🟢 BAIXA

### Item 45 — Nova Atividade · Ordem de campos
> 1º Data/hora · 2º Propriedade+Cliente (propriedade obrigatória) · 3º Animais · Renomear botão "+ Animal"

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Arquivo:** [NewAppointmentSheet.tsx](app/(dashboard)/_components/sheets/NewAppointmentSheet.tsx) — reordenar e tornar `studFarmId` obrigatório.

### Item 46 — Novo Compromisso · Ordem de campos
> 1º Data/hora · 2º Título · 3º Descrição · Título no hover do calendário · Botão reagendar

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** `RescheduleAppointmentSheet.tsx` já existe; falta auditar tooltip e ordem do form.

### Item 48 — Nova Atividade · Comportamento
> Ao selecionar dia/data, janela fecha sozinha

- **Status:** 🐞 BUG (ou mudança de fluxo)
- **Complexidade:** 🟡 MÉDIA
- **Validar:** é bug ou pedido de novo comportamento? Hoje o usuário precisa clicar "Salvar".

### Item 54 — Clicar em cima da atividade
> Tirar data/hora · Adicionar animal, propriedade, cliente, tipo de atendimento, descrição · Tirar tipo "Atividade ou Compromisso"

- **Status:** 🟡 PARCIAL — quase todo o tooltip já cobre o que foi pedido
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [AppointmentTooltip.tsx:28-69](app/(dashboard)/calendar/_components/AppointmentTooltip.tsx#L28-L69) — animais, propriedade, cliente, tipo e descrição já aparecem.
- **Falta:** confirmar se a chip "Atividade/Compromisso" foi totalmente removida e se data/hora ainda aparece em outro estado.

### Item 56 — Visão Mês
> Mês precisa ter horário

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟢 BAIXA
- **Arquivo:** [MonthView.tsx](app/(dashboard)/calendar/_components/MonthView.tsx)

### Item 57 — Visão Dia
> Tirar dia e horário (do header? do evento?)

- **Status:** ❓ CONFUSO / ⛔ PENDENTE
- **Complexidade:** 🟢 BAIXA
- **Arquivo:** [DayView.tsx](app/(dashboard)/calendar/_components/DayView.tsx) — clarificar com cliente o que ele quer remover.

---

## 3. Atendimentos

### Item 15 — Atendimentos (tabela)
> "Novo atendimento" abrir só atendimento · Tirar "filtro todos animais" · Filtro de data · Mudança de status · Coluna categoria · "Especialidade" → "Tipo de Atendimento"

- **Status:** 🟡 PARCIAL (quase todo feito)
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** [ServicesTable.tsx:54-114](app/(dashboard)/services/_components/ServicesTable.tsx#L54-L114) — filtros de status, data e cliente presentes; coluna usa `appointmentType`.
- **Falta:** confirmar fluxo do botão "Novo atendimento" e documentar/expor mudança de status (ver itens 24 e `ChangeAppointmentStatusSheet.tsx`).

### Item 17 — Criar Atendimento
> Renomear "+ Animal" · Tirar código ao lado dos nomes

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟢 BAIXA

### Item 22 — Histórico de Atendimentos
> Foto · Sexo/idade · "Registros" → "Atendimento atual" · Tirar "Novo" do início

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** [ServiceHistory.tsx:93-128](app/(dashboard)/services/_components/ServiceHistory.tsx#L93-L128) — foto e sexo/idade já presentes; "Histórico de atendimentos" no título.
- **Falta:** renomear coluna "Registros" → "Atendimento atual" e tirar prefixo "Novo" das linhas.

### Item 24 — Finalizar Atendimento ❓
> Como finalizar?

- **Status:** ❓ CONFUSO
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [ChangeAppointmentStatusSheet.tsx:13-38](app/(dashboard)/_components/sheets/ChangeAppointmentStatusSheet.tsx#L13-L38) — `FINISHED`, `IN_PROGRESS`, `RESCHEDULED` existem.
- **Validar:** é dúvida de UX (precisa botão mais óbvio?) ou feature missing (gerar PDF/bloquear edição ao finalizar)?

### Item 25 — Histórico → Ver (BUG)
> Está abrindo a ficha de outro animal

- **Status:** 🐞 BUG
- **Complexidade:** 🟡 MÉDIA
- **Arquivo:** [ServiceHistory.tsx:79](app/(dashboard)/services/_components/ServiceHistory.tsx#L79) — investigar link/handler. Provável bug no `appointmentService.ts` (fallback).

### Item 27 — Microfone em todas as janelas (inclusive vacinas)
- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** `AudioToFormButton` já em [CreateAnimalSheet.tsx:377-380](app/(dashboard)/_components/sheets/CreateAnimalSheet.tsx#L377-L380), [NewAppointmentSheet.tsx:74-86](app/(dashboard)/_components/sheets/NewAppointmentSheet.tsx#L74-L86), [CreateReminderSheet.tsx:74-86](app/(dashboard)/_components/sheets/CreateReminderSheet.tsx#L74-L86).
- **Falta:** vacinas e demais sheets (estoque, financeiro, anotação).

### Item 36 — Histórico → Ver (destino)
> Deve abrir resumo dos atendimentos, não visão geral do animal

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA
- **Nota:** mesma origem do item 25 — após corrigir o bug, redirecionar para uma view "resumo de atendimentos".

### Item 49 — Anotações (tabela)
> Título torto · Adicionar coluna Animal · Ordem: Tipo/Data/Título+Animal/Conteúdo · Tirar "+" dos botões

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟢 BAIXA
- **Arquivo:** [NotesTable.tsx:162-198](app/(dashboard)/notes/_components/NotesTable.tsx#L162-L198) — auditar CSS do título, ordem das colunas e labels dos botões.

---

## 4. Estoque

### Item 7 (estoque) — Tela geral
> Tirar busca em saída · O que é Armazém? · Tirar botões informativos · Só "alertas" · Botão editar produto · Tirar "saída" do volante · Como cadastrar categoria · Quantidade mínima 0

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🔴 ALTA
- **Arquivos:** [stock/page.tsx](app/(dashboard)/stock/page.tsx) · [StockMovementsTable.tsx](app/(dashboard)/stock/_components/StockMovementsTable.tsx) · [StockProductsTable.tsx](app/(dashboard)/stock/_components/StockProductsTable.tsx) · [DashboardGeneralStockTable.tsx](app/(dashboard)/_components/DashboardGeneralStockTable.tsx) · [DashboardVolanteStockTable.tsx](app/(dashboard)/_components/DashboardVolanteStockTable.tsx)
- **Validar:** termo "Armazém" — substituir por outro nome ou remover?

### Item 37 — Relatório de Movimentações
> Deixar por último · Tirar horário · Tirar "+ e -" da quantidade · Valor unitário ressurgiu

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA

### Item 38 — Tabelas de estoque
> Última tabela ser só "Produtos" · Estoque Geral/Mínimo · Estoque Volante/Mínimo

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA

### Item 39 — Novo Produto
> Estoque Mínimo Geral e Volante

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [AddProductSheet.tsx:27-64](app/(dashboard)/_components/sheets/stock/AddProductSheet.tsx#L27-L64) — campos `minimumStock` e `minimumFieldStock` já existem.
- **Falta:** confirmar labels visíveis ("Estoque Mínimo Geral" / "Estoque Mínimo Volante").

---

## 5. Financeiro

### Item 3 (fin) — Criar Pagamento
> Colocar proprietário · Salvar fatura PDF

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA (campo) + 🔴 ALTA (PDF)
- **Arquivo:** [NewPaymentSheet.tsx](app/(dashboard)/financial/_components/NewPaymentSheet.tsx)

### Item 12 — Pagamentos
> Período das caixas · Primeira ação "criar cobrança" · Dados detalhados (entradas/saídas, mensal/semestral/anual, por cliente/animal/tipo) · Botão "Visualizar" · Colunas Animal/Propriedade/Data de Criação

- **Status:** ⛔ PENDENTE (parcialmente — filtros de data já existem)
- **Complexidade:** 🔴 ALTA
- **Evidência:** [PaymentsTable.tsx:73-107](app/(dashboard)/financial/_components/PaymentsTable.tsx#L73-L107) — `startDate`/`endDate` presentes.
- **Falta:** dashboards detalhados, coluna Visualizar com modal, novas colunas, reposicionar CTA.

### Item 13 — Criar Fatura
> Balanço mensal · Pessoal/Profissional como na agenda

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🔴 ALTA
- **Validar:** o que é "balanço mensal" (card de saldo ou DRE simplificado?). Confirmar onde fica o pessoal/profissional na agenda para espelhar.

### Item 14 — "+ Novo Pagamento" → "Nova Movimentação"
- **Status:** ✅ FEITO
- **Evidência:** [SmallCards.tsx:59-62](app/(dashboard)/_components/SmallCards.tsx#L59-L62)
- **Nota:** se houver outras superfícies (header de financeiro), confirmar consistência.

---

## 6. Fichas, Laudos e Especialidades

### Item 28 — Padronizar
> Tamanho de fichas · Exames adicionar PDF com fundo embaçado

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA

### Item 29 — Exportar PDF de Prescrição
> Em todos os finais de atendimento

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🔴 ALTA
- **Sugestão:** `@react-pdf/renderer` ou `jspdf`.

### Item 30 — Mídias
> Vídeos e fotos em todos os campos

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🔴 ALTA

### Item 31 — Odontologia
> Odontograma para desenhos · Campo de prescrição

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🔴 ALTA
- **Evidência:** [mock.ts:260-267](app/(dashboard)/services/_data/mock.ts#L260-L267) — seções de odontologia existem; falta odontograma visual.
- **Validar:** abordagem do odontograma (mapa clicável por dente vs desenho livre vs upload).

### Item 32 — Laudo
> Exportar PDF

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🔴 ALTA

### Item 35 — Ortopedia
> "Atendimento Ortopédico" → "Exame Físico"

- **Status:** ✅ FEITO
- **Evidência:** [mock.ts:270](app/(dashboard)/services/_data/mock.ts#L270)

### Item 23 — Doadora · Reprodução
> Cio: OE/U/OD mesma linha · Ginecológica: tirar ultrassom + OE/U/OD na mesma linha · Indução: hora

- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** [mock.ts:282-318](app/(dashboard)/services/_data/mock.ts#L282-L318) — `leftOvary`/`rightOvary` em CIO; ultrassom ainda presente em ginecológica; `time` já existe em indução.
- **Falta:** remover ultrassom da ginecológica, adicionar OE/U/OD nela, conferir layout em linha única.

### Item 26 — Vacina → Vacinação · Lembretes de vencimento
- **Status:** 🟡 PARCIAL
- **Complexidade:** 🟡 MÉDIA
- **Evidência:** `MOCK_VACCINES` no plural; lembretes de vencimento de exame/vacina **não existem** — estender [CreateReminderSheet.tsx](app/(dashboard)/_components/sheets/CreateReminderSheet.tsx) com tipo + recorrência.

### Item 33 — BUG Castrado abre Hermilia
- **Status:** 🐞 BUG
- **Complexidade:** 🟡 MÉDIA
- **Arquivos:** [services/appointmentService.ts](services/appointmentService.ts) — provável fallback incorreto por sexo no `getDetails`.

### Item 34 — Matriz · Doadora vira matriz?
> Faltando avaliação ginecológica até DG inicial · Égua doadora pode virar matriz

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🔴 ALTA
- **Evidência:** [mock.ts:355-414](app/(dashboard)/services/_data/mock.ts#L355-L414) — `breeding` sem ginecológica.
- **Validar:** modelo do papel reprodutivo (campo editável com histórico vs múltiplos papéis simultâneos).

### Item 41 — BUG Data + emoji bebê
> Atendimento criado no horário atual mostra outra data · Tirar 🍼 de "Parto"

- **Status:** 🐞 BUG + ⛔ PENDENTE
- **Complexidade:** 🟡 MÉDIA
- **Investigar:** timezone na criação/render do atendimento; varrer mock e components por emoji bebê.

---

## 7. Interface e Navegação

### Item 19 — Sidebar tudo no plural ❓
- **Status:** ❓ CONFUSO
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [DashboardSidebar.tsx:30-41](app/(dashboard)/_components/DashboardSidebar.tsx#L30-L41) — mix atual de singular/plural.
- **Validar:** pluralizar item a item ("Financeiros", "Estoques", "Assinaturas" soam estranhos).

### Item 42 — Botões atalho
> Diminuir · Ordem Cliente/Animal/Propriedade · "Criar Agendamento" (abrir no dia atual) · "Criar Fatura"

- **Status:** 🟡 PARCIAL (quase tudo feito)
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [SmallCards.tsx:32-75](app/(dashboard)/_components/SmallCards.tsx#L32-L75) — ordem e atalhos corretos.
- **Falta:** ajustar tamanho (CSS) · garantir que "Criar Agendamento" abre com data atual pré-preenchida · "Criar Fatura" precisa existir como sheet/destino.

### Item 50 — Cards
> Tirar desenhos · Aumentar letras · Tirar coloridos das categorias

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟢 BAIXA
- **Evidência:** [SmallCards.tsx:94-95](app/(dashboard)/_components/SmallCards.tsx#L94-L95) (ícones decorativos) · [AnimalsTable.tsx:68-74](app/(dashboard)/clients-equines/_components/AnimalsTable.tsx#L68-L74) (`CATEGORY_COLORS`).

### Item 52 — Nossos Parceiros
> Dar mais destaque · Colocar abaixo da clínica

- **Status:** ⛔ PENDENTE
- **Complexidade:** 🟢 BAIXA
- **Arquivo:** `AdvertisersSlider` em `DashboardSidebar.tsx`.

### Item 53 — Nome da Clínica
> Em cima não precisa ser um botão

- **Status:** ⛔ PENDENTE (não verificado)
- **Complexidade:** 🟢 BAIXA
- **Auditar:** header do dashboard.

---

## 🐞 Bugs (prioridade)

| # | Descrição | Arquivo principal | Complexidade |
|---|---|---|---|
| 25 | Histórico → Ver abre ficha de outro animal | [ServiceHistory.tsx](app/(dashboard)/services/_components/ServiceHistory.tsx) + [appointmentService.ts](services/appointmentService.ts) | 🟡 MÉDIA |
| 33 | Ficha de animal castrado abre "Hermilia" (doadora) | [appointmentService.ts](services/appointmentService.ts) | 🟡 MÉDIA |
| 41 | Data de atendimento criada agora aparece com outra data na visão geral | conversão ISO / timezone | 🟡 MÉDIA |
| 48 | Nova Atividade — selecionar data fecha a janela sozinha (validar se bug ou pedido) | [NewAppointmentSheet.tsx](app/(dashboard)/_components/sheets/NewAppointmentSheet.tsx) | 🟡 MÉDIA |

## ❓ Itens confusos (precisa cliente)

| # | Tema | Pergunta a fazer |
|---|---|---|
| 7 | Ficha animal sem obrigatoriedade | É modo visualização sem validação ou todos os campos opcionais na criação? E o que é "Atendimento" nesta tela? |
| 19 | Sidebar plural | Quais itens devem permanecer no singular? |
| 24 | Finalizar atendimento | É só tornar o botão mais visível ou existem regras novas (PDF automático, bloquear edição)? |
| 57 | Visão Dia "tirar dia e horário" | Tirar de onde — header da página, do evento, da grade? |

---

## 🗺️ Roadmap sugerido

### Sprint 1 — Quick wins (1-2 dias) 🟢
- Item 5: adicionar raças SRD e American Trotter
- Item 11a: já feito, validar
- Item 14: já feito, validar
- Item 35: já feito, validar
- Item 42 (resto): tamanho dos cards, abrir agendamento no dia atual
- Item 49: alinhar título, ordenar colunas, remover "+"
- Item 50: tirar ícones e cores das categorias
- Item 52: realocar parceiros e dar destaque
- Item 53: remover botão do nome da clínica
- Item 26 (rename): "Vacina" → "Vacinação" em labels

### Sprint 2 — Bugs críticos (2-3 dias) 🐞
- Item 25 + 36: corrigir bug do "Ver" no histórico e redirecionar para resumo de atendimentos
- Item 33: investigar fallback de gênero (Hermilia)
- Item 41: corrigir timezone na criação do atendimento; remover 🍼
- Item 48: definir comportamento esperado e ajustar

### Sprint 3 — Cadastros e Agenda (3-5 dias) 🟡
- Item 1: revisar obrigatoriedades e ordem
- Item 2: já feito, validar
- Item 3: UI para upload de foto no animal
- Item 4: adicionar Animal + Proprietário + Propriedade em Anotações
- Item 6: confirmar destino do "Visualizar" na tabela de animais
- Item 8: ajustar colunas em Propriedades
- Item 9: limpar Agenda de Hoje (botões, ordem, propriedade)
- Item 10: reordenar campos, criar listagem de lembretes feitos, renomear "+ novo"
- Item 11b: modal "Iniciar atendimento?" + redirecionar ao Kanban
- Itens 45, 46, 48, 54, 56, 57: padronizar ordem dos formulários de agenda e ajustar tooltip/visões
- Item 15: ajustes finos da tabela de atendimentos
- Item 17: rename do botão e remoção do código nos nomes
- Item 22: renomear coluna "Registros" → "Atendimento atual"

### Sprint 4 — Boards clínicos + microfone + mídia (4-6 dias) 🟡🔴
- Item 23: reorganizar campos de Doadora (CIO + ginecológica + indução)
- Item 27: microfone em todas as sheets, inclusive vacinas
- Item 28: padronizar tamanho de fichas e upload de PDF em exames
- Item 30: vídeos e fotos em todos os campos do board
- Item 31: campo de prescrição em Odontologia + decidir abordagem do odontograma
- Item 34: avaliação ginecológica em Matriz; modelo doadora↔matriz
- Item 24: tornar "Finalizar atendimento" óbvio na UX

### Sprint 5 — Estoque (3-5 dias) 🟡🔴
- Item 7-estoque: tela geral (busca, armazém, alertas, botão editar, filtros)
- Item 37: relatório de movimentações limpo
- Item 38: separar tabelas (Geral/Mínimo, Volante/Mínimo, Produtos)
- Item 39: labels claros de Mínimo Geral e Volante

### Sprint 6 — Financeiro + PDF (5-8 dias) 🔴
- Item 3-fin: campo proprietário em Criar Pagamento
- Item 12: dashboards detalhados, botão Visualizar, novas colunas, CTA "Criar cobrança" em destaque
- Item 13: balanço mensal + categoria Pessoal/Profissional
- Items 29, 32: módulo de exportação PDF (prescrição + laudo) com `@react-pdf/renderer`
- Item 3-fin + 42: PDF de fatura com logo da clínica (aguardar exemplos do cliente)
- Item 26 (parte 2): lembretes de vencimento de exames/vacinas

### Sprint 7 — Polimento e revisão final 🟢
- Item 19: pluralização da sidebar conforme decidido com o cliente
- Revisão visual de toda a UI alterada
- QA com o cliente

---

## Arquivos críticos auditados

- `app/(dashboard)/_components/sheets/NewPropertySheet.tsx`
- `app/(dashboard)/_components/sheets/CreateOwnerSheet.tsx`
- `app/(dashboard)/_components/sheets/CreateAnimalSheet.tsx`
- `app/(dashboard)/notes/_components/CreateNoteSheet.tsx`
- `app/(dashboard)/_components/sheets/CreateReminderSheet.tsx`
- `app/(dashboard)/_components/sheets/NewAppointmentSheet.tsx`
- `app/(dashboard)/financial/_components/NewPaymentSheet.tsx`
- `app/(dashboard)/_components/sheets/RescheduleAppointmentSheet.tsx`
- `app/(dashboard)/_components/sheets/ChangeAppointmentStatusSheet.tsx`
- `app/(dashboard)/_components/sheets/stock/AddProductSheet.tsx`
- `app/(dashboard)/_components/DashboardAgendaCard.tsx`
- `app/(dashboard)/_components/DashboardSidebar.tsx`
- `app/(dashboard)/_components/SmallCards.tsx`
- `app/(dashboard)/clients-equines/_components/AnimalsTable.tsx`
- `app/(dashboard)/clients-equines/_components/StudFarmsTable.tsx`
- `app/(dashboard)/notes/_components/NotesTable.tsx`
- `app/(dashboard)/services/_components/ServicesTable.tsx`
- `app/(dashboard)/services/_components/ServiceHistory.tsx`
- `app/(dashboard)/services/_data/mock.ts`
- `app/(dashboard)/financial/_components/PaymentsTable.tsx`
- `app/(dashboard)/calendar/_components/AppointmentTooltip.tsx`
- `constants/breads_colors.ts`

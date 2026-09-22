# Web profissional — equinology-web-v2

**Stack:** Next.js 16.1.6, React 19.2.3, TypeScript e Tailwind 4.

## Navegação

| Área | Caminho |
|---|---|
| Acesso e contratação | `app/(auth)/`: login, cadastro, recuperação, código, planos e checkout |
| Início e operação | `app/(dashboard)/` |
| Cadastros | `clients-equines/`, incluindo fichas de animais |
| Atendimentos | `services/` |
| Agenda | `calendar/` |
| Financeiro, estoque e CRM | `financial/`, `stock/`, `crm/` |
| Clínica, assinatura e organização | `clinic/`, `subscription/`, `notes/`, `reminders/` |
| Sincronização | `sync/` |
| Fatura compartilhada | `app/fatura/[token]/` |
| IA no servidor | `app/api/chat/`, `app/api/audio/` |

A fatura compartilhada renderiza os dados transportados pelo token do link. O recebimento e a situação persistida da fatura são tratados pela API.

## API e sessão

`context/ApiContext.tsx` centraliza as chamadas usando `NEXT_PUBLIC_API_URL`. Serviços em `services/` e componentes consomem esse cliente. Os utilitários de autenticação ficam em `lib/auth.ts` e `lib/auth-cookies.ts`.

O cookie é configurado por `NEXT_PUBLIC_USER_TOKEN`. O middleware verifica sua presença e consulta `/signature/validation`. Para preservar a navegação da sessão durante indisponibilidade de rede, o middleware possui tratamento de timeout/erro de servidor; a API autoriza cada operação recebida. Respostas de autenticação são tratadas pelo cliente de API.

## Atendimentos e documentos

As páginas de atendimentos usam a configuração das seções em `services/boardRecordService.ts` e os componentes de formulário em `app/(dashboard)/services/`. Há recursos de odontograma, laudos, prescrições, anexos e exportação de documentos.

Anotações destinadas ao proprietário e prescrições compartilhadas compõem o conteúdo apresentado no app. Ao alterar uma ficha, confira o DTO, o presenter e o mapeamento do app correspondente.

## Offline

`lib/offline/` implementa cache em IndexedDB, IDs temporários, arquivos locais e fila de escritas elegíveis. A interface exibe indicadores e a tela `/sync`, com acompanhamento e ações de sincronização.

O escopo das escritas está em `lib/offline/routes.ts`: atendimentos, participantes do atendimento, seções clínicas e registros de vacina, vermifugação, exame e casqueamento. Outras operações seguem o fluxo online. Antes do uso em campo, a sessão e os dados necessários devem ser carregados com conexão.

Leia o [ADR 0004](../decisions/0004-offline.md) para manutenção desse fluxo.

## IA e ambiente

Chat e transcrição passam pelas rotas do servidor Next.js usando `OPENROUTER_API_KEY`. O código compartilhado de áudio fica em `lib/audio-transcribe.ts`. A URL da aplicação pode ser configurada com `NEXT_PUBLIC_APP_URL`.

Configuração e comandos: [setup](../guides/developer/setup.md), [validação](../guides/operations/qa-checklist.md) e [deploy](../guides/operations/deploy.md).

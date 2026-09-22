# ADR 0002 — IA e transcrição no servidor da web

## Decisão

A web profissional encaminha chat e transcrição para rotas do servidor Next.js, que usam OpenRouter. A variável `OPENROUTER_API_KEY` é lida nesse ambiente de servidor.

## Arquivos

| Responsabilidade | Caminho em equinology-web-v2 |
|---|---|
| Chat | `app/api/chat/route.ts` |
| Transcrição de áudio | `app/api/audio/transcribe/route.ts` |
| Preenchimento assistido de formulário | `app/api/audio/transcribe-to-form/route.ts` |
| Preenchimento de odontograma | `app/api/audio/transcribe-to-odontogram/route.ts` |
| Utilitários compartilhados | `lib/audio-transcribe.ts` |

Prompts, formatos de resposta e seleção de modelo são definidos nesses arquivos. A interface consome o resultado e mantém o fluxo de revisão/preenchimento do formulário.

## Configuração

Configure `OPENROUTER_API_KEY` na hospedagem da web e `NEXT_PUBLIC_APP_URL` para a referência da aplicação. As chaves de integração são administradas no ambiente de servidor. Veja [setup](../guides/developer/setup.md).

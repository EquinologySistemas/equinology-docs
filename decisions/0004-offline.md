# ADR 0004 — Operação offline e sincronização

## Fluxo

1. A sessão autenticada e as consultas online alimentam o cache local.
2. As leituras elegíveis usam dados armazenados com a representação das alterações locais.
3. Escritas reconhecidas por `lib/offline/routes.ts` entram na fila.
4. Novos registros recebem IDs temporários. Arquivos associados são guardados localmente e têm seu fluxo de envio.
5. Quando há conexão, a sincronização envia as operações respeitando dependências e substitui referências temporárias pelos IDs retornados.
6. O usuário acompanha a fila, os resultados e as ações disponíveis em `/sync`.

## Componentes

| Componente na web | Papel |
|---|---|
| `context/OfflineContext.tsx` | Estado de conexão, contadores e acionamento da sincronização |
| `lib/offline/db.ts` | IndexedDB `equinology-offline`: respostas, entidades, fila, mapa de IDs, arquivos e metadados |
| `lib/offline/namespace.ts` | Separação dos dados locais por empresa e usuário |
| `lib/offline/routes.ts` | Reconhecimento de operações elegíveis |
| `lib/offline/interceptor.ts` | Integração das chamadas da API com o armazenamento local |
| `lib/offline/queue.ts`, `synthetic.ts`, `overlay.ts` | Fila e representação dos dados locais |
| `lib/offline/files.ts`, `sync.ts` | Envio de arquivos, dependências e operações |
| `lib/offline/warm.ts` | Carregamento dos dados de referência |
| `public/sw.js`, `lib/sw-client.ts` | Service worker e navegação |
| `app/(dashboard)/sync/page.tsx` | Interface de acompanhamento |

O namespace local usa os IDs do JWT para separar armazenamento; a autorização das operações continua na API.

## Escopo

Escritas de atendimentos, participantes, seções clínicas e registros de vacina, vermifugação, exame e casqueamento são reconhecidas pela camada. Operações financeiras, autenticação e outras rotas seguem o fluxo online.

A consulta offline depende dos dados disponíveis no navegador. Antes do trabalho em campo, carregue a sessão e os registros necessários com conexão. A fila possui estados `pending`, `syncing`, `error` e `blocked`, apresentados pela interface.

## Contrato da API

A sincronização envia `Idempotency-Key` com a identidade da operação. O interceptor da API armazena hash, estado e resposta em `IdempotencyKey`:

- Mesmo usuário, chave e conteúdo concluído: devolve a resposta armazenada.
- Mesma chave com conteúdo diferente: resposta 422.
- Operação ainda em processamento: resposta 409.
- Falha de execução: permite nova tentativa conforme o interceptor.

O contrato cobre escritas autenticadas com chave. Rotas públicas, uploads multipart e a própria exclusão `DELETE /client/me` seguem seus fluxos específicos. A limpeza dos registros é tratada por `idempotency-cleanup.service.ts`.

## Configuração e validação

O service worker é ativado em produção ou com `NEXT_PUBLIC_ENABLE_SW=true`. `NEXT_PUBLIC_OFFLINE_DISABLED=true` seleciona a execução sem a camada offline.

Para validar, carregar dados online, criar/editar um registro elegível sem conexão, conferir sua indicação local, reconectar e conferir a persistência após a sincronização. Use os mesmos usuário e empresa durante o percurso. Veja [validação](../guides/operations/qa-checklist.md).

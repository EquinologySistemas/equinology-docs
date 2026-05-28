# ADR 0002 — IA/transcrição via OpenRouter (server-side)

**Status:** aceito (implementado na web).

## Contexto
A web usa IA para transcrição de áudio e preenchimento assistido de fichas. Uma
abordagem anterior usava o SDK `openai` no **cliente**, o que exporia a chave de
API no browser (`NEXT_PUBLIC_OPENAI_API_KEY`).

## Decisão
O fluxo vivo roteia as chamadas por **rotas server-side** do Next
(`app/api/chat`, `app/api/audio/transcribe*`) usando **OpenRouter**
(`OPENROUTER_API_KEY`, sem prefixo `NEXT_PUBLIC_`). A chave nunca chega ao browser.

## Consequência
- A dependência `openai` e a var `NEXT_PUBLIC_OPENAI_API_KEY` ficaram **mortas** —
  devem ser removidas, e a chave (se já exposta) rotacionada.
- O componente legado `components/ocr/ocr.tsx` está 100% comentado (morto).

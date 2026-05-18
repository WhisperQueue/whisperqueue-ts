# whisperqueue-ts — AI Assistant Context

See root `CLAUDE.md` for shared rules (commits, TypeScript, tooling).

## What This Is

Official TypeScript SDK for the WhisperQueue API. Published to npm as `whisperqueue`. Thin HTTP wrapper — no business logic, no bundled server. Must run in both Bun and Node.js 18+.

## Build

Uses `tsup` to produce dual CJS + ESM output in `dist/`. Never use `bun build` for the library output.

```bash
bun run build     # produces dist/
bun run dev       # watch mode
```

## Path Aliases

Use `@/*` for all internal imports — maps to `./src/*`.

## SDK Design Rules

- Thin wrapper only — no business logic beyond what the API requires
- All HTTP via native `fetch` (available in Bun and Node.js 18+)
- Typed error classes — never surface raw HTTP responses to callers
- `TranscribeOptions` must include `force?: boolean` and `language?: string`
- `transcribeAndWait` polls `status()` until `completed` or `failed`

## Error Classes

All errors extend a base `WhisperQueueError`. Typed subclasses:
- `WhisperQueueAuthError` — 401
- `WhisperQueueNotFoundError` — 404
- `WhisperQueueNotReadyError` — 425
- `WhisperQueueServerError` — 500

## Project Structure

```
src/
├── index.ts        # public exports only
├── client.ts       # WhisperQueue class — all methods
├── errors.ts       # typed error classes
└── types.ts        # request/response types
```

## Required Methods

| Method | Description |
|---|---|
| `transcribe(url, options?)` | POST /transcribe — returns job response immediately |
| `status(jobId)` | GET /status/:job_id |
| `transcript(transcriptId)` | GET /transcript/:transcript_id |
| `transcribeAndWait(url, options?)` | submit + poll until completed or failed |
| `health()` | GET /health |

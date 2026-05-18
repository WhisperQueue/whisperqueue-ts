# whisperqueue

[![npm version](https://img.shields.io/npm/v/whisperqueue)](https://www.npmjs.com/package/whisperqueue)
[![License: MIT](https://img.shields.io/github/license/WhisperQueue/whisperqueue-ts)](LICENSE)
[![Issues](https://img.shields.io/github/issues/WhisperQueue/whisperqueue-ts)](https://github.com/WhisperQueue/whisperqueue-ts/issues)

Official TypeScript SDK for [WhisperQueue](https://github.com/WhisperQueue/whisperqueue-api) — async GPU-accelerated transcription microservice.

Works in **Bun**, **Node.js 18+**, and any environment with native `fetch`.

## Installation

```bash
# npm
npm install whisperqueue

# bun
bun add whisperqueue
```

## Quick Start

```ts
import { WhisperQueue } from 'whisperqueue';

const client = new WhisperQueue({
  baseUrl: 'http://omen.local:3000',
  apiKey: 'your-api-key',
});

// Submit and wait for completion
const transcript = await client.transcribeAndWait('s3://my-bucket/audio/interview.mp3');
console.log(transcript.text);
```

## Usage

### Submit a job

```ts
const job = await client.transcribe('s3://my-bucket/audio/interview.mp3');
// { jobId: 'job_abc123', status: 'queued', position: 1 }
```

Add `force: true` to bypass the cache:

```ts
const job = await client.transcribe('s3://my-bucket/audio/interview.mp3', { force: true });
```

### Poll for status

```ts
const status = await client.status(job.jobId);
// { jobId: 'job_abc123', status: 'completed', transcriptId: 'tr_xyz789' }
```

### Fetch the transcript

```ts
const transcript = await client.transcript(status.transcriptId);
// { transcriptId: 'tr_xyz789', text: '...', language: 'en', duration: 142.5, segments: [...] }
```

### Submit and wait (convenience)

```ts
const transcript = await client.transcribeAndWait('https://example.com/audio.mp3');
```

### Health check

```ts
const health = await client.health();
// { status: 'ok', model: 'large-v3', device: 'cuda', queueDepth: 0 }
```

## Error Handling

Errors are thrown as typed classes — no need to inspect status codes.

```ts
import {
  WhisperQueue,
  WhisperQueueAuthError,
  WhisperQueueNotFoundError,
  WhisperQueueNotReadyError,
  WhisperQueueServerError,
} from 'whisperqueue';

try {
  await client.transcribe(url);
} catch (e) {
  if (e instanceof WhisperQueueAuthError) {
    // 401 — bad or missing API key
  }
  if (e instanceof WhisperQueueNotFoundError) {
    // 404 — job or transcript not found
  }
  if (e instanceof WhisperQueueServerError) {
    // 500 — server error
  }
}
```

## Development

Requires [Bun](https://bun.sh) >= 1.2.

```bash
bun install
bun run dev       # watch mode
bun test          # run tests
bun run build     # build dist/
bun check         # type check + code analysis
```

## Contributing

See [CONTRIBUTING.md](https://github.com/WhisperQueue/.github/blob/main/CONTRIBUTING.md). Questions? Join the [Discord](https://discord.gg/QK4hMAtm).

## License

[MIT](LICENSE)

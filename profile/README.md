# Atomic Strata

**Modular context memory infrastructure for AI applications and agents.**

Atomic Strata builds open-source infrastructure for AI memory: the context layer that helps AI systems remember, retrieve, update, and assemble what they know about users, teams, projects, tools, and workflows.

As AI applications move from demos into production, memory becomes more than a feature. It becomes state.

That state should be inspectable, configurable, portable, and controlled by the teams building on it.

## Why Atomic Strata exists

Most AI memory products are packaged as hosted black boxes.

They often bundle storage, extraction, embeddings, retrieval, ranking, packaging, scope, and observability into one opinionated backend. That can be useful for prototypes, but it creates lock-in at exactly the layer where developers need flexibility.

AI memory infrastructure should be modular because the whole stack keeps changing:

- better embedding models ship
- extraction models improve
- reranking strategies evolve
- applications need different trust and correction rules
- teams need different scope, privacy, and isolation models
- developers need to swap providers without rewriting their products

Atomic Strata is built around a simple idea:

**Applications should not be permanently wired to one memory vendor, one model stack, or one theory of context.**

## Atomic Memory

**Atomic Memory** is our open-source Developer Preview for configurable AI context memory.

It includes two layers:

### AtomicMemory SDK

A typed portability layer for AI applications and agents.

The SDK gives application code one memory interface across backends, including AtomicMemory Core, Mem0, or custom providers. Your application integrates with the interface, not a single vendor.

Use the SDK when you want:

- one memory interface across providers
- backend portability
- a typed API for ingest, search, package, list, get, and delete
- a clean provider boundary for custom memory systems
- application code that can survive changes in the memory backend

### AtomicMemory Core

A self-hosted HTTP memory engine for deployed AI systems.

Core runs on your infrastructure, uses Postgres + pgvector by default, and exposes memory operations through plain HTTP. It is designed for teams that want to own, inspect, and configure the memory layer itself.

Use Core when you want:

- self-hosted memory infrastructure
- no required hosted control plane
- configurable model and storage surfaces
- inspectable retrieval and context assembly
- explicit user and workspace scope
- memory that can be corrected, superseded, and versioned over time

## What is included

Atomic Memory currently includes:

- **AUDN mutation model** — add, update, delete, supersede, clarify, and no-op decisions for memory that changes over time.
- **Contradiction-aware claim versioning** — old claims can be superseded without erasing lineage.
- **Structured observability envelope** — search responses can expose retrieval, packaging, and assembly traces.
- **First-class scope** — user and workspace scopes, with agent visibility enforced below the application layer.
- **Pluggable model providers** — support for OpenAI, OpenAI-compatible endpoints, Anthropic, Google, Groq, Ollama, local transformers.js, Voyage, and other providers depending on model surface.
- **Pluggable storage contracts** — Postgres + pgvector by default, with narrow interfaces designed for future backend replacement.
- **Shared MCP server** — one tool contract for coding agents: `memory_search`, `memory_ingest`, `memory_package`, and `memory_list`.
- **Coding-agent integrations** — Claude Code, Codex, OpenClaw, Hermes, and Cursor.
- **Framework integrations** — Vercel AI SDK and OpenAI Agents SDK through local/source install paths; LangChain JS, LangGraph JS, and Mastra planned.
- **AtomicBench** — a benchmark harness for memory failure classes, not just recall scores.

## Architecture

Atomic Memory is designed around two forms of portability.

### 1. Application portability

Your application talks to the SDK interface.

That means your product can point to AtomicMemory Core, Mem0, or a custom provider without rewriting application code.

### 2. Infrastructure portability

AtomicMemory Core separates the internal memory stack into configurable surfaces.

Extraction, mutation, embeddings, retrieval, packaging, scope, observability, and evaluation should be replaceable over time. The memory engine should be able to absorb better models, better retrieval methods, and better infrastructure without forcing a full application rewrite.

## Quickstart

Run AtomicMemory Core locally:

```bash
git clone https://github.com/atomicstrata/atomicmemory-core.git
cd atomicmemory-core
cp .env.example .env
docker compose up -d --build
```

Verify the server:

```bash
curl http://localhost:3050/v1/memories/health
```

Install the SDK:

```bash
pnpm add @atomicmemory/atomicmemory-sdk
```

> Package names may change during Developer Preview. Check the docs for the latest install instructions.

## Example

```ts
import { MemoryClient } from '@atomicmemory/atomicmemory-sdk';

const memory = new MemoryClient({
  providers: {
    atomicmemory: {
      apiUrl: 'http://localhost:3050',
    },
  },
});

await memory.initialize();

await memory.ingest({
  mode: 'messages',
  messages: [
    {
      role: 'user',
      content: 'I prefer aisle seats when I travel.',
    },
  ],
  scope: {
    user: 'demo-user',
  },
});

const results = await memory.search({
  query: 'What is my seat preference?',
  scope: {
    user: 'demo-user',
  },
});

console.log(results);
```

## Repositories

- **AtomicMemory Core** — self-hosted HTTP memory engine  
  https://github.com/atomicstrata/atomicmemory-core

- **AtomicMemory SDK** — typed memory interface and provider abstraction  
  https://github.com/atomicstrata/atomicmemory-sdk

- **AtomicBench** — benchmark harness for memory failure classes  
  Coming soon / see docs

## Docs

Full documentation:

https://docs.atomicmemory.ai

## Roadmap

Atomic Strata is building toward a broader context memory stack:

1. **Open-source memory infrastructure** — AtomicMemory SDK and Core.
2. **Hosted AtomicMem products** — managed products built on the same core infrastructure.
3. **SuperNetic blockchain layer** — secure, auditable, composable, and portable context infrastructure for use cases that require stronger guarantees around ownership, access control, auditability, portability, and settlement.

## Feedback

We are especially interested in feedback on:

- the SDK API surface
- the provider abstraction
- self-hosted deployment
- correction and contradiction handling
- observability and context assembly traces
- local model workflows
- coding-agent integrations
- memory benchmark design
- memory failure cases we should add to AtomicBench

Open an issue, start a discussion, or build a provider.

## License

Atomic Memory is released under the Apache 2.0 license.

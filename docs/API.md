# HTTP API Reference

Base URL is `http://localhost:5001` in dev. Every endpoint returns JSON unless otherwise noted.

## Setup & Discovery

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/simulation/suggest-scenarios` | Scenario auto-suggest (Bull / Bear / Neutral) from a document preview |
| `GET` | `/api/simulation/trending` | Pull RSS/Atom items for the "What's Trending" panel |
| `POST` | `/api/simulation/ask` | Just Ask — synthesize a seed briefing from a question |
| `POST` | `/api/graph/fetch-url` | Fetch + extract text from a URL |
| `GET` | `/api/templates/list` | Preset templates |
| `GET` | `/api/templates/<id>?enrich=true` | Template + live FeedOracle enrichment |

## Graph Build (Step 1)

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/graph/ontology/generate` | NER + ontology extraction |
| `POST` | `/api/graph/build` | Build Neo4j graph from ontology |
| `GET` | `/api/graph/task/<task_id>` | Poll async task status |
| `GET` | `/api/graph/data/<graph_id>` | Paginated graph nodes + edges |
| `GET` | `/api/simulation/entities/<graph_id>` | Browse entities |
| `GET` | `/api/simulation/entities/<graph_id>/<uuid>` | Single entity + neighborhood |

## Simulation Lifecycle

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/simulation/create` | Create simulation from seed + prompt |
| `POST` | `/api/simulation/prepare` | Kick off profile generation (Step 2) |
| `POST` | `/api/simulation/prepare/status` | Poll Step 2 |
| `POST` | `/api/simulation/start` | Launch Wonderwall subprocess (Step 3) |
| `POST` | `/api/simulation/stop` | Terminate |
| `POST` | `/api/simulation/branch-counterfactual` | Fork with counterfactual injection |
| `POST` | `/api/simulation/fork` | Duplicate config |
| `POST` | `/api/simulation/<id>/director/inject` | Director mode — live event injection |
| `GET` | `/api/simulation/<id>/director/events` | List director events |

## Live State & Data

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/simulation/<id>/run-status` | Current round / totals |
| `GET` | `/api/simulation/<id>/run-status/detail` | Per-platform progress |
| `GET` | `/api/simulation/<id>/frame/<round>` | Compact per-round snapshot |
| `GET` | `/api/simulation/<id>/timeline` | Round-by-round summary |
| `GET` | `/api/simulation/<id>/actions` | Raw agent action log |
| `GET` | `/api/simulation/<id>/posts` | Paginated posts (Twitter + Reddit) |
| `GET` | `/api/simulation/<id>/profiles` | Agent personas |
| `GET` | `/api/simulation/<id>/profiles/realtime` | Live belief updates |
| `GET` | `/api/simulation/<id>/polymarket/markets` | Markets + current prices |
| `GET` | `/api/simulation/<id>/polymarket/market/<mid>/prices` | Price history |

## Analytics

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/simulation/<id>/belief-drift` | Stance drift per topic per round |
| `GET` | `/api/simulation/<id>/counterfactual` | Original vs branch comparison |
| `GET` | `/api/simulation/<id>/agent-stats` | Per-agent engagement + posting |
| `GET` | `/api/simulation/<id>/influence` | Influence leaderboard |
| `GET` | `/api/simulation/<id>/interaction-network` | Agent-to-agent graph |
| `GET` | `/api/simulation/<id>/demographics` | Archetype distribution |
| `GET` | `/api/simulation/<id>/quality` | Run health diagnostics |
| `POST` | `/api/simulation/compare` | Side-by-side belief comparison |

## Interaction

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/simulation/interview` | Chat with one agent |
| `POST` | `/api/simulation/interview/batch` | Ask a group in parallel |
| `POST` | `/api/simulation/<id>/agents/<name>/trace-interview` | Chat with full reasoning trace |
| `GET` | `/api/simulation/<id>/interviews/<name>` | Past transcripts with an agent |

## Publish / Embed / Export

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/simulation/<id>/publish` | Toggle `is_public` |
| `GET` | `/api/simulation/<id>/embed-summary` | Embed payload (public sims only) |
| `POST` | `/api/simulation/<id>/article` | Generate a Substack-style write-up |
| `GET` | `/api/simulation/<id>/export` | Full JSON export |
| `GET` | `/api/simulation/list` | List simulations |
| `GET` | `/api/simulation/history` | Simulation history / diffs |

## Report Agent

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/report/generate` | Launch ReACT report agent |
| `POST` | `/api/report/generate/status` | Poll generation |
| `GET` | `/api/report/<id>` | Full report |
| `GET` | `/api/report/by-simulation/<sim_id>` | Report for a simulation |
| `GET` | `/api/report/<id>/download` | PDF export |
| `POST` | `/api/report/chat` | Chat with report agent (re-queries graph) |
| `GET` | `/api/report/<id>/agent-log` | Full ReACT trace |
| `GET` | `/api/report/<id>/agent-log/stream` | SSE stream |
| `GET` | `/api/report/<id>/console-log` | Raw LLM call logs |

## Observability

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/observability/events/stream` | SSE feed |
| `GET` | `/api/observability/events` | Event log (paginated) |
| `GET` | `/api/observability/stats` | Aggregate stats |
| `GET` | `/api/observability/llm-calls` | LLM call history |

## Settings & Push

| Method | Path | Purpose |
|---|---|---|
| `GET` / `POST` | `/api/settings` | Runtime settings (masked keys) |
| `POST` | `/api/settings/test-llm` | Ping configured LLM |
| `GET` | `/api/simulation/push/vapid-public-key` | VAPID key for web push |
| `POST` | `/api/simulation/push/subscribe` | Register a browser subscription |
| `POST` | `/api/simulation/push/test` | Fire a test notification |

# Models

Four independent model slots (see [Configuration](CONFIGURATION.md#model-slots) for the env vars). This doc covers which models to put in which slot.

## Cloud presets (OpenRouter)

Two benchmarked presets ship in `.env.example`. Copy one and set your API key.

Each slot controls a different quality axis — benchmarked across 10+ model combos (full details in `models.md`):

| Slot | Controls | Key finding |
|---|---|---|
| **Default** | Persona richness, sim density | Haiku produces distinct 348-char voices; Gemini Flash produces generic 173-char copy |
| **Smart** | Report quality (#1 lever) | Claude Sonnet 9/10, Gemini 2.5 Flash 5/10, DeepSeek 2/10 |
| **NER** | Extraction reliability | gemini-2.0-flash reliable; flash-lite causes 3x retry bloat |
| **Wonderwall** | Cost (biggest consumer) | 850+ calls, 7M+ tokens. Verbosity matters more than $/M |

### Cheap mode — ~$1.20/run, ~13 min

All Gemini. Fast and reliable, but thin reports and generic personas.

| Slot | Model | $/M | Why |
|---|---|---|---|
| Default | `google/gemini-2.0-flash-001` | $0.10 | Fast, reliable JSON |
| Smart | `google/gemini-2.5-flash` | $0.30 | Adequate reports |
| NER | `google/gemini-2.0-flash-001` | $0.10 | No retry bloat |
| Wonderwall | `google/gemini-2.0-flash-lite-001` | $0.075 | Cheapest, least verbose |

### Best mode — ~$3.50/run, ~25 min

Claude reports, Haiku personas, cheap Wonderwall. Best report quality at reasonable cost.

| Slot | Model | $/M | Why |
|---|---|---|---|
| Default | `anthropic/claude-haiku-4.5` | $0.80/$4.00 | Rich personas, dense sim configs |
| Smart | `anthropic/claude-sonnet-4.6` | $3.00/$15.00 | 9/10 report quality, only ~19 calls |
| NER | `google/gemini-2.0-flash-001` | $0.10 | Proven reliable, no retries |
| Wonderwall | `google/gemini-2.0-flash-lite-001` | $0.075 | Wonderwall doesn't drive quality — Smart does |

> Both presets use `openai/text-embedding-3-small` for embeddings and `google/gemini-2.0-flash-001:online` for web research.

## Local mode (Ollama)

> **Context override required.** Ollama defaults to 4096 tokens, but MiroShark prompts need 10–30k. Create a custom Modelfile:
>
> ```bash
> printf 'FROM qwen3:14b\nPARAMETER num_ctx 32768' > Modelfile
> ollama create mirosharkai -f Modelfile
> ```

| Model | VRAM | Speed | Notes |
|---|---|---|---|
| `qwen2.5:32b` | 20GB+ | ~40 t/s | Default in `.env.example` — solid all-rounder |
| `qwen3:30b-a3b` *(MoE)* | 18GB | ~110 t/s | Fastest — MoE activates only 3B params per token |
| `qwen3:14b` | 12GB | ~60 t/s | Good balance for mid-range GPUs |
| `qwen3:8b` | 8GB | ~42 t/s | Minimum viable; drop Wonderwall rounds if context is tight |

### Hardware quick-pick

| Setup | Model |
|---|---|
| RTX 3090/4090 or M2 Pro 32GB+ | `qwen2.5:32b` |
| RTX 4080 / M2 Pro 16GB | `qwen3:30b-a3b` |
| RTX 4070 / M1 Pro | `qwen3:14b` |
| 8GB VRAM / laptop | `qwen3:8b` |

**Embeddings locally:** `ollama pull nomic-embed-text` — 768 dimensions, matches the Neo4j default.

## Hybrid mode

Most users land here naturally: run local for the high-volume simulation rounds, route to Claude for reports.

```bash
LLM_MODEL_NAME=qwen2.5:32b
SMART_PROVIDER=claude-code
SMART_MODEL_NAME=claude-sonnet-4-20250514
```

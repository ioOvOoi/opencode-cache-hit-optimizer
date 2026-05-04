# DeepSeek V4 Cache Notes

## Prefix Cache Model

DeepSeek API caching rewards exact prefix reuse. A later request can hit cache for a previous request prefix when the initial token sequence is unchanged.

Example:

- Request 1: `A + B` creates a cached prefix.
- Request 2: `A + B + C` can hit `A + B`.
- Request 3: `A + C` does not hit `A + B`, but the service may later extract common prefix `A`.

The practical rule is simple: keep the beginning of requests stable and append rather than rewrite.

## Usage Metrics

When available, inspect response `usage` fields:

- `prompt_cache_hit_tokens` — input tokens served from cache
- `prompt_cache_miss_tokens` — input tokens billed as uncached

Optimize for cost using hit/miss split, not only total context length. A longer mostly-hit prompt may be cheaper than a shorter mostly-miss prompt.

## Cache-Breaking Events

Treat these as likely cache-pool or prefix breaks:

- changing the model, such as V4 Pro ↔ V4 Flash
- changing provider, such as DeepSeek ↔ Fireworks ↔ NVIDIA NIM ↔ SGLang
- changing API key or account
- changing system prompt or configured instruction files
- switching to a thinking mode that injects extra system instructions
- editing earlier conversation messages or otherwise changing prefix content

## Thinking Mode Guidance

DeepSeek V4 thinking mode can affect cache behavior when it changes request-level instructions or output shape.

Recommended pattern:

| Work type | Mode | Cache note |
|---|---|---|
| Primary coding/planning | think-high | Stable quality/default choice |
| Simple search/exploration | non-think | Shorter and cheaper for subagents |
| Hard architecture/proof | think-max in a fresh session | Avoid mixing with other modes mid-session |

Do not switch thinking mode mid-session unless the current cache chain is no longer valuable.

## Context Window Is Not a License to Hoard

DeepSeek V4's large context window makes long tasks possible, but stale tool output still increases prompt size, distracts the model, and can reduce effective cache benefits after compaction. Preserve essential summaries and remove raw noise.

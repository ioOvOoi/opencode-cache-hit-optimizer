# DeepSeek V4 Cache Notes

## Prefix Cache Model

DeepSeek API 之 cache，贵在 exact prefix reuse。后一 request 若开头 token sequence 与前一 request prefix 不变，即可 hit cache。

例：

- Request 1：`A + B`，生 cached prefix。
- Request 2：`A + B + C`，可 hit `A + B`。
- Request 3：`A + C`，不 hit `A + B`；service 或后取 common prefix `A`。

实律甚简：request 之始须稳；宜 append，勿 rewrite。

## Usage Metrics

有 response `usage` fields 时，当观：

- `prompt_cache_hit_tokens` — input tokens served from cache
- `prompt_cache_miss_tokens` — input tokens billed as uncached

审 cost 须看 hit/miss split，勿只看 total context length。较长而 mostly-hit，可廉于较短而 mostly-miss。

## Cache-Breaking Events

下列事，多破 cache pool 或 prefix：

- 换 model，如 V4 Pro ↔ V4 Flash
- 换 provider，如 DeepSeek ↔ Fireworks ↔ NVIDIA NIM ↔ SGLang
- 换 API key 或 account
- 改 system prompt 或 configured instruction files
- 切 thinking mode，若其注入 extra system instructions
- 改 earlier conversation messages，或任意变 prefix content

## Thinking Mode Guidance

DeepSeek V4 thinking mode 若改 request-level instructions 或 output shape，即可害 cache。

建议：

| Work type | Mode | Cache note |
|---|---|---|
| Primary coding/planning | think-high | 稳定质量/default choice |
| Simple search/exploration | non-think | subagents 更短更廉 |
| Hard architecture/proof | think-max in a fresh session | 勿与他 mode 混于同 session |

除非当前 cache chain 已无价值，勿中途切 thinking mode。

## Context Window 非囤积许可

DeepSeek V4 context window 大，可任长事；然 stale tool output 仍增 prompt size、扰 model、且 compaction 后或损 cache benefit。宜存 essential summaries，去 raw noise。

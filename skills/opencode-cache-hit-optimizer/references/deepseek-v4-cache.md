# DeepSeek V4 Cache Notes

## Prefix Cache Model

DeepSeek API 之缓存（cache），贵在精确前缀复用（exact prefix reuse）。后一请求（request）若开头 token sequence 与前一请求前缀（request prefix）不变，即可缓存命中（hit cache）。

例：

- Request 1：`A + B`，生已缓存前缀（cached prefix）。
- Request 2：`A + B + C`，可命中（hit）`A + B`。
- Request 3：`A + C`，不命中（hit）`A + B`；服务（service）或后取共同前缀（common prefix）`A`。

实律甚简：请求（request）之始须稳；宜追加（append），勿重写（rewrite）。

## Usage Metrics

有响应（response）`usage` fields 时，当观：

- `prompt_cache_hit_tokens` — input tokens served from cache
- `prompt_cache_miss_tokens` — input tokens billed as uncached

审成本（cost）须看命中/未命中拆分（hit/miss split），勿只看总上下文长度（total context length）。较长而 mostly-hit，可廉于较短而 mostly-miss。

## Cache-Breaking Events

下列事，多破缓存池（cache pool）或前缀（prefix）：

- 换模型（model），如 V4 Pro ↔ V4 Flash
- 换提供方（provider），如 DeepSeek ↔ Fireworks ↔ NVIDIA NIM ↔ SGLang
- 换 API key 或 account
- 改 system prompt 或 configured instruction files
- 切思考模式（thinking mode），若其注入额外系统指令（extra system instructions）
- 改早期对话消息（earlier conversation messages），或任意变前缀内容（prefix content）

## Thinking Mode Guidance

DeepSeek V4 思考模式（thinking mode）若改请求级指令（request-level instructions）或输出形态（output shape），即可害缓存（cache）。

建议：

| Work type | Mode | Cache note |
|---|---|---|
| Primary coding/planning | think-high | 稳定质量（stable quality）/ default choice |
| Simple search/exploration | non-think | 子代理（subagents）更短更廉 |
| Hard architecture/proof | think-max in a fresh session | 勿与他模式（mode）混于同会话（session） |

除非当前缓存链（cache chain）已无价值，勿中途切思考模式（thinking mode）。

## Context Window 非囤积许可

DeepSeek V4 上下文窗口（context window）大，可任长事；然陈旧工具输出（stale tool output）仍增提示词大小（prompt size）、扰模型（model）、且压缩（compaction）后或损缓存收益（cache benefit）。宜存必要摘要（essential summaries），去原始噪声（raw noise）。

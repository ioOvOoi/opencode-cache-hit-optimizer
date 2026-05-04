---
name: opencode-cache-hit-optimizer
description: 用于优化 DeepSeek V4 prompt cache hits；适用于 stable prompts、AGENTS.md、/new、/compact、subagents、thinking modes、provider/model switching、cache hit/miss cost analysis。
---

# OpenCode Cache Hit Optimizer

专治一事：增 DeepSeek V4 prompt cache hit。法在守 request prefix 之稳，分 noisy exploration 与 long-lived task context，并视 model / provider / thinking mode 变更为 cache-breaking events。

总律：**先保 stable prefix；次减 context；parallelize 必问 cache trade-off。**

## 何时用

遇下列事，乃用：

- DeepSeek V4 cache hit / cache miss optimization
- OpenCode 或 Atlas long-session strategy
- `AGENTS.md`、`instructions`、system-prompt stability
- `/new`、`/compact`、Magic Context、DCP、compaction behavior
- subagent fan-out、exploration isolation、model-tier routing
- thinking mode：non-think、think-high、think-max
- 以 `prompt_cache_hit_tokens`、`prompt_cache_miss_tokens` 审 cost

若仅泛数 tokens，而不涉 cache-prefix behavior，勿用。

## Cache-First 决策流

1. **识 stable prefix。** 看 repeated requests 之首：system prompt、`AGENTS.md`、configured instruction files、persistent memory、early conversation。
2. **避 prefix churn。** 勿轻改 stable instruction files；勿中途换 model / provider / API key / thinking mode，除非收益胜 cache loss。
3. **择 session shape。** 独立任务用 `/new`，复用同一 startup prefix；旧 turns 仍有证据或决策，则 continue。
4. **隔 exploration。** broad search 或 noisy investigation 用 subagents，保 main session clean。
5. **慎 compact。** 先 local pruning 或 Magic Context-style reduction；full `/compact` 会自 summary 点后重塑 prefix。
6. **以 usage 验。** 有 `prompt_cache_hit_tokens` / `prompt_cache_miss_tokens`，则依 hit ratio 与 cost impact，不只看 total tokens。

## OpenCode / Atlas 法

| 情形 | 宜 | 忌 |
|---|---|---|
| Stable project rules | 置 concise durable rules 于 `AGENTS.md` 或 configured `instructions` | 每 prompt 重贴 project context |
| 新独立任务 | `/new`，用同 stable instruction prefix | 续 polluted long conversation |
| 同任务有积累证据 | continue current session | 重开而失 useful cached prefix chain |
| broad file search / repo survey | 委 `@explore` 或 read-only subagent | 主会话塞 raw search output |
| noisy tool output | 摘 findings 后 reduce / drop | 巨量 output 久留 main context |
| repeated status replies | 用稳定短式，如 `wenyan-lite` | 同一状态每回换说法 |
| emergency confusion | `/compact` 或 `/new` | 对 confused context 反复补 prompt |

Atlas 宜以 project files 存 planning state，只返 concise phase summaries 于 main conversation。如此既存 task continuity，又不令 raw findings 反复入 prompt prefix。

本文文言简式，非 tone-control。其用唯二：短、稳、少 volatile tail。勿扩此 skill 为通用写作风格。

## DeepSeek V4 Thinking Mode 律

- 一 session 尽量守同一 thinking mode。
- 主 coding / planning，质量要紧，宜 **think-high**。
- 简 search / exploration，或 cheap subagent，宜 **non-think**。
- **think-max** 宜另开 session；其或改 system prompt content，破 prefix matching。
- model / provider / API key 变，皆视为 separate cache pools。

## Subagent 取舍

Subagents 护 main context，然多从 fresh context 起。用之于：

- exploration 将注入多 irrelevant tool outputs
- 多 independent searches 可并行
- cheap models 如 DeepSeek V4 Flash 足任其事
- 只需 concise result 回 main agent

若任务重依 current conversation prefix，且 inline 更廉，则勿滥用 subagent。

## 验证

称 cache optimization 成前，先查：

1. model / provider / API key / thinking mode 是否恒定。
2. stable instruction files 是否改动。
3. old noisy outputs 是否 reduce 或 isolate。
4. API usage fields（若有）：
   - `prompt_cache_hit_tokens`
   - `prompt_cache_miss_tokens`
5. 报 trade-off：hit ratio、cache miss risk、context cleanliness、expected cost impact。

## 参考

- `references/deepseek-v4-cache.md` — DeepSeek V4 cache mechanics 与 thinking-mode implications
- `references/opencode-cache-practices.md` — OpenCode / Atlas session patterns
- `references/deepseek-tui-lessons.md` — DeepSeek-TUI 经验，取其 cache optimization 义

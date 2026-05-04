# OpenCode Cache Practices

## Stable Startup Prefix

Durable high-signal project instructions，宜置于 session start 附近之稳定文件：

- `AGENTS.md`
- configured `instructions` files
- compact global agent instructions

此类文件宜 concise。频改 startup instructions，则 cross-session prefix reuse 必减。

## `/new` vs Continue

新任务独立，且仅需同一 stable startup prefix，则用 `/new`。

若 previous turns 含必要 decisions、evidence、partial work，则 continue current session。

决策：

- 需 old conversation details → continue。
- 仅需 project rules + fresh task → `/new`。
- current context noisy 或 confused → `/compact` 或 `/new`，视 summary 是否仍有值。

## `/compact` Strategy

Full compaction 以 summary 代 accumulated conversation，自该点后生新 prefix shape。只于 context 过大或混乱时用；勿以其应付普通增长。

优先序：

1. Drop / reduce large completed tool outputs。
2. Durable task state 移入 project files（若合宜）。
3. 入下一 phase 前，summarize findings。
4. Local cleanup 不足，方用 `/compact`。

## Subagent Isolation

Subagents cache-expensive，然 context-protective。用于 broad、independent、noisy work：

- repository exploration
- web research
- parallel file search
- independent hypothesis checks

只返 concise findings 于 main session。勿将 raw subagent logs 合回 primary context。

## Atlas Pattern

Atlas 宜 file-backed planning：

- `task_plan.md` 存 plan
- `findings.md` 存 evidence 与 paths
- `progress.md` 存 session log 与 verification

此法免反复发送 large planning context，且经 compaction 或 new session 后仍可恢复。

## Stable Terse Output Pattern

短而重复之 replies，可减 long session 中 noisy tail growth。`wenyan-lite` 只在其为 stable terse-output pattern 时有益：

- across turns 保同 compact response shape
- code、paths、commands、metrics、warnings 必 exact
- 细节非必需时，只报 decision、cache reason、next step
- 若 compression 会藏 risk、ordering、verification evidence，则止用 terse style

Cache framing：

- 善：phase-boundary replies 恒用同一短结构。
- 恶：每 turn 新造文体，徒增 prompt churn。

此非 general style guidance。terse modes 只为 improve cache locality 或 reduce low-value context growth。

## Cost Review Checklist

审 OpenCode session cache health，当问：

- stable instruction files 是否变？
- agent 是否换 model / provider / API key？
- agent 是否换 thinking mode？
- exploratory outputs 是否留在 main session 外？
- completed phases 后是否 reduction？
- usage metrics 中 `prompt_cache_hit_tokens` 是否高于 misses？

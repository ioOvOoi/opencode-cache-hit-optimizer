# OpenCode Cache Practices

## Stable Startup Prefix

持久高信号项目指令（durable high-signal project instructions），宜置于会话起点（session start）附近之稳定文件：

- `AGENTS.md`
- configured `instructions` files
- compact global agent instructions

此类文件宜精简（concise）。频改启动指令（startup instructions），则跨会话前缀复用（cross-session prefix reuse）必减。

## `/new` vs Continue

新任务独立，且仅需同一稳定启动前缀（stable startup prefix），则用 `/new`。

若前文轮次（previous turns）含必要决策（decisions）、证据（evidence）、未完工作（partial work），则续当前会话（continue current session）。

决策：

- 需旧对话细节（old conversation details）→ continue。
- 仅需项目规则（project rules）+ fresh task → `/new`。
- 当前上下文（current context）noisy 或 confused → `/compact` 或 `/new`，视摘要（summary）是否仍有值。

## `/compact` Strategy

全量压缩（full compaction）以摘要（summary）代累积对话（accumulated conversation），自该点后生新前缀形态（prefix shape）。只于上下文（context）过大或混乱时用；勿以其应付普通增长。

优先序：

1. Drop / reduce large completed tool outputs。
2. 持久任务状态（durable task state）移入项目文件（project files）（若合宜）。
3. 入下一阶段（phase）前，summarize findings。
4. 本地清理（local cleanup）不足，方用 `/compact`。

## Subagent Isolation

子代理（subagents）cache-expensive，然 context-protective。用于 broad、independent、noisy work：

- repository exploration
- web research
- parallel file search
- independent hypothesis checks

只返精简发现（concise findings）于主会话（main session）。勿将原始子代理日志（raw subagent logs）合回主上下文（primary context）。

## Atlas Pattern

Atlas 宜文件化规划（file-backed planning）：

- `task_plan.md` 存 plan
- `findings.md` 存 evidence 与 paths
- `progress.md` 存 session log 与 verification

此法免反复发送大型规划上下文（large planning context），且经压缩（compaction）或新会话（new session）后仍可恢复。

## Stable Terse Output Pattern

短而重复之回复（replies），可减长会话（long session）中噪声尾部增长（noisy tail growth）。`wenyan-lite` 只在其为稳定短输出模式（stable terse-output pattern）时有益：

- across turns 保同精简响应形态（compact response shape）
- code、paths、commands、metrics、warnings 必精确（exact）
- 细节非必需时，只报决策（decision）、缓存理由（cache reason）、次步（next step）
- 若压缩（compression）会藏风险（risk）、顺序（ordering）、验证证据（verification evidence），则止用短式风格（terse style）

Cache framing：

- 善：阶段边界回复（phase-boundary replies）恒用同一短结构。
- 恶：每 turn 新造文体，徒增提示词扰动（prompt churn）。

此非通用风格指导（general style guidance）。短式模式（terse modes）只为改进缓存局部性（cache locality）或减少低价值上下文增长（low-value context growth）。

## Cost Review Checklist

审 OpenCode 会话缓存健康（session cache health），当问：

- 稳定指令文件（stable instruction files）是否变？
- 代理（agent）是否换模型（model）/ 提供方（provider）/ API key？
- 代理（agent）是否换思考模式（thinking mode）？
- 探索输出（exploratory outputs）是否留在主会话（main session）外？
- 已完成阶段（completed phases）后是否缩减（reduction）？
- 用量指标（usage metrics）中 `prompt_cache_hit_tokens` 是否高于未命中（misses）？

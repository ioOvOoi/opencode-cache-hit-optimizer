# DeepSeek-TUI Lessons for OpenCode

DeepSeek-TUI 可作设计参照：其围绕 DeepSeek V4 1M-token 上下文（context）、前缀缓存（prefix cache）、思考模式流式输出（thinking-mode streaming）、cheap Flash 扇出（fan-out）而成。取其义，勿移其全运行时（runtime）。

## 可取之法

### Show Cost, Not Just Tokens

DeepSeek-TUI 显用量（usage）与成本（cost）。OpenCode 代理（agents）亦当在数据（data）可得时，观缓存命中 vs 未命中 tokens（cache hit vs miss tokens）。原始 token 计数（raw token count）无命中/未命中拆分（hit/miss split），则不全。

### Compress With Prefix Awareness

自动压缩（automatic compaction）应尽量保稳定早期上下文（stable early context）。若压缩（compaction）大改提示词形态（prompt shape），虽省上下文长度（context length），亦可损缓存复用（cache reuse）。

### Use Cheap Fan-Out Deliberately

DeepSeek-TUI 之 RLM pattern，以 cheap Flash children 并行。OpenCode 对应法：子代理（subagents）做独立探索（independent exploration）。此增吞吐（throughput），护主上下文（main context）；然 each child 或缓存复用（cache reuse）差。

可 fan-out 于：

- 任务独立（tasks independent）
- children 可用 cheaper models 或 non-think mode
- 结果可精简摘要（compactly summarized）
- 主会话上下文洁净度（main-session context cleanliness）重于 child 缓存命中（child cache hits）

### Separate Interaction Risk Levels

DeepSeek-TUI 有 Plan、Agent、YOLO 模式（modes）。译入 OpenCode / Atlas：

- Plan-like：read-only exploration，先 proposal 后 edits
- Agent-like：normal approval-gated execution
- YOLO-like：仅 trusted、low-risk automation，且 rollback 清楚

缓存优化（cache optimization）不可压过安全（safety）。勿为保缓存命中（cache hits）而留污染或高风险会话（polluted or risky session）。

## 勿过用

- 勿因 1M 上下文（context）即保留 all raw tool output。
- 单次聚焦读取（single focused read）足矣时，勿多生子代理（subagents）。
- UI 允许切模型（model）/ 思考模式（thinking mode），不代表可中途切。
- 勿以缓存命中率（cache hit rate）牺牲正确答案（correct answers）或安全编辑（safe edits）。

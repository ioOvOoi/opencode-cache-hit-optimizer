# DeepSeek-TUI Lessons for OpenCode

DeepSeek-TUI 可作设计参照：其围绕 DeepSeek V4 1M-token context、prefix cache、thinking-mode streaming、cheap Flash fan-out 而成。取其义，勿移其全 runtime。

## 可取之法

### Show Cost, Not Just Tokens

DeepSeek-TUI 显 usage 与 cost。OpenCode agents 亦当在 data 可得时，观 cache hit vs miss tokens。raw token count 无 hit/miss split，则不全。

### Compress With Prefix Awareness

Automatic compaction 应尽量保 stable early context。若 compaction 大改 prompt shape，虽省 context length，亦可损 cache reuse。

### Use Cheap Fan-Out Deliberately

DeepSeek-TUI 之 RLM pattern，以 cheap Flash children 并行。OpenCode 对应法：subagents 做 independent exploration。此增 throughput，护 main context；然 each child 或 cache reuse 差。

可 fan-out 于：

- tasks independent
- children 可用 cheaper models 或 non-think mode
- results 可 compactly summarized
- main-session context cleanliness 重于 child cache hits

### Separate Interaction Risk Levels

DeepSeek-TUI 有 Plan、Agent、YOLO modes。译入 OpenCode / Atlas：

- Plan-like：read-only exploration，先 proposal 后 edits
- Agent-like：normal approval-gated execution
- YOLO-like：仅 trusted、low-risk automation，且 rollback 清楚

Cache optimization 不可压过 safety。勿为保 cache hits 而留 polluted 或 risky session。

## 勿过用

- 勿因 1M context 即保留 all raw tool output。
- single focused read 足矣时，勿多生 subagents。
- UI 允许切 model / thinking mode，不代表可中途切。
- 勿以 cache hit rate 牺牲 correct answers 或 safe edits。

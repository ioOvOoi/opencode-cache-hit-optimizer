# DeepSeek-TUI Lessons for OpenCode

DeepSeek-TUI is useful as a design reference because it is built around DeepSeek V4's 1M-token context, prefix cache, thinking-mode streaming, and cheap Flash fan-out. Adapt the ideas, not the entire runtime.

## Lessons to Apply

### Show Cost, Not Just Tokens

DeepSeek-TUI surfaces usage and cost. OpenCode agents should similarly reason about cache hit vs miss tokens when data is available. A raw token count is incomplete without hit/miss split.

### Compress With Prefix Awareness

Automatic compaction should preserve stable early context when possible. If compaction rewrites too much of the prompt shape, it may reduce cache reuse even while saving context length.

### Use Cheap Fan-Out Deliberately

DeepSeek-TUI's RLM pattern uses cheap Flash children for parallel work. The OpenCode equivalent is using subagents for independent exploration. This improves throughput and protects the main context, but each child may have poor cache reuse.

Apply fan-out when:

- tasks are independent
- children can use cheaper models or non-think mode
- results can be summarized compactly
- main-session context cleanliness matters more than child cache hits

### Separate Interaction Risk Levels

DeepSeek-TUI has Plan, Agent, and YOLO modes. Translate this into OpenCode/Atlas behavior:

- Plan-like: read-only exploration and proposal before edits
- Agent-like: normal approval-gated execution
- YOLO-like: only for trusted, low-risk automation with clear rollback

Cache optimization must not override safety. Do not keep a polluted or risky session merely to preserve cache hits.

## Lessons Not to Over-Apply

- Do not assume 1M context means all raw tool output should stay in prompt.
- Do not spawn many subagents when a single focused read is enough.
- Do not switch models or thinking modes mid-session just because the UI allows it.
- Do not optimize cache hit rate at the cost of wrong answers or unsafe edits.

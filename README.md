# opencode-cache-hit-optimizer

An Agent Skill for improving DeepSeek V4 prompt cache hit rates in OpenCode-style coding sessions.

The skill focuses on one problem: **keeping request prefixes stable enough for DeepSeek's prompt cache to keep working while an agent explores, edits, compacts, and delegates work.**

## Why This Exists

DeepSeek V4 exposes large context windows and low-cost cached input, but cache savings depend on exact prefix reuse. Agent workflows can accidentally destroy that reuse by changing system prompts, switching models or thinking modes, compacting too aggressively, or flooding the main session with exploratory tool output.

This skill gives agents a cache-first decision framework for:

- preserving stable instruction prefixes
- choosing between continuing a session and starting a new one
- deciding when `/compact` helps or hurts
- isolating noisy exploration in subagents
- keeping thinking mode, model, provider, and API-key choices cache-aware
- reviewing `prompt_cache_hit_tokens` and `prompt_cache_miss_tokens` when available

## Skill Layout

```text
skills/
  opencode-cache-hit-optimizer/
    SKILL.md
    references/
      deepseek-v4-cache.md
      opencode-cache-practices.md
      deepseek-tui-lessons.md
```

## Installation

### OpenCode-style local skill directory

Install the repository wherever local skills or vendored skills are kept, then expose the skill directory that contains `SKILL.md`.

Example using a vendor directory plus a minimal wrapper:

```text
<opencode-config>/
  vendor/
    opencode-cache-hit-optimizer/
      skills/opencode-cache-hit-optimizer/SKILL.md
  skills/
    opencode-cache-hit-optimizer/
      SKILL.md
```

The wrapper can stay small and point agents to the vendored upstream skill source. Avoid hard-coding personal machine paths in published wrappers or docs.

### DeepSeek-TUI

DeepSeek-TUI supports installing community skills from GitHub repositories:

```text
/skill install github:ioOvOoi/opencode-cache-hit-optimizer
```

This repository uses the multi-skill layout `skills/<name>/SKILL.md`.

## When to Load the Skill

Load `opencode-cache-hit-optimizer` for questions or tasks involving:

- maximizing DeepSeek V4 cache hits
- reducing cache misses in OpenCode or Atlas-like agents
- deciding whether to use `/new`, continue, or `/compact`
- structuring `AGENTS.md` or instruction files for stable prefixes
- deciding whether subagents improve or harm cache efficiency
- comparing DeepSeek-TUI cache-related design patterns with OpenCode workflows
- explaining cost differences between cache-hit and cache-miss input

Do not load it for generic context-window advice unless prefix-cache behavior changes the decision.

## Included References

- `deepseek-v4-cache.md` — DeepSeek V4 prefix-cache mechanics, usage metrics, and thinking-mode implications
- `opencode-cache-practices.md` — OpenCode / Atlas session-shaping patterns
- `deepseek-tui-lessons.md` — DeepSeek-TUI design lessons adapted for OpenCode cache optimization

## Design Principles

1. **Stable prefixes beat clever compression.** Keep startup instructions small, durable, and consistent.
2. **Append when continuity matters.** Continue a session when previous turns are useful cached prefix.
3. **Start fresh when tasks diverge.** Use a new session for independent work that only needs stable project instructions.
4. **Isolate noisy exploration.** Subagents can protect the main session even if child calls have worse cache reuse.
5. **Measure hit/miss, not just tokens.** A larger mostly cached prompt can be cheaper than a smaller uncached one.

## Related Inspiration

- DeepSeek V4 prompt-cache behavior and 1M-token context design
- DeepSeek-TUI's cache-aware terminal agent patterns: cost tracking, compaction, thinking-mode controls, and cheap Flash fan-out
- OpenCode / Atlas workflows that separate stable instructions, task state, exploration, and verification

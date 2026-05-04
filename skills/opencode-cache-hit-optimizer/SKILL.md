---
name: opencode-cache-hit-optimizer
description: Use when optimizing DeepSeek V4 prompt cache hits in OpenCode or Atlas sessions, especially for stable prompts, AGENTS.md, /new, /compact, subagents, thinking modes, provider/model switching, or cache hit/miss cost analysis.
---

# OpenCode Cache Hit Optimizer

## Overview

Maximize DeepSeek V4 prompt cache hits by keeping request prefixes stable, separating exploratory work from long-lived task context, and treating model/provider/thinking-mode changes as cache-breaking events.

Core rule: **preserve stable prefixes first; reduce context second; parallelize only when the cache trade-off is worth it.**

## When to Use

Use this skill when working on:

- DeepSeek V4 cache hit / cache miss optimization
- OpenCode or Atlas long-session strategy
- `AGENTS.md`, `instructions`, or system-prompt stability
- `/new`, `/compact`, Magic Context, DCP, or compaction behavior
- subagent fan-out, exploration isolation, or model-tier routing
- thinking-mode selection such as non-think, think-high, or think-max
- cost review involving `prompt_cache_hit_tokens` and `prompt_cache_miss_tokens`

Do not use for generic token counting unless cache-prefix behavior affects the decision.

## Cache-First Decision Flow

1. **Identify the stable prefix.** Check which content appears at the start of repeated requests: system prompt, `AGENTS.md`, configured instruction files, persistent memory, and the early conversation.
2. **Avoid prefix churn.** Do not edit stable instruction files, switch models/providers/API keys, or change thinking mode mid-session unless the benefit outweighs cache loss.
3. **Choose session shape.** Use `/new` for independent tasks that can reuse the same stable startup prefix. Continue the current session when the previous turns are needed as prefix.
4. **Isolate exploration.** Use subagents for broad search or noisy investigation when preserving the main session matters more than subagent cache reuse.
5. **Compact deliberately.** Prefer local pruning or Magic Context-style reduction before full `/compact`. Full compaction resets the prefix after the summary point.
6. **Verify with usage metrics.** Inspect `prompt_cache_hit_tokens` and `prompt_cache_miss_tokens` when available; optimize based on hit ratio and dollar impact, not total tokens alone.

## OpenCode / Atlas Practices

| Situation | Prefer | Avoid |
|---|---|---|
| Stable project rules | Put concise durable rules in `AGENTS.md` or configured `instructions` | Re-pasting the same project context into every prompt |
| New independent task | Start `/new` with the same stable instruction prefix | Continuing a polluted long conversation |
| Same task with accumulated evidence | Continue current session | Starting over and losing useful cached prefix chain |
| Broad file search or repo survey | Delegate to `@explore` / read-only subagent | Filling the main session with raw search output |
| Noisy tool output | Reduce/drop old tool outputs after extracting findings | Keeping huge outputs in main context indefinitely |
| Repeated status replies | Use a stable terse format when enough, e.g. `wenyan-lite`-style short summaries | Rephrasing the same status in many new ways |
| Emergency confusion | `/compact` or `/new` | Repeatedly appending corrective prompts to a confused context |

For Atlas specifically, keep planning state in project files and return concise phase summaries to the main conversation. This preserves task continuity without forcing every raw finding into the prompt prefix.

Treat terse modes such as `wenyan-lite` only as stable output shapes that slow volatile tail growth. Do not expand this skill into a general writing-style or tone-control skill.

## DeepSeek V4 Thinking Mode Rules

- Keep one thinking mode for the whole session when possible.
- Prefer **think-high** for primary coding/planning sessions when quality matters.
- Prefer **non-think** for cheap exploratory subagents or simple retrieval work.
- Reserve **think-max** for separate sessions because it can alter system prompt content and break prefix matching.
- Treat model/provider/API-key changes as separate cache pools.

## Subagent Trade-Off

Subagents protect the main context but usually start fresh. Use them when:

- exploration would inject many irrelevant tool outputs into the main session
- multiple independent searches can run in parallel
- cheap models such as DeepSeek V4 Flash can handle the work
- only a concise result needs to return to the main agent

Avoid unnecessary subagents when the task depends heavily on the current conversation prefix and the result would be cheaper to handle inline.

## Verification

Before claiming cache optimization success:

1. Check whether the model/provider/API key/thinking mode stayed constant.
2. Check whether stable instruction files changed.
3. Check whether old noisy outputs were reduced or isolated.
4. Check API usage fields when available:
   - `prompt_cache_hit_tokens`
   - `prompt_cache_miss_tokens`
5. Report the trade-off: hit ratio, cache miss risk, context cleanliness, and expected cost impact.

## Additional Resources

- `references/deepseek-v4-cache.md` — DeepSeek V4 cache mechanics and thinking-mode implications
- `references/opencode-cache-practices.md` — OpenCode / Atlas session patterns
- `references/deepseek-tui-lessons.md` — DeepSeek-TUI design lessons adapted for OpenCode

# OpenCode Cache Practices

## Stable Startup Prefix

Put durable, high-signal project instructions in stable files loaded near session start:

- `AGENTS.md`
- configured `instructions` files
- compact global agent instructions

Keep these files concise. Frequent edits to startup instructions reduce cross-session prefix reuse.

## `/new` vs Continue

Use `/new` when the next task is independent and can benefit from the same stable startup prefix.

Continue the current session when previous turns contain necessary decisions, evidence, or partially completed work.

Decision rule:

- Need old conversation details → continue.
- Need only project rules and a fresh task → `/new`.
- Current context is noisy or confused → `/compact` or `/new` depending on whether the summary is still valuable.

## `/compact` Strategy

Full compaction replaces the accumulated conversation with a summary and creates a new prefix shape after that point. Use it when context is too large or confused, not as the first response to ordinary growth.

Prefer this order:

1. Drop or reduce large completed tool outputs.
2. Move durable task state into project files when appropriate.
3. Summarize phase findings before moving on.
4. Use `/compact` when local cleanup is insufficient.

## Subagent Isolation

Subagents are cache-expensive but context-protective. Use them for broad, independent, noisy work:

- repository exploration
- web research
- parallel file search
- independent hypothesis checks

Return concise findings to the main session. Do not merge raw subagent logs back into the primary context.

## Atlas-Specific Pattern

Atlas benefits from file-backed planning:

- keep task plans in `.opencode/atlas/task_plan.md`
- keep findings in `.opencode/atlas/findings.md`
- keep progress in `.opencode/atlas/progress.md`

This pattern avoids repeatedly re-sending large planning context while preserving recoverability across compaction or new sessions.

## Cost Review Checklist

When reviewing an OpenCode session for cache health, check:

- Did stable instruction files change?
- Did the agent switch model/provider/API key?
- Did the agent switch thinking mode?
- Did exploratory outputs stay out of the main session?
- Did reductions happen after completed phases?
- Do usage metrics show high `prompt_cache_hit_tokens` relative to misses?

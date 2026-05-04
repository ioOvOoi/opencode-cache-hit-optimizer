# opencode-cache-hit-optimizer

此 Agent Skill，专治一事：**增 DeepSeek V4 于 OpenCode 式会话中之 prompt cache hit**。

要义：守 request prefix 之稳，使 agent 探索、编辑、`/compact`、委派 subagent 时，prompt cache 犹可续用。

## 何以有此

DeepSeek V4 有大 context window，亦有低价 cached input。然 cache 省费，系于 exact prefix reuse。若 system prompt 屡变、model / provider / thinking mode 中途换、`/compact` 过猛，或主会话灌入大量 noisy tool output，则 cache 易断。

此 skill 给 agent 一 cache-first 法：

- 守 stable instruction prefix
- 判 continue、`/new`、`/compact` 何者宜
- 以 subagent 隔 noisy exploration
- 使 thinking mode、model、provider、API key 选择知 cache 利害
- 有 usage 时，观 `prompt_cache_hit_tokens` 与 `prompt_cache_miss_tokens`

## 目录

```text
skills/
  opencode-cache-hit-optimizer/
    SKILL.md
    references/
      deepseek-v4-cache.md
      opencode-cache-practices.md
      deepseek-tui-lessons.md
```

## 安装

### OpenCode 式本地 skill

置 repo 于 local skill 或 vendor 所在处，再暴露含 `SKILL.md` 之 skill 目录。

例：vendor + wrapper：

```text
<opencode-config>/
  vendor/
    opencode-cache-hit-optimizer/
      skills/opencode-cache-hit-optimizer/SKILL.md
  skills/
    opencode-cache-hit-optimizer/
      SKILL.md
```

wrapper 可简，然须可独立执行；可注明 vendored upstream path。勿在公开文档写个人机器路径。

### DeepSeek-TUI

DeepSeek-TUI 可从 GitHub 安装 community skill：

```text
/skill install github:ioOvOoi/opencode-cache-hit-optimizer
```

本 repo 用 multi-skill layout：`skills/<name>/SKILL.md`。

## 何时加载

遇下列事，载 `opencode-cache-hit-optimizer`：

- 求最大化 DeepSeek V4 cache hits
- 降 OpenCode / Atlas cache misses
- 判 `/new`、continue、`/compact`
- 设计 `AGENTS.md` 或 instruction files，使 prefix 稳
- 判 subagent 是护 cache，抑或增耗
- 比 DeepSeek-TUI cache-aware 设计与 OpenCode workflow
- 解释 cache-hit 与 cache-miss input 成本差

若仅泛谈 context window，而不涉 prefix-cache 行为，勿载。

## 参考

- `deepseek-v4-cache.md` — DeepSeek V4 prefix cache、usage metrics、thinking mode 影响
- `opencode-cache-practices.md` — OpenCode / Atlas session-shaping patterns
- `deepseek-tui-lessons.md` — DeepSeek-TUI 经验，取其 cache optimization 义

## 设计律

1. **Stable prefix 胜 clever compression。** Startup instructions 宜短、稳、久。
2. **需 continuity，则 append。** 旧 turns 有用，留其 cached prefix chain。
3. **事异则新。** 独立任务用 `/new`，复用稳定 project instructions。
4. **Noisy exploration 外置。** subagents 可护 main session，即使 child cache reuse 较差。
5. **量 hit/miss，勿只数 tokens。** 较长而多 hit，或比短而多 miss 廉。

## 风格

本文采文言简式；技术术语保留现代中文 + English。此风格只为稳定短输出与低噪声 tail，不作通用文风 skill。

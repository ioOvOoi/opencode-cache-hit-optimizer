# opencode-cache-hit-optimizer

此 Agent Skill，专治一事：**增 DeepSeek V4 于 OpenCode 式会话中之提示词缓存命中（prompt cache hit）**。

要义：守请求前缀（request prefix）之稳，使代理（agent）探索、编辑、`/compact`、委派子代理（subagent）时，提示词缓存（prompt cache）犹可续用。

## 术语

- 提示词缓存（prompt cache）
- 请求前缀（request prefix）/ 前缀（prefix）
- 缓存命中（cache hit）/ 缓存未命中（cache miss）
- 命中 tokens（hit tokens）/ 未命中 tokens（miss tokens）
- 子代理（subagent）
- 思考模式（thinking mode）
- 上下文窗口（context window）
- 模型（model）/ 提供方（provider）/ API key

## 何以有此

DeepSeek V4 有大上下文窗口（context window），亦有低价已缓存输入（cached input）。然缓存（cache）省费，系于精确前缀复用（exact prefix reuse）。若系统提示词（system prompt）屡变、模型（model）/ 提供方（provider）/ 思考模式（thinking mode）中途换、`/compact` 过猛，或主会话灌入大量噪声工具输出（noisy tool output），则缓存（cache）易断。

此 skill 给 agent 一 cache-first 法：

- 守稳定指令前缀（stable instruction prefix）
- 判 continue、`/new`、`/compact` 何者宜
- 以子代理（subagent）隔噪声探索（noisy exploration）
- 使思考模式（thinking mode）、模型（model）、提供方（provider）、API key 选择知缓存（cache）利害
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

- 求最大化 DeepSeek V4 缓存命中（cache hits）
- 降 OpenCode / Atlas 缓存未命中（cache misses）
- 判 `/new`、continue、`/compact`
- 设计 `AGENTS.md` 或指令文件（instruction files），使前缀（prefix）稳
- 判子代理（subagent）是护缓存（cache），抑或增耗
- 比 DeepSeek-TUI 缓存感知（cache-aware）设计与 OpenCode 工作流（workflow）
- 解释缓存命中（cache-hit）与缓存未命中输入（cache-miss input）成本差

若仅泛谈上下文窗口（context window），而不涉前缀缓存（prefix-cache）行为，勿载。

## 参考

- `deepseek-v4-cache.md` — DeepSeek V4 前缀缓存（prefix cache）、用量指标（usage metrics）、思考模式（thinking mode）影响
- `opencode-cache-practices.md` — OpenCode / Atlas session-shaping patterns
- `deepseek-tui-lessons.md` — DeepSeek-TUI 经验，取其 cache optimization 义

## 设计律

1. **稳定前缀（stable prefix）胜 clever compression。** 启动指令（startup instructions）宜短、稳、久。
2. **需连续性（continuity），则追加（append）。** 旧 turns 有用，留其已缓存前缀链（cached prefix chain）。
3. **事异则新。** 独立任务用 `/new`，复用稳定 project instructions。
4. **噪声探索（noisy exploration）外置。** 子代理（subagents）可护主会话（main session），即使子调用缓存复用（child cache reuse）较差。
5. **量命中/未命中（hit/miss），勿只数 tokens。** 较长而多命中（hit），或比短而多未命中（miss）廉。

## 风格

本文采文言简式；技术术语用中文（English）双语。此风格只为稳定短输出（stable terse output）与低噪声尾部（low-noise tail），不作通用文风 skill。

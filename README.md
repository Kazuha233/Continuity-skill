# AgentContinuitySkill -- Stop AI Agent Drift, Session Amnesia & Ghost Continuity

> **Your AI agent forgets, drifts off-topic, and hallucinates about past actions. This fixes that.**
> **你的 Agent 会漂移、会失忆、会一本正经地胡说。这个 skill 修的就是这个。**

[![License: PolyForm-Noncommercial-1.0.0](https://img.shields.io/badge/License-PolyForm--Noncommercial-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.1.0-green.svg)](skill/continuity-kit.skill.md)
[![Upstream](https://img.shields.io/badge/upstream-Qianhui/continuity--kit-lightgrey)](https://github.com/phamhien4278n-hub/continuity-kit)

---

## 熟悉吗？/ Does This Sound Familiar?

| 你说... / You say... | Agent 做了啥 / Your agent does... | 这是 / That's |
|---------------------|----------------------------------|:---:|
| *"跑一下 Project X"* | 20 轮后在优化你根本没提过的东西 / 20 turns later optimizing something you never asked for | **漂移 / Drift** |
| *新开一个终端 / Open a new terminal* | "我们在干嘛？不记得了" / "What were we doing? I don't remember" | **失忆 / Amnesia** |
| *"你检查过配置吗？"* | "查了"--其实根本没动 / "Yes" -- but it never did | **幽灵连续性 / Ghost Cont.** |
| *"这个假设是错的"* | 它在第 1 轮的一个猜测上建了 3 小时的工作 / Built 3h of work on a guess from turn 1 | **假设腐败 / Assumption Rot** |

> **用过 Claude Code、Codex、Cursor 或 Hermes 超过 15 分钟？这四个坑你一定踩过。**
> **If you've used any coding agent for more than 15 minutes, you've hit every single one.**

这些问题不是"记忆不够"。Agent 不缺记忆。它缺的是元认知 -- 知道自己知道什么、在假设什么、忘了什么。上下文压缩救不了这个。

These are not memory problems. The agent has plenty of memory. What it lacks is **metacognition** -- knowing what it knows, what it's assuming, and what it's forgotten. No amount of context compression fixes that.

---

## 怎么治 / What ContinuityKit Does

三层架构。每层对应一个问题。

Three layers. Each targets one of the problems above.

| 层 / Layer | 做什么 / What | 解决的问题 / Problem Fixed |
|-----------|--------------|:---:|
| **心跳 / Heartbeat** | 每 N 轮自检：注意力、假设、未收束推理 / Self-check every N turns | **漂移 / Drift** (不走偏) |
| **会话快照 / Session Snapshot** | 会话结束时写结构化快照 / Structured snapshot at session end | **失忆 / Amnesia** (秒恢复) |
| **RELAY 协议** | 跨会话活索引，每项有时间戳和来源 / Cross-session living index | **幽灵连续性 / Ghost Cont.** (可追溯) |

**实验验证：任务连贯性 +6.00** (A/B test, N=4, 30-turn sessions)

---

## 为什么要用这个 skill / Why This Skill

原版 ContinuityKit (v0.1, by Qianhui) 是一份设计很好的**协议规范 + YAML schema 定义** -- 但仅此而已。它告诉你"该做什么"，但没有给出"怎么做"的东西。

The original ContinuityKit is a well-designed protocol spec -- but it tells you *what* to do, without giving you something you can actually run.

本 skill 把协议翻译成了 Agent 自己能读懂的指令文件，装上去就能用：

This skill turns the protocol into an instruction file the agent can follow directly. Install and go.

| 原作 / Original (spec) | 本 skill / This skill |
|------------------------|----------------------|
| 文档 + schema，等人来实现 / Documentation + schemas | **可执行指令**，Agent 自己就能读懂 / **Executable instructions** the agent can follow |
| 需要手动接入 hook / Manual hook integration | **自律模式**，Agent 自己记得做 / **Self-discipline mode**, agent self-enforces |
| 没有存储目录 / No storage directory | **自动初始化**目录结构 / **Auto-initializes** directory structure |
| 无安装流程 / No install flow | **对话式上手指南**，三步搞定 / **Conversational onboarding**, 3 steps |

---

## 上手指南：一场对话，不是一份配置文件 / Onboarding: A Conversation, Not a Config File

大多数 Agent 工具让你改 YAML、设环境变量、或者读一篇长长的文档。这个 skill 不一样 -- 它直接跟你聊天，三步就好。

Most agent tools make you edit configs or read long docs. This skill just talks to you. Three questions, and you're done.

**第一步：存储目录 / Step 1: Storage directory**

```
Agent: 我需要一个本地目录来存放会话状态，就像一个小笔记本。
       下次启动时我可以翻看之前记到哪了。只存任务摘要和心跳记录，不会存对话日志。

       存储目录：
         1) 默认路径  (~/agent-continuity-text/)
         2) 自定义    (你自己指定路径)
       选 1 还是 2？

User:  2: D:\ProgramData\agent-continuity-text(codex-test)
```

**第二步：心跳间隔 / Step 2: Heartbeat interval**

```
Agent: 为防止执行长任务时跑偏，我会定期自检——
       看看自己在关注什么、在做哪些假设、还有什么推理没做完。

       心跳间隔：
         1) 默认    (每 5 轮一次)
         2) 自定义  (你指定轮数，或输 "off" 关掉)
       选哪个？

User:  1
```

**第三步：权限确认 / Step 3: Permission to write**

```
Agent: 我会在指定目录下创建 relay.yaml 和 snapshots/ 目录。
       需要你确认才能开始，可以吗？

User:  可以
```

**完成！/ Done!**

```
  ✔  创建 D:\ProgramData\agent-continuity-text(codex-test)/
  ✔  创建 snapshots/
  ✔  初始化 relay.yaml
```

三次问答。不需要碰 YAML，不需要设环境变量，不需要翻文档。装好就能用。

Three questions. No YAML editing. No env vars. No "RTFM" moments. Ready to go.

---

## 版本记录 / Version History

这个 skill 经过两次迭代才达到现在的质量。

This skill went through two iterations to reach its current quality.

### v1.0.0 -- 初始移植 / Initial Port

忠实翻译了原协议的心跳/快照/RELAY 结构和边界规则。能用，但有几个问题：

Faithfully translated the protocol structure. It worked, but had issues:

- 用户拒绝时 Onboarding 可能死循环 / Onboarding could loop on rejection
- 文件操作不请示就执行 / File ops without asking permission
- N+1 心跳计数依赖 Agent 记忆（不可靠）/ N+1 count relied on agent memory
- 没有停用流程 / No deactivation path
- 描述缺少触发条件 / Description lacked triggers

### v1.1.0 -- 改进 / Refinement

10 个问题全部修复。原则：**只修坏的，不加新的。**

All 10 issues fixed. Principle: **fix what's broken, add nothing extra.**

| # | 问题 / Issue | 修复 / Fix |
|---|-------------|-----------|
| 1 | 拼写错误 high\|medium\|high | 修正为 high\|medium\|low |
| 2 | 空话式"强制中断" / Empty "forced interrupt" rhetoric | 删除。只说做什么，不说"强制" |
| 3 | 用户拒绝时死循环 / Onboarding loop on refusal | 简化跳过多选。拒绝立刻尊重 |
| 4 | 文件操作不请示 / File ops without permission | 新增 "May I proceed?" 许可步骤 |
| 5 | interaction_mode 字段诱导幻觉 | 完全删除 -- Agent 无法可靠观察用户 |
| 6 | N+1 依赖 Agent 记忆 / N+1 needed agent memory | 改为从当前回答计数，不需要历史 |
| 7 | 没有停用路径 / No deactivation path | 新增 Deactivation 段落 |
| 8 | 快照文件是 .md 但内容是 YAML | 改为 .yaml |
| 9 | 描述缺少触发条件 / Description lacked triggers | frontmatter 增加了触发场景 |
| 10 | 没有测试用例 / No test cases | 新增 3 个测试场景 |

---

## 快速安装 / Quick Install

```bash
# Hermes Agent
hermes skills install https://raw.githubusercontent.com/Kazuha233/Continuity-skill/main/skill/continuity-kit.skill.md
```

**手动安装 / Manual (Any Agent):**

Copy `skill/continuity-kit.skill.md` to your agent's skills directory:

| Agent | Path |
|-------|------|
| Hermes | `~/.hermes/skills/continuity-kit/SKILL.md` |
| Claude Code | `./.claude/skills/continuity-kit/SKILL.md` |
| Codex | `./.agents/skills/continuity-kit/SKILL.md` |
| Cursor | `./.cursor/skills/continuity-kit/SKILL.md` |

安装后首次使用会自动触发对话式上手指南。

After install, the first run walks you through the 3-step conversational setup.

---

## 仓库结构 / Repository Structure

```
continuity-kit/
├── README.md                           <- 就是这页 / You are here
├── LICENSE                             <- PolyForm Noncommercial 1.0.0
├── PRIVACY.md                          <- 数据处理说明 / Data handling & privacy
├── .gitignore                          <- 过滤敏感文件 / Filters sensitive files
├── skill/
│   └── continuity-kit.skill.md         <- 核心 skill（装这个 / Install this）
├── config-example.yaml                 <- 配置模板 / Config template
├── docs/
│   └── protocol-spec.md                <- 协议规范 / Protocol spec
└── examples/
    ├── heartbeat-example.md            <- 心跳示例 / Heartbeat example
    └── hermes-dotfile-template.md      <- .hermes.md 强化模板 / Dual-layer enforcement
```

---

## 系统要求 / Requirements

- 任何支持读写文件和结构化输出的 Agent 框架
- 不需要 GPU、API key 或外部服务
- 完全离线运行

- Any agent framework with file I/O and structured output support
- No GPU, API keys, or external services required
- Works fully offline

## 许可证 / License

**PolyForm Noncommercial 1.0.0**

- 免费：个人非商业用途 / Personal, non-commercial use
- 免费：5 人以下非商业团队 / Teams of 5 or fewer, non-commercial
- 需要商业许可：营利、企业、SaaS 或创收场景 / Requires commercial license for for-profit use

上游原作 / Upstream: [ContinuityKit](https://github.com/phamhien4278n-hub/continuity-kit) v0.1 by **Qianhui**.

---
title: 别把 CLAUDE.md 和 AGENTS.md 写成镜像
slug: 2026-05-20-claude-agents-writing-guide
summary: 明确 CLAUDE.md 与 AGENTS.md 的分工，给出全局与项目级指令文件的重构方案。
description: CLAUDE.md 与 AGENTS.md 的定位差异、内容归属决策树、全局级与项目级编写规则，以及速查参考。
---

> 创建日期：2026-05-20

CLAUDE.md 和 AGENTS.md 最常见的问题不是写得太少，而是写成了两份几乎相同的文件。根本原因是没有理解两者的定位差异：AGENTS.md 是面向所有 AI 工具的通用操作手册，CLAUDE.md 则是在此基础上叠加 Claude Code 专属能力的扩展层。理解这个分层关系后，"这条规则该写在哪里"就变成了一个可以机械判断的问题——本文提供了一棵决策树来做这件事。全文按"概念 → 决策 → 全局规则 → 项目规则 → 速查"的顺序组织，方便按需跳读。

***

## 目录

1. [一、基础概念](#一基础概念)
2. [二、内容归属决策树](#二内容归属决策树)
3. [三、全局级编写规则](#三全局级编写规则)
4. [四、项目级编写规则](#四项目级编写规则)
5. [五、速查参考](#五速查参考)

***

## 一、基础概念

### 1.1 两层文件的关系

```
全局级（跨项目生效）                       项目级（仅当前仓库生效）
┌─────────────────────────┐              ┌─────────────────────────┐
│ ~/.claude/CLAUDE.md     │              │ {repo}/CLAUDE.md        │
│ ~/.codex/AGENTS.md      │              │ {repo}/AGENTS.md        │
└─────────────────────────┘              └─────────────────────────┘
  个人偏好 source of truth                 项目特定的上下文和约束，优先级高于全局级
  文件独立设计，互不读取                     CLAUDE.md 可用 @AGENTS.md 导入
```

### 1.2 核心原则

| 原则       | 说明                                                                                                   |
| -------- | ---------------------------------------------------------------------------------------------------- |
| **行数上限** | Anthropic 官方建议 CLAUDE.md 控制在 **200 行以内**；Codex 无明确限制，建议同样 200 行以内 |
| **独立设计** | `~/.claude/CLAUDE.md` 只被 Claude Code 读取，`~/.codex/AGENTS.md` 只被 Codex 读取——不存在跨读，不需要强制统一结构 |
| **自然重叠** | 个人偏好（语言、编码、Git 规范等）在两文件中相同 |
| **各有侧重** | `CLAUDE.md` 面向交互式 Agent（plan mode / Agent 工具 / MCP / @import）；`AGENTS.md` 面向异步 Agent（沙箱约束 / 无网络 / 结构化输出） |

### 1.3 核心差异化框架

```
AGENTS.md = 通用操作手册                CLAUDE.md = @AGENTS.md 导入 + Claude 专属扩展
┌─────────────────────────┐          ┌─────────────────────────────────┐
│ Quick Facts             │          │ @AGENTS.md  ← 自动展开全部内容     │
│ Project Structure       │          │                                 │
│ Commands                │          │ ## Architecture Decisions       │
│ Coding Standards        │          │   (AGENTS.md 不需要的 WHY)       │
│ Boundaries              │          │ ## Common Gotchas               │
│                         │          │   (Agent 无法从代码推断的坑)       │
│ 18+ 工具都能读取          │          │ ## Agent Collaboration Rules    │
│ 纯 Markdown，无特殊语法   │          │   (Claude Code 专属工作流)        │
└─────────────────────────┘          └─────────────────────────────────┘
  source of truth for shared           Claude Code 读取时自动展开
  commands, structure, rules           获得 AGENTS.md + Claude 专属内容
```

## 二、内容归属决策树

当你不确定一条规则应该写在 AGENTS.md 还是 CLAUDE.md 时，沿着这棵树往下走：

```
这条规则应该写在哪里？

├── 是可直接执行的命令吗？
│   └── YES → AGENTS.md（Commands 章节）
│
├── 是项目结构或目录职责吗？
│   └── YES → AGENTS.md（Project Structure 章节）
│
├── 是硬性安全/隐私约束吗？
│   └── YES → **两边都写**（安全规则不依赖单一入口）
│
├── 是架构决策的 WHY 或设计理由吗？
│   └── YES → CLAUDE.md（Architecture Decisions 章节）
│
├── 是反直觉的行为或踩过的坑吗？
│   └── YES → CLAUDE.md（Common Gotchas 章节）
│
├── 是 Claude Code 专属能力（plan mode / Agent / MCP / .claude/）？
│   └── YES → CLAUDE.md（Agent Collaboration Rules 章节）
│
├── 是跨工具通用的行为约束吗？
│   └── YES → AGENTS.md（Boundaries 章节）
│
└── 不确定？
    └── 默认放 AGENTS.md（通用性更强），必要时在 CLAUDE.md 添加补充
```

## 三、全局级编写规则

全局级文件承载个人偏好，跨所有项目生效。两个文件分别由不同工具读取，独立设计即可。

### 3.1 全局 CLAUDE.md 设计原则

**推荐结构：**

```markdown
# Global CLAUDE.md

## Communication          ← 语言、风格偏好
## General Rules          ← 文件规范、操作约束（精简版，删除 Agent 默认行为）
## Engineering Approach   ← 工程偏好、依赖策略
## Code & Testing         ← 测试原则
## Git Conventions        ← 提交规范
## Instruction Priority   ← 优先级声明
## Claude Code Workflow   ← plan mode / Agent / MCP 使用策略（核心差异化章节）
```

### 3.2 全局 AGENTS.md 设计原则

**推荐结构：**

```markdown
# Global AGENTS.md

## Communication          ← 语言、风格偏好
## General Rules          ← 文件规范、操作约束
## Engineering Approach   ← 工程偏好、依赖策略
## Code & Testing         ← 测试原则
## Git Conventions        ← 提交规范
## Instruction Priority   ← 优先级声明
## Sandbox & Output       ← 沙箱限制 + 工具映射 + 异步不确定性处理
## Output Expectations    ← PR 格式、commit 格式、产出结构化要求
```

## 四、项目级编写规则

项目级文件承载当前仓库的特定上下文，优先级高于全局级。核心策略是让 AGENTS.md 作为共享内容的 single source of truth，CLAUDE.md 通过 `@AGENTS.md` 导入并叠加专属扩展。

### 4.1 `@AGENTS.md` 导入机制

Anthropic 官方推荐的去重方案——CLAUDE.md 用 `@AGENTS.md` 导入共享内容：

```markdown
# CLAUDE.md — {Project Name}
@AGENTS.md

## Architecture Decisions    ← AGENTS.md 不需要的、Claude 能理解的 WHY
## Common Gotchas            ← Agent 无法从代码推断的坑
## Agent Collaboration Rules ← Claude Code 专属工作流
```

**优势：**

1. 消除两文件内容同质化的根本问题
2. 共享内容的 single source of truth = AGENTS.md
3. Claude Code 读取时自动展开 `@AGENTS.md`，获得完整指令集
4. 维护时只改一处，不需要同步两个文件

**注意事项：**

* `@AGENTS.md` 必须放在 CLAUDE.md 的最前面或章节之间，不能嵌在段落中
* 被导入的 AGENTS.md 行数也算入 CLAUDE.md 的总行数预算
* 如果 AGENTS.md 过长，可以选择性导入关键章节而非整个文件

### 4.2 AGENTS.md 项目级推荐结构

```markdown
# AGENTS.md — {Project Name}

## Quick Facts              ← 一行定位 + 技术栈
## Project Structure        ← 目录职责表
## Commands                 ← 可 copy-paste 的命令
## Coding Standards         ← 代码示例优先
## Boundaries               ← Always / Ask First / Never 三级约束
```

### 4.3 CLAUDE.md 项目级推荐结构（使用 @import）

```markdown
# CLAUDE.md — {Project Name}
@AGENTS.md                  ← 导入所有共享内容

## Architecture Decisions   ← 非显而易见的设计决策和 WHY
## Common Gotchas           ← 项目特有的坑
## Agent Collaboration Rules ← Claude Code 专属工作流
```

**各章节编写指引：**

| 章节                        | 写什么                             | 不写什么         |
| ------------------------- | ------------------------------- | ------------ |
| Architecture Decisions    | 设计选择的 WHY、trade-off 记录          | 代码可读的结构描述    |
| Common Gotchas            | 踩过的坑、反直觉行为、隐含依赖                 | 一次性 bug 修复记录 |
| Agent Collaboration Rules | plan 目录、inbox 规则、plan mode 触发条件 | 人类团队流程       |

**Claude 专属内容（仅出现在 CLAUDE.md 的扩展章节中）：**

* `@import` 外部知识文件（如 `@docs/arch-decision-records.md`）
* `.claude/` 目录结构引用（settings、commands、hooks）
* plan mode / Agent 工具使用策略
* MCP server 相关指引
* Skills / Hooks 相关约定

## 五、速查参考

### 5.1 知名仓库参考模式

| 仓库             | 模式                                       | 启示                      |
| -------------- | ---------------------------------------- | ----------------------- |
| Vercel/Next.js | AGENTS.md 以命令和结构为主，面向所有 AI 工具            | 项目级 AGENTS.md 应是 "操作手册" |
| 大型 monorepo    | 根目录 AGENTS.md + 子目录 `AGENTS.override.md` | 分层覆盖适合复杂项目              |
| Anthropic 官方示例 | CLAUDE.md 用 `@` 导入共享文档，专注 Claude 专属扩展    | `@import` 是官方推荐的去重方案    |

### 5.2 AGENTS.md vs CLAUDE.md 速查对比

| 维度       | AGENTS.md                          | CLAUDE.md                              |
| -------- | ---------------------------------- | -------------------------------------- |
| 读取工具     | 18+ AI 工具（Claude Code、Codex 等）     | 仅 Claude Code                          |
| 定位       | 通用操作手册                             | 通用手册 + Claude 专属扩展                     |
| 全局路径     | `~/.codex/AGENTS.md`               | `~/.claude/CLAUDE.md`                  |
| 项目路径     | `{repo}/AGENTS.md`                 | `{repo}/CLAUDE.md`                     |
| 核心内容     | 命令、结构、编码规范、行为约束                    | 架构决策 WHY、踩坑记录、Agent 工作流                |
| 特殊语法     | 无，纯 Markdown                       | `@import`、`.claude/` 引用               |
| 去重策略     | 作为共享内容的 single source of truth      | 用 `@AGENTS.md` 导入，避免重复                 |
| 差异化章节（全局） | Sandbox & Output、Output Expectations | Claude Code Workflow                   |
| 差异化章节（项目） | Quick Facts、Commands、Boundaries     | Architecture Decisions、Common Gotchas、Agent Collaboration Rules |

---
name: grill-er
description: Grill bounded-context and domain decisions, then draw a DDD ER skeleton, then expand tables in field-detail.puml. Use when the user wants domain data design, DDD domain split, ER-then-fields, grill-to-ER, 领域划分, or modeling a new iteration.
disable-model-invocation: true
---

# Grill ER

把「追问决议 → 领域划分 → ER 骨架 → 字段细节」固化为可重复流程。本阶段**只出设计文档**，不写实现代码。

er绘制核心是 领域ddd划分， 字段细节才需要真正的展开

写法以**当前仓库已有**的 `*-ER.puml` / `*-字段细节.puml` 为准；没有先例时用 [templates.md](templates.md)。ER 优先学临平那种骨架（中文实体、Crow's foot、决议短码、不变式 note），不要学带集合名/存储分层的表结构图。

## Prerequisites

本 skill 在 Phase 1 会运行 `grill-with-docs`（内含 grilling + domain-modeling）。若当前环境没有这些 skill，先安装后再继续，例如：

```bash
npx skills add mattpocock/skills --skill grill-with-docs
```

## 开场

1. **定位产出目录**：在仓库内搜索已有 `*-ER.puml`、`*-字段细节.puml`（常见于 `doc/`、`docs/`、设计目录）。有则沿用其父目录与命名模式；没有则问用户路径（可建议 `doc/<版本>/`）。
2. **对齐本地约定**：从最近 1～2 份**骨架型**同仓样例读出——文件命名、`entity`/`object` 用法、跨域引用、决议短码。无样例则用 templates。审计字段名只在 Phase 3 对齐库表。
3. 确认本次限界上下文名称（用于 title、`package` 与文件名）。可以有多个并列上下文，经共享身份对齐。
4. 术语表/ADR 需要时走 `grill-with-docs`；能查代码与已有文档的事实不要问用户。

## Phase 1 — Grill（决议）

运行一次 `grill-with-docs` session：

- 一次只问一个问题，附推荐答案，等用户回复后再继续。
- **先压领域划分**：几个限界上下文、谁在本图、谁外域只挂、谁本期不做。用户确认切分之前，不要往实体细节钻。
- 再压：聚合根/实体边界、组成 vs 仅对齐、基数、唯一性、状态机/派生态、跨域共享身份、值对象（ER 不升格）。
- 决议用短码标记（可自拟，如 U3、H1、B1），后续写进两份 puml。
- **用户确认「决议够稳、可以落 ER」之前，不要写 puml。**

## Phase 2 — ER（DDD 骨架）

路径：`{产出目录}/{域简称}-ER.puml`（命名跟同仓先例；无先例则用此模式）

这张图的任务是**限界上下文 + 实体 + 关系**，不是表结构。

必须包含：

- `title`：上下文名 + 关系主题关键词
- 文件头注释：已定决议短码 + `字段细节见：…-字段细节.puml`
- `package "限界上下文名"` 圈本域实体；并列上下文各自一个 package
- 外域实体放在 package 外，只留中文名（临平「主账户」那种）
- `entity` 用领域语言，Crow's foot；边上写业务关系，不写列名/类型
- 关键实体用 `note` 写不变式：状态、互斥、懒创建、组成 vs 对齐

禁止出现在 ER 上：

- 表名、集合名、`[Mongo]`/`[MySQL]`、字段清单、审计列
- 按存储分层的 package（如「① Mongo ② 快照集合」）
- 值对象升格为 `entity`（科目清单、内嵌作息副本等）
- 外域展开字段或再画一张外域表结构

写完后请用户确认：**领域切分、聚合、关系**是否齐全。不要在这一步补字段。

## Phase 3 — 字段细节

路径：`{产出目录}/{域简称}-字段细节.puml`

ER 稳定后再写。这里才真正展开表/集合/列。用 `object` 块，不是再画一遍抽象 ER。

必须包含：

- 文件头：`关系骨架见：…-ER.puml` + 决议摘要 + 表名/字段名为草案的说明
- 每张业务表：`中文名(table_name)`、主键、FK、业务列、**本仓惯用**审计列
- `--` 下：唯一约束、派生规则、本期边界、决议码对应
- 跨域实体只挂主键 +「详见 {已有域}-ER.puml」，不展开外域字段
- 文末可用简化关系与 ER 对照；桥表可在本文件补 `object`

## 硬约束

| 做 | 不做 |
|---|---|
| 先划限界上下文，再画实体关系 | 一上来按库表/Mongo 集合铺图 |
| ER 只承载 DDD 划分与不变式 | 把全字段、存储、审计塞进 ER |
| 字段细节才展开表结构 | 跳过 grill 直接臆造表 |
| 两文件互相引用 | 本阶段写实现代码 / 建表脚本（除非用户另要） |
| 命名与跨域习惯跟**当前仓库**对齐 | 把别的项目的表前缀硬搬进 ER 实体名 |

## 完成检查

- [ ] 用户已确认 Phase 1 决议（含限界上下文切分）
- [ ] ER 只有领域实体/关系/note，没有表字段与存储细节
- [ ] ER 与字段细节路径、title、交叉引用一致
- [ ] 每个硬决议在 ER note 或字段 `--` 区至少出现一次
- [ ] 字段细节的审计字段与跨域引用与**本仓**先例一致
- [ ] 用户确认两份图后结束（实现另开任务）

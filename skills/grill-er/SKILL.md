---
name: grill-er
description: Design data models for a new feature or iteration by grilling decisions first, then writing ER.puml, then field-detail.puml. Use when the user wants domain data design, ER-then-fields, grill-to-ER, or starts modeling a new iteration.
disable-model-invocation: true
---

# Grill ER

把「追问决议 → ER 骨架 → 字段细节」固化为可重复流程。本阶段**只出设计文档**，不写实现代码。

写法以**当前仓库已有**的 `*-ER.puml` / `*-字段细节.puml` 为准；没有先例时用 [templates.md](templates.md)，并与项目里已有表/审计习惯对齐。

## Prerequisites

本 skill 在 Phase 1 会运行 `grill-with-docs`（内含 grilling + domain-modeling）。若当前环境没有这些 skill，先安装后再继续，例如：

```bash
npx skills add mattpocock/skills --skill grill-with-docs
```

## 开场

1. **定位产出目录**：在仓库内搜索已有 `*-ER.puml`、`*-字段细节.puml`（常见于 `doc/`、`docs/`、设计目录）。有则沿用其父目录与命名模式；没有则问用户路径（可建议 `doc/<版本>/`）。
2. **对齐本地约定**：从最近 1～2 份同仓样例读出——文件命名、`entity`/`object` 用法、审计字段名、跨域引用方式、决议短码习惯。无样例则用 templates，审计字段跟现有库表或代码实体走。
3. 确认本次域名称（用于 title 与文件名）。
4. 术语表/ADR 需要时走 `grill-with-docs`；能查代码与已有文档的事实不要问用户。

## Phase 1 — Grill（决议）

运行一次 `grill-with-docs` session：

- 一次只问一个问题，附推荐答案，等用户回复后再继续。
- 优先压：实体边界、基数、唯一性、状态机/派生态、跨域引用、本期不做项。
- 决议用短码标记（可自拟，如 U3、H1），后续写进两份 puml。
- **用户确认「决议够稳、可以落 ER」之前，不要写 puml。**

## Phase 2 — ER（骨架）

路径：`{产出目录}/{域简称}-ER.puml`（命名跟同仓先例；无先例则用此模式）

只画实体、关系、基数、关键业务约束；**不要**铺开全字段。

必须包含：

- `title`：域 + 关系主题关键词
- 文件头注释：已定决议短码 + `字段细节见：…-字段细节.puml`
- `entity` + Crow's foot；边上可写关键约束
- 关键实体用 `note` 写状态、派生、互斥、共享资源挂载约定

写完后请用户确认关系与决议是否齐全。

## Phase 3 — 字段细节

路径：`{产出目录}/{域简称}-字段细节.puml`

ER 稳定后再写。用 `object` 块列字段，不是再画一遍抽象 ER。

必须包含：

- 文件头：`关系骨架见：…-ER.puml` + 决议摘要 + 表名/字段名为草案的说明
- 每张业务表：`中文名(table_name)`、主键、FK、业务列、**本仓惯用**审计列
- `--` 下：唯一约束、派生规则、本期边界、决议码对应
- 跨域实体只挂主键 +「详见 {已有域}-ER.puml」，不展开外域字段
- 文末可用简化关系与 ER 对照；桥表可在本文件补 `object`

## 硬约束

| 做 | 不做 |
|---|---|
| 先决议再 ER，先 ER 再字段 | 跳过 grill 直接臆造表结构 |
| 两文件互相引用 | 把全字段塞进 ER |
| 决议短码可追溯 | 本阶段写实现代码 / 建表脚本（除非用户另要） |
| 命名与审计习惯跟**当前仓库**对齐 | 把别的项目的表前缀/字段习惯硬搬过来 |

## 完成检查

- [ ] 用户已确认 Phase 1 决议
- [ ] ER 与字段细节路径、title、交叉引用一致
- [ ] 每个硬决议在 ER note 或字段 `--` 区至少出现一次
- [ ] 审计字段与跨域引用方式与**本仓**先例一致（无先例则与 templates + 现有表一致）
- [ ] 用户确认两份图后结束（实现另开任务）

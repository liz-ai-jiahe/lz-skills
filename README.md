# grill-er

Agent skill：先用追问把领域决议打稳，再落 `*-ER.puml`，最后落 `*-字段细节.puml`。

适用于新需求 / 新迭代的数据模型设计。本阶段只出设计文档，不写实现代码。写法跟随**当前仓库**已有 ER / 字段细节约定，可跨项目复用。

## Install

把下面命令里的 `<owner>` 换成你的 GitHub 用户名或组织名（推送仓库后）：

```bash
npx skills add <owner>/grill-er --skill grill-er
```

或：

```bash
npx skills add https://github.com/<owner>/grill-er --skill grill-er
```

列出仓库内 skill（不安装）：

```bash
npx skills add <owner>/grill-er -l
```

## Prerequisite

`grill-er` 在 Phase 1 依赖 `grill-with-docs`（含 grilling + domain-modeling）。建议一并安装：

```bash
npx skills add mattpocock/skills --skill grill-with-docs
```

## Usage

在 Cursor / 兼容 agent 中显式挂上 `grill-er`，例如：

```text
@grill-er
新迭代：XX 模块。先 grill，决议稳了再落 ER 和字段细节。
```

流程：

1. **Grill** — 一次一问，压实体边界 / 基数 / 唯一性 / 派生规则等  
2. **ER** — 用户确认决议后写 `{域}-ER.puml`  
3. **字段细节** — ER 稳定后写 `{域}-字段细节.puml`  

## Repo layout

```text
skills/
  grill-er/
    SKILL.md
    templates.md
    agents/openai.yaml
```

## Publish to skills.sh

1. 将本仓库推到 **Public** GitHub  
2. 分享安装命令：`npx skills add <owner>/grill-er --skill grill-er`  
3. [skills.sh](https://skills.sh) 会根据 CLI 安装 telemetry 自动收录（无需投稿；关闭 telemetry / CI 安装不计入排行）

## License

MIT

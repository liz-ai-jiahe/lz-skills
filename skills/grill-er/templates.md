# PlantUML 模板

无同仓先例时使用。有 `*-ER.puml` / `*-字段细节.puml` 时优先模仿本仓写法，仅用本文补缺口。

占位符：`{域}`、`{域简称}`、`{产出目录}`、审计列名以开场探测到的本仓习惯替换。

## ER 骨架

```plantuml
@startuml
title {域} 领域关系（{关键词1} / {关键词2} / …）

' 已定：{短码} {一句话} | {短码} {一句话}
' 字段细节见：{域简称}-字段细节.puml

entity "{实体A}" as A
entity "{实体B}" as B

A ||--o{ B : …

note right of A
  {状态 / 关键约束 / 派生规则}
end note

@enduml
```

## 字段细节

```plantuml
@startuml
title {域} 字段细节（{关键词…}）

' 关系骨架见：{域简称}-ER.puml
' 领域决议：{短码列表与摘要}
' 表名/字段名为草案；审计字段对齐本仓习惯

object "{外域实体}({external_table}) [外域]" as ext {
{external_pk}
--
详见 {外域}-ER.puml
}

object "{中文名}({table_name})" as a {
{table_name}_id 主键
{fk}_id FK…
{业务字段} {说明}
{审计列…}
--
{唯一约束 / 决议码 / 本期边界}
}

' —— 关系（与骨架一致，便于对照）——
a "1" --> "多" b

@enduml
```

## 写法要点

- ER 用 `entity`；字段细节用 `object { … }`。
- 关系边与 note 写业务约束，不写具体语言类型。
- 共享附件/素材表：按本仓既有挂载方式注明（常见为关联 id + 业务类型），不强行发明物理 FK。
- 派生态优先写「不落库、如何派生」，除非用户明确要求冗余列。

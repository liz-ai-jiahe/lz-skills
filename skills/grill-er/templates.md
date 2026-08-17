# PlantUML 模板

无同仓先例时使用。有 `*-ER.puml` / `*-字段细节.puml` 时：ER 优先模仿**骨架型**（中文实体 + 限界上下文 package）；字段细节再对齐表名与审计习惯。

占位符：`{域}`、`{域简称}`、`{产出目录}`、审计列名以开场探测到的本仓习惯替换。

## ER 骨架（DDD 划分，不展开表）

```plantuml
@startuml
title {域} 领域关系（{关键词1} / {关键词2} / …）

' 已定：{短码} {一句话} | {短码} {一句话}
' 字段细节见：{域简称}-字段细节.puml

package "{限界上下文A}" {
  entity "{聚合根}" as Root
  entity "{实体}" as E
}

package "{并列上下文B}" {
  entity "{另一聚合根}" as Other
}

entity "{外域身份}" as Ext

Root ||--o{ E : {业务关系}
Ext ||--o{ Root : {共享身份}
Other .. Root : {对齐，非组成}

note right of Root
  {不变式 / 互斥 / 懒创建}
end note

@enduml
```

## 字段细节（这里才展开表）

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

- ER 用 `entity` + `package`（限界上下文）；字段细节用 `object { … }`。
- ER 关系边与 note 写业务约束，不写列名、类型、集合名、存储引擎。
- 值对象留在 ER note；字段细节再列内嵌结构。
- 共享附件/素材表：按本仓既有挂载方式在字段细节注明，不强行发明物理 FK。
- 派生态优先写「不落库、如何派生」，除非用户明确要求冗余列。

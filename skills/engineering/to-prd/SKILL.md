---
name: to-prd
description: 把当前对话上下文整理成 PRD（产品需求文档），并发布到项目 issue tracker。Use when 用户想根据当前讨论创建 PRD、需求文档、产品规格或实现规格。
---

默认使用简体中文与用户沟通。生成的 PRD 默认使用中文；代码标识符、接口名、文件名、标签名保持原文，必要时附中文解释。

本 skill 基于当前对话上下文和代码库理解生成 PRD。不要采访用户；只综合你已经知道的信息。

issue tracker（问题跟踪器）和 triage label vocabulary（分诊标签词表）应已提供；如果没有，运行 `/setup-matt-pocock-skills`。

## Process

1. 如果还没有探索仓库，先探索以理解代码库当前状态。整个 PRD 使用项目领域术语表中的词汇，并尊重当前区域相关 ADR。

2. 草拟完成实现需要构建或修改的主要 modules（模块）。主动寻找可以抽取 deep modules（深模块）的机会，让它们能被隔离测试。

deep module（深模块）与 shallow module（浅模块）相对：它用简单、可测试、很少变化的 interface（接口）封装大量功能。

向用户确认这些模块是否符合预期。向用户确认哪些模块需要写测试。

3. 使用下面模板编写 PRD，然后发布到项目 issue tracker。应用 `ready-for-agent` triage label，无需额外分诊。

<prd-template>

## Problem Statement（问题陈述）

从用户视角描述用户正在面对的问题。

## Solution（解决方案）

从用户视角描述解决方案。

## User Stories（用户故事）

一份较长的编号用户故事列表。每个用户故事格式如下：

1. 作为 <角色>，我想要 <功能>，以便 <收益>

<user-story-example>
1. 作为手机银行客户，我想查看账户余额，以便更好地决定如何消费
</user-story-example>

用户故事列表应尽量完整，覆盖功能的所有方面。

## Implementation Decisions（实现决策）

列出已经做出的实现决策。可以包括：

- 将构建或修改的 modules（模块）
- 将修改的 module interfaces（模块接口）
- 开发者给出的技术澄清
- 架构决策
- schema 变更
- API contracts（API 契约）
- 具体交互

不要包含具体文件路径或代码片段，它们很快会过期。

例外：如果 prototype（原型）产生了比文字更精确表达决策的片段，例如状态机、reducer、schema、类型形状，可以内联到相关决策中，并简短注明来自原型。只保留决策密度高的部分，不要放完整可运行 demo。

## Testing Decisions（测试决策）

列出已经做出的测试决策。包括：

- 什么是好测试：只测试外部行为，不测试实现细节。
- 哪些 modules 将被测试。
- 测试先例：代码库中类似类型的测试。

## Out of Scope（范围外）

描述本 PRD 不覆盖的内容。

## Further Notes（补充说明）

关于该功能的任何补充说明。

</prd-template>

---
name: to-issues
description: 使用 tracer-bullet vertical slices（示踪弹垂直切片）把计划、规格或 PRD 拆成可独立领取的 issue。Use when 用户想把计划转成 issues、创建实现票据、拆分工作、拆任务或生成 GitHub/GitLab/本地 issue。
---

# To Issues（拆分 Issue）

默认使用简体中文与用户沟通。拆分说明、追问、issue 草稿和总结都用中文；标题可按项目习惯使用中文或英文，标签和命令保持原文。

使用 vertical slices（垂直切片，也叫 tracer bullets）把计划拆成可独立领取的 issue。

issue tracker（问题跟踪器）和 triage label vocabulary（分诊标签词表）应已提供；如果没有，运行 `/setup-matt-pocock-skills`。

## Process

### 1. 收集上下文

基于当前对话上下文工作。如果用户传入 issue 引用，例如 issue 编号、URL 或路径，就从 issue tracker 获取它，并阅读完整正文和评论。

### 2. 探索代码库，可选

如果还没有探索代码库，先探索以理解当前代码状态。issue 标题和描述应使用项目领域术语表中的词汇，并尊重当前区域相关 ADR。

### 3. 起草垂直切片

把计划拆成 **tracer bullet** issues。每个 issue 都是薄的 vertical slice（垂直切片），端到端穿过所有集成层，而不是某一层的 horizontal slice（水平切片）。

切片可以是 `HITL` 或 `AFK`。`HITL` 切片需要人工交互，例如架构决策或设计评审。`AFK` 切片可以在没有人工交互的情况下实现并合并。能用 `AFK` 时优先 `AFK`。

<vertical-slice-rules>
- 每个切片都交付一条窄但完整的路径，穿过所有层，例如 schema、API、UI、tests。
- 完成后的切片必须能独立演示或验证。
- 偏好多而薄的切片，而不是少而厚的切片。
</vertical-slice-rules>

### 4. 向用户校验

用编号列表展示建议拆分。每个切片展示：

- **Title（标题）**：简短描述性名称。
- **Type（类型）**：HITL / AFK。
- **Blocked by（阻塞项）**：必须先完成哪些其他切片，如有。
- **User stories covered（覆盖的用户故事）**：该切片解决哪些用户故事，如果源材料包含它们。

询问用户：

- 粒度是否合适？太粗还是太细？
- 依赖关系是否正确？
- 是否有切片应该合并或继续拆分？
- HITL 和 AFK 标记是否正确？

迭代直到用户认可拆分。

### 5. 发布 issue 到 issue tracker

对每个已认可切片，在 issue tracker 发布一个新 issue。使用下面的 issue 正文模板。这些 issue 默认视为可交给 AFK agents，因此除非用户另有说明，发布时应用正确 triage label。

按依赖顺序发布 issue，阻塞项先发，这样可以在 “Blocked by” 字段中引用真实 issue 标识。

<issue-template>
## Parent

父 issue 引用。如果来源是已有 issue，则填写；否则省略本节。

## What to build

简洁描述这个垂直切片。描述端到端行为，不要逐层描述实现。

避免具体文件路径或代码片段，它们很快会过期。例外：如果 prototype（原型）产生了一个比文字更精确表达决策的片段，例如状态机、reducer、schema、类型形状，可以内联到这里，并简短注明来自原型。只保留决策密度高的部分，不要放完整可运行 demo。

## Acceptance criteria

- [ ] 验收标准 1
- [ ] 验收标准 2
- [ ] 验收标准 3

## Blocked by

- 阻塞 ticket 引用，如有

如果没有阻塞项，写 “None - can start immediately”。

</issue-template>

不要关闭或修改任何父 issue。

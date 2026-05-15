---
name: improve-codebase-architecture
description: 基于 CONTEXT.md 的领域语言和 docs/adr/ 的决策记录，寻找代码库中的 deepening opportunities（深化机会）。Use when 用户想改进架构、寻找重构机会、合并强耦合模块，或让代码库更可测试、更适合 AI 导航。
---

# Improve Codebase Architecture（改进代码库架构）

默认使用简体中文与用户沟通。架构建议、问题、权衡和计划都用中文；保留代码标识符、文件名、英文架构术语，并在首次出现时补中文释义。

识别架构摩擦，并提出 **deepening opportunities（深化机会）**：把 shallow modules（浅模块）重构成 deep modules（深模块）。目标是提升 testability（可测试性）和 AI-navigability（AI 可导航性）。

## 术语表

每个建议都必须精确使用这些术语。语言一致性是重点；不要漂移到 “component”“service”“API” 或 “boundary”。完整定义见 [LANGUAGE.md](LANGUAGE.md)。

- **Module（模块）**：任何拥有 interface（接口）和 implementation（实现）的东西，例如函数、类、包、slice。
- **Interface（接口）**：调用方为了使用该模块必须知道的一切，包括类型、不变量、错误模式、调用顺序、配置；不只是类型签名。
- **Implementation（实现）**：模块内部代码。
- **Depth（深度）**：接口上的杠杆率；小接口背后承载大量行为。**Deep（深）** = 高杠杆。**Shallow（浅）** = 接口几乎和实现一样复杂。
- **Seam（接口位置）**：interface 所在的位置；可以不原地编辑就改变行为的地方。用这个词，不用 “boundary”。
- **Adapter（适配器）**：在某个 seam 上满足 interface 的具体东西。
- **Leverage（杠杆）**：调用方从 depth 中获得的价值。
- **Locality（局部性）**：维护者从 depth 中获得的价值：变更、bug、知识集中在一个地方。

关键原则，完整列表见 [LANGUAGE.md](LANGUAGE.md)：

- **Deletion test（删除测试）**：想象删除这个模块。如果复杂度消失了，它只是 pass-through。如果复杂度重新散落到 N 个调用方，它就在发挥作用。
- **Interface is the test surface（接口就是测试面）。**
- **One adapter = hypothetical seam. Two adapters = real seam（一个适配器是假设接口位置，两个适配器才是真接口位置）。**

本 skill 受项目领域模型约束。领域语言为好的 seam 命名；ADR 记录了不应反复争论的决策。

## Process

### 1. 探索

先阅读项目领域术语表，以及当前区域相关 ADR。

然后探索代码库。不要机械套启发式；自然地阅读，并记录你感到摩擦的位置：

- 理解一个概念是否需要在许多小模块之间来回跳转？
- 哪些模块是 **shallow（浅）** 的，即 interface 几乎和 implementation 一样复杂？
- 哪些纯函数只是为了测试而抽出，但真正 bug 藏在调用方式里，因此没有 **locality（局部性）**？
- 哪些强耦合模块把细节泄漏到了 seam 之外？
- 代码库哪些部分未测试，或难以通过当前 interface 测试？

对任何你怀疑浅的东西应用 **deletion test（删除测试）**：删除它会集中复杂度，还是只会移动复杂度？“会集中”才是想要的信号。

### 2. 展示候选项

展示一个编号列表，列出 deepening opportunities（深化机会）。每个候选项包含：

- **Files（文件）**：涉及哪些文件或模块。
- **Problem（问题）**：当前架构为什么造成摩擦。
- **Solution（方案）**：用简明中文说明要改变什么。
- **Benefits（收益）**：用 locality 和 leverage 解释，并说明测试会如何改善。

**领域概念使用 `CONTEXT.md` 里的词，架构概念使用 [LANGUAGE.md](LANGUAGE.md) 里的词。** 如果 `CONTEXT.md` 定义了 “Order”，就说 “Order intake module”，不要说 “FooBarHandler”，也不要随意说 “Order service”。

**ADR 冲突**：如果候选项与现有 ADR 冲突，只有当摩擦真实到值得重新讨论 ADR 时才提出。清楚标注，例如“与 ADR-0007 冲突，但值得重开讨论，因为……”。不要列出 ADR 理论上禁止的每个重构。

此时不要提出具体 interface。询问用户：“你想深入哪一个？”

### 3. 追问循环

用户选定候选项后，进入追问对话。与用户一起走设计树：约束、依赖、深化后模块的形状、seam 后面放什么、哪些测试应该保留。

当决策逐渐清晰时，副作用要即时发生：

- **用不在 `CONTEXT.md` 中的概念命名深化模块？** 把术语加入 `CONTEXT.md`，纪律与 `/grill-with-docs` 相同，见 [CONTEXT-FORMAT.md](../grill-with-docs/CONTEXT-FORMAT.md)。如果文件不存在，懒创建。
- **对话中收紧了一个模糊术语？** 立刻更新 `CONTEXT.md`。
- **用户用关键理由拒绝候选项？** 提议写 ADR，表达为：“要不要我把这记录成 ADR，避免未来架构评审重复提出它？” 只有当这个理由确实能帮助未来探索者避免重复建议时才提出；跳过临时理由，例如“现在不值得”，以及显而易见的理由。见 [ADR-FORMAT.md](../grill-with-docs/ADR-FORMAT.md)。
- **想探索深化模块的备选 interface？** 阅读 [INTERFACE-DESIGN.md](INTERFACE-DESIGN.md)。

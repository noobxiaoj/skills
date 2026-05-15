---
name: write-a-skill
description: 创建结构正确、支持 progressive disclosure（渐进披露）并可包含 bundled resources（捆绑资源）的新 agent skill。Use when 用户想创建、编写、设计、更新或本地化一个 skill。
---

# Writing Skills（编写 Skill）

默认使用简体中文与用户沟通。新 skill 的说明、提示、检查清单默认写中文；`name`、文件名、命令、代码标识符保持英文或项目既有命名。

## Process

1. **收集需求**：询问用户：
   - 这个 skill 覆盖什么任务或领域？
   - 需要处理哪些具体用例？
   - 需要可执行脚本，还是只需要说明？
   - 是否有要包含的参考资料？

2. **起草 skill**：创建：
   - 带有简洁说明的 `SKILL.md`
   - 如果内容超过 500 行，拆出额外 reference files（参考文件）
   - 如果需要确定性操作，添加 utility scripts（工具脚本）

3. **与用户评审**：展示草稿并询问：
   - 是否覆盖你的用例？
   - 是否缺少内容或不清楚？
   - 哪些部分应该更详细或更简短？

## Skill 结构

```
skill-name/
├── SKILL.md           # 主说明，必需
├── REFERENCE.md       # 详细文档，如需要
├── EXAMPLES.md        # 使用示例，如需要
└── scripts/           # 工具脚本，如需要
    └── helper.js
```

## SKILL.md 模板

```md
---
name: skill-name
description: 简短描述能力。Use when [具体触发场景]。
---

# Skill Name

## Quick start

[Minimal working example]

## Workflows

[Step-by-step processes with checklists for complex tasks]

## Advanced features

[Link to separate files: See [REFERENCE.md](REFERENCE.md)]
```

## Description 要求

`description` 是 agent 决定是否加载 skill 时**唯一能看到的内容**。它会和其他已安装 skills 一起出现在 system prompt 中。agent 会读取这些 descriptions，并根据用户请求选择相关 skill。

**目标**：给 agent 刚好足够的信息，让它知道：

1. 这个 skill 提供什么能力。
2. 什么时候、为什么触发它，例如具体关键词、上下文、文件类型。

**格式**：

- 最多 1024 字符。
- 使用第三人称描述。
- 第一句：它做什么。
- 第二句：`Use when [具体触发场景]`。

**好例子**：

```
从 PDF 文件提取文本和表格、填写表单、合并文档。Use when 处理 PDF 文件，或用户提到 PDF、表单、文档提取。
```

**坏例子**：

```
帮助处理文档。
```

坏例子无法让 agent 区分它和其他文档类 skills。

## 什么时候添加脚本

在以下情况添加 utility scripts：

- 操作是确定性的，例如校验、格式化。
- 同样代码会被反复生成。
- 错误需要明确处理。

脚本相比现场生成代码更省 token，也更可靠。

## 什么时候拆分文件

在以下情况拆分成独立文件：

- `SKILL.md` 超过 100 行。
- 内容有明显不同领域，例如 finance schema 和 sales schema。
- 高级功能很少使用。

## 评审清单

起草后确认：

- [ ] Description 包含触发条件，例如 `Use when...`
- [ ] `SKILL.md` 少于 100 行
- [ ] 不包含时效性信息
- [ ] 术语一致
- [ ] 包含具体示例
- [ ] references（参考文件）只深一层

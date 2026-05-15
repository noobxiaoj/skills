---
name: setup-matt-pocock-skills
description: 在 AGENTS.md/CLAUDE.md 中设置 `## Agent skills` 区块，并生成 `docs/agents/` 配置，让工程类 skills 知道本仓库的 issue tracker、triage 标签词表和领域文档布局。Use before 首次使用 `to-issues`、`to-prd`、`triage`、`diagnose`、`tdd`、`improve-codebase-architecture`、`zoom-out`，或当这些 skill 缺少 issue tracker、标签、领域文档上下文时使用。
disable-model-invocation: true
---

# Setup Matt Pocock's Skills（配置 Agent Skills）

默认使用简体中文与用户沟通。说明、选择题、确认草稿和完成总结都用中文；文件名、命令、标签名和配置 key 保持原文。

为工程类 skills 生成每个仓库需要的配置：

- **Issue tracker（问题跟踪器）**：issue 存放在哪里，默认 GitHub，也内置支持本地 markdown。
- **Triage labels（分诊标签）**：五个标准 triage roles 使用的字符串。
- **Domain docs（领域文档）**：`CONTEXT.md` 和 ADR 的位置，以及读取规则。

这是 prompt-driven skill（提示驱动技能），不是确定性脚本。先探索，展示发现，向用户确认，再写入。

## Process

### 1. 探索

查看当前仓库的起始状态。读取已有内容，不要假设：

- `git remote -v` 和 `.git/config`：这是 GitHub 仓库吗？是哪一个？
- 根目录的 `AGENTS.md` 和 `CLAUDE.md`：是否存在？是否已有 `## Agent skills` 区块？
- 根目录的 `CONTEXT.md` 和 `CONTEXT-MAP.md`
- `docs/adr/` 以及任何 `src/*/docs/adr/` 目录
- `docs/agents/`：之前是否已经运行过本 skill？
- `.scratch/`：这可能表示已经使用本地 markdown issue tracker 约定

### 2. 展示发现并询问

总结当前存在什么、缺少什么。然后带用户逐一完成三个决策：一次只展示一个部分，拿到用户答案后再进入下一个。不要一次性抛出三个问题。

假设用户不知道这些术语的含义。每一部分先给简短解释：它是什么、这些 skill 为什么需要它、不同选择会改变什么。然后展示选项和默认值。

**A 部分：Issue tracker（问题跟踪器）。**

> 解释：issue tracker 是这个仓库保存 issue 的地方。`to-issues`、`triage`、`to-prd`、`qa` 等 skill 会读取和写入它，因此需要知道是调用 `gh issue create`、在 `.scratch/` 下写 markdown 文件，还是遵循你描述的其他流程。选择你实际跟踪工作的地方。

默认姿态：这些 skill 是为 GitHub 设计的。如果 `git remote` 指向 GitHub，建议 GitHub。如果 `git remote` 指向 GitLab，例如 `gitlab.com` 或自托管 GitLab，建议 GitLab。否则，或用户偏好其他方式，提供：

- **GitHub**：issue 存在仓库的 GitHub Issues 中，使用 `gh` CLI。
- **GitLab**：issue 存在仓库的 GitLab Issues 中，使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。
- **Local markdown（本地 markdown）**：issue 作为文件存放在本仓库的 `.scratch/<feature>/` 下，适合个人项目或没有 remote 的仓库。
- **Other（其他）**：Jira、Linear 等。要求用户用一段话描述工作流；skill 会把它记录成自由文本。

**B 部分：Triage label vocabulary（分诊标签词表）。**

> 解释：当 `triage` skill 处理新 issue 时，会把它推进一个状态机：需要评估、等待报告者、可交给 AFK agent、需要人工处理、或不会修复。为此，它需要应用与你实际配置的字符串匹配的标签，或 issue tracker 中的等价机制。如果仓库已经使用不同标签名，例如 `bug:triage` 而不是 `needs-triage`，在这里建立映射，避免 skill 创建重复标签。

五个标准角色：

- `needs-triage`：需要维护者评估
- `needs-info`：等待报告者
- `ready-for-agent`：规格完整，适合 AFK agent 接手，无需额外人工上下文
- `ready-for-human`：需要人工实现
- `wontfix`：不会处理

默认值：每个角色的字符串等于角色名。询问用户是否要覆盖。如果 issue tracker 没有现有标签，默认值即可。

**C 部分：Domain docs（领域文档）。**

> 解释：一些 skill，例如 `improve-codebase-architecture`、`diagnose`、`tdd`，会读取 `CONTEXT.md` 来理解项目领域语言，并读取 `docs/adr/` 来了解过去的架构决策。它们需要知道仓库是一个全局上下文，还是多个上下文，例如 monorepo 中前端/后端各自独立，才能看对位置。

确认布局：

- **Single-context（单上下文）**：仓库根目录有一个 `CONTEXT.md` 和 `docs/adr/`。大多数仓库属于这种。
- **Multi-context（多上下文）**：根目录有 `CONTEXT-MAP.md`，指向每个上下文自己的 `CONTEXT.md`，常见于 monorepo。

### 3. 确认并编辑

向用户展示草稿：

- 要加入 `CLAUDE.md` 或 `AGENTS.md` 的 `## Agent skills` 区块，选择规则见步骤 4。
- `docs/agents/issue-tracker.md`、`docs/agents/triage-labels.md`、`docs/agents/domain.md` 的内容。

写入前允许用户修改。

### 4. 写入

**选择要编辑的文件：**

- 如果 `CLAUDE.md` 存在，编辑它。
- 否则如果 `AGENTS.md` 存在，编辑它。
- 如果两者都不存在，询问用户创建哪一个，不要替用户决定。

当 `CLAUDE.md` 已存在时，不要创建 `AGENTS.md`，反之亦然；始终编辑已经存在的那个。

如果选定文件中已经有 `## Agent skills` 区块，原地更新内容，不要追加重复区块。不要覆盖周边用户编辑。

区块内容：

```markdown
## Agent skills

### Issue tracker

[一行总结 issue 存放在哪里]。见 `docs/agents/issue-tracker.md`。

### Triage labels

[一行总结标签词表]。见 `docs/agents/triage-labels.md`。

### Domain docs

[一行总结布局：single-context 或 multi-context]。见 `docs/agents/domain.md`。
```

然后使用本 skill 文件夹中的种子模板作为起点，写入三个文档文件：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub issue tracker
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab issue tracker
- [issue-tracker-local.md](./issue-tracker-local.md)：本地 markdown issue tracker
- [triage-labels.md](./triage-labels.md)：label 映射
- [domain.md](./domain.md)：领域文档读取规则和布局

对于“其他” issue tracker，根据用户描述从零写 `docs/agents/issue-tracker.md`。

### 5. 完成

告诉用户配置已完成，并说明哪些工程类 skills 之后会读取这些文件。说明他们以后可以直接编辑 `docs/agents/*.md`；只有想切换 issue tracker 或从头配置时，才需要重新运行本 skill。

---
name: handoff
description: 把当前对话压缩成 handoff document（交接文档），方便另一个 agent 继续处理。Use when 用户要交接当前工作、压缩上下文、保存进度、准备新会话或让下一个 agent 接手。
argument-hint: "下一个会话要用来做什么？"
---

默认使用简体中文。handoff document（交接文档）用中文编写；代码、命令、路径、issue 编号、PR 链接、错误日志保持原文。

编写一份 handoff document，总结当前对话，让新的 agent 能继续工作。保存到 `mktemp -t handoff-XXXXXX.md` 生成的路径；写入前先读取该文件。

如果下一个会话适合使用某些 skills，明确建议。

不要重复已经记录在其他工件中的内容，例如 PRD、计划、ADR、issue、commit、diff。改为通过路径或 URL 引用它们。

如果用户传入参数，把它视为下一个会话的关注点描述，并据此定制文档。

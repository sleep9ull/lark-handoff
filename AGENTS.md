# lark-handoff

本仓库存放供 Codex 执行飞书交接任务的技能和契约。解释、任务评论和文档默认使用中文。

- 用户要求扫描、接管或合并飞书交接任务时，读取 [.agents/skills/lark-handoff/SKILL.md](.agents/skills/lark-handoff/SKILL.md)。
- 修改本项目技能时，同时核对它引用的状态契约、执行流程和审计恢复规则，保证定义一致。创建技能不等于执行清单中的任务。
- 任务目标代码在任务「所属项目」对应的本地项目中修改，本仓库只是入口；本机路径写入已忽略的 `handoff.local.json`。
- Commit message 格式：`feature(scope): description`、`fix(scope): description` 等，正文用 `- ` 列出实际改动。

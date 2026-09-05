# lark-handoff

让 Codex 从飞书任务清单接管工作，在对应本地项目的任务分支上实现、自测、推送并交付 PR；人类验收后通过 PR 合并至 `main`，核实结果后直接标记 `done`。执行记录回写到原任务的评论中。

## 入口

- [lark-handoff 技能](.agents/skills/lark-handoff/SKILL.md)：手动或 Scheduled 任务调用同一个入口。
- [状态契约](.agents/skills/lark-handoff/references/contract.md)：六阶段定义、授权边界和项目映射。
- [多 Agent 调度](.agents/skills/lark-handoff/references/dispatch.md)：任务分配、并发边界和执行者收尾。
- [执行流程](.agents/skills/lark-handoff/references/execution.md)：开发、验收交付、合并及异常处理。
- [飞书接口](.agents/skills/lark-handoff/references/lark.md)：读取、自定义状态更新、评论和权限。
- [审计与恢复](.agents/skills/lark-handoff/references/audit.md)：评论格式、运行锁、幂等恢复。
- [Scheduled 运行说明](docs/scheduled.md)：启动条件与可复制的任务提示词。

主 Agent 负责调度，每个 sub-agent 执行一个飞书任务；独立开发任务可并行，同远端仓库合并串行。工作流由 Codex 按技能执行；本项目不提供独立常驻服务。

## 本机设置

1. 安装并授权 `lark-cli`，准备 `lark-task`、`lark-shared` 技能；若当前 CLI 缺少读取评论的封装，还需 `lark-openapi-explorer`。
2. 用 Codex 打开本仓库。项目技能存放在 `.agents/skills/`；如当前会话尚未发现新技能，可直接让 Codex 读取上面的 `SKILL.md`。
3. 复制本机配置并将示例路径改成真实路径：

   ```bash
   cp handoff.local.example.json handoff.local.json
   ```

4. 对照[飞书接口说明](.agents/skills/lark-handoff/references/lark.md)补齐权限和状态选项，再进行一次只读预检。

[handoff.json](handoff.json) 保存清单、状态字段和六个精确状态名称。本机配置不提交；凭据由 `lark-cli` 管理。

当前绑定的是本人 `handoff` 清单，GUID 与原 `codex-handoff` 相同。状态选项 GUID 每次从接口解析，不保存在配置中。

## 使用

只读检查：

```text
使用 $lark-handoff 对当前 handoff 配置做只读预检，列出可执行任务、项目映射、缺失的权限和状态选项；不写入评论、状态或代码。
```

执行一轮：

```text
使用 $lark-handoff 执行一轮 handoff 扫描，按契约处理 ready-for-agent 和 ready-to-merge，并将每项执行结果评论到原任务。
```

启用前需完成本机路径配置、核验写入权限、补齐状态选项及分组读取权限。建立技能文件不会自动创建 Scheduled 任务或变更线上任务。

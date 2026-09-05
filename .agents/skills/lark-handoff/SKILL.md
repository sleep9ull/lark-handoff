---
name: lark-handoff
description: 从飞书 handoff 清单接管 ready-for-agent 任务，在分组对应的本地 Git 项目中开发交付；处理人类验收后的 ready-to-merge 任务并回写评论审计。支持执行前的只读预检。
---

# 飞书任务交接

供 Codex 手动调用或在 Scheduled 任务中执行一轮。以仓库根目录的 `handoff.json` 为清单配置，`handoff.local.json` 为本机项目配置；从本技能位置向上定位仓库，勿用目标代码项目的同名配置替代。

## 每轮入口

1. 读取 [contract.md](references/contract.md)，区分只读预检与执行一轮。只读预检仅报告发现，不写任务、评论、锁或项目文件。
2. 按 [lark.md](references/lark.md) 预检身份、字段和权限，完整分页读取指定清单、任务详情和分组。以自定义「任务状态」解析规范状态，保留原始名称和 GUID。未知状态或配置冲突须报告，不能凭标题或评论推测。
3. 执行模式先按 [audit.md](references/audit.md) 获得本机清单运行锁，恢复待回写记录，再筛选 `ready-for-agent` 与 `ready-to-merge`。恢复只补齐已发生操作的审计，不在其他状态启动新的开发或合并。
4. 主 Agent 对候选任务读取全部评论，解析并验证项目位置，按 [dispatch.md](references/dispatch.md) 将每个飞书任务分配给一个 sub-agent；独立任务在可用并发额度内执行，其余排队。只读预检不派发执行者。
5. sub-agent 开始前重新读取状态与任务内容，确认仍满足触发条件，按 [execution.md](references/execution.md) 对应分支执行，按 [audit.md](references/audit.md) 完成评论和状态回读。人类中途撤回或修改要求时保存现场并停止该项操作。成功、失败、低置信度、阻塞都要有审计结果；权限导致无法评论时保留本地待回写记录并报告。
6. 主 Agent 等待全部 sub-agent 及关联进程停止，核对每项 journal、评论和状态结果后，释放本轮拥有的锁。报告完成项和需要用户处理的问题；没有候选、没有待恢复结果、也没有新问题时保持安静。

## 完成标准

- `ready-for-agent`：交付可检查的任务分支、提交与 PR、相关验证结果、验收指引；转入 `ready-to-review`，或说明不确定性后转入 `need-to-refine`。
- `ready-to-merge`：确认已验收 PR 已合并且批准对应的提交已进入远端 `origin/main`，评论合并证据并转入 `done`。
- 具体转换、授权和例外以状态契约为准；状态更新与评论不是原子操作，完成前必须核对两者。

创建或修改本技能时，使用 [validation.md](references/validation.md) 检查真实分支决策。首次配置及 Scheduled 提示词见仓库的 [运行说明](../../../docs/scheduled.md)。

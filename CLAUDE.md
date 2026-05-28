# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目性质

这是一个**文档驱动的 Skill 定义项目**，不含传统代码。核心产物是 `SKILL.md`，定义了一个在 Hermes 平台上运行的多 Agent 协作团队设计技能。没有构建、测试或 lint 命令。

## 核心文件

- `SKILL.md` — 完整的技能定义（826行），包含角色模板、工作流程、输出规范
- `README.md` — 使用指南，说明如何调用 Skill 和创建 Profile
- `references/hermes-integration-open-questions.md` — 待解决的技术集成问题

## 架构概览

### 调度模型

Orchestrator（主 Agent）通过终端命令调度子 Agent：

```
<profile_name> chat -q "任务描述"
```

子 Agent 收到任务后直接执行，**无确认环节**。Orchestrator 只做调度和汇总，不做具体工作。

### 输出结构

每次设计生成：

```
<task-name>/
├── souls/
│   ├── orchestrator_soul.md      # 主 Agent（含团队定义+调度协议）
│   └── [role]_soul.md            # 各子 Agent
├── team_workflow.md
└── [task_name]_team_spec.md      # 完整汇总
```

### 角色体系

- **Orchestrator**：唯一与用户对话的主 Agent，负责任务拆解、分配、汇总
- **Reviewer**：独立质疑角色，**必须是独立 Agent**，不能与 Orchestrator 合并
- **Analyst / Creator / Specialist / Executor**：按需添加的专业角色

团队规模：一般任务 3-4 人，复杂任务 5-10 人，最多 10 人。

## 关键约束

修改 `SKILL.md` 时必须遵守：

1. **Reviewer 必须独立** — 不能与 Orchestrator 合并，保证"第二双眼睛"的价值
2. **每个 Agent 必须包含"耐心"和"质疑思维"** — 基础性格要求
3. **禁止生成"总是附和用户"的角色**
4. **任务描述必须准确** — 子 Agent 不会确认，收到什么执行什么
5. **不在需求讨论完成前生成初稿**，不在自审完成前交付

## 六阶段工作流程

Stage 1: 需求讨论 → Stage 2: 生成初稿 → Stage 3: 内部评审（10项检查清单）→ Stage 4: 修订 → Stage 5: Profile 部署 → Stage 6: 交付确认

## Stage 3 自审检查清单

修改 `SKILL.md` 后，用此清单快速验证：

1. 完整性：所有必需角色都存在？
2. Reviewer 独立性：是否是独立角色，未与 Orchestrator 合并？
3. 调度协议：每个子 Agent 的调度命令是否写清楚了？
4. 上下文传递：调度命令是否包含完整的输入材料、输出要求和背景约束？
5. 任务分配前置检查：Orchestrator 是否有自我审查任务描述的步骤？
6. 性格一致性：每个角色是否有"耐心"和"质疑思维"？
7. 禁止项检查：有没有生成"总是附和用户"的角色？
8. 子 Agent 隔离：子 Agent 的 soul.md 是否不含调度逻辑和团队信息？
9. 协作流程：流程是否闭环？有没有角色无事可做？
10. 规模限制：总人数是否不超过10人？

## 上下文传递格式

调度命令推荐格式：

```
<profile> chat -q "
[任务目标]: 具体要做什么
[输入材料]: 文件路径 或 前序输出摘要
[输出要求]: 格式、长度、结构
[背景约束]: 业务背景、专业术语说明（如有）
"
```

传递前序输出的方式：
- 文件：直接传文件路径，如 `/tmp/analyst_output.md`
- 文本：将关键结论内嵌到任务描述里（不超过200字）
- 长内容：先写入临时文件，再传路径

## 子 Agent 失败处理

| 情况 | Orchestrator 操作 |
|------|-----------------|
| 返回空结果 | 检查任务描述是否完整，重新调度一次 |
| 质量不达标 | 附上具体反馈，重新调度（最多重试1次） |
| 重试后仍失败 | 向用户报告，说明原因，询问是否降级或跳过 |
| 执行报错 | 立即向用户报告错误信息，不继续后续步骤 |

原则：不静默失败，不无限重试。最多重试1次，之后必须向用户报告。

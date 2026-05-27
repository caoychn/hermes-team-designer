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

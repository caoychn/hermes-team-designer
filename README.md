# Agent Team Designer 使用指南

## 什么是 Agent Team Designer

一个 Hermes Skill，根据任务需求设计多 Agent 协作团队，并自动将团队部署到 Hermes profiles。

典型场景：
- 数据分析（加载数据 → 分析 → 写报告）
- 内容创作（策划 → 写作 → 审核）
- 代码审查（功能审查 → 安全审查）
- 研究报告（信息收集 → 撰写 → 质量把关）
- 任何需要多角色分工配合的任务

---

## 工作流程

### 第一步：提出需求

向 Hermes 描述你的任务，比如：
- "帮我分析这份销售数据，输出文字报告"
- "做一个读书笔记账号，每周产出 3 篇笔记"
- "搭建一个代码审查团队"

### 第二步：需求讨论

Skill 会先和你讨论几个关键问题（最终用户是谁、团队规模偏好、交付形式等），然后再开始设计。如果你的需求已经足够详细，讨论会自动精简。

### 第三步：生成团队设计

讨论完成后，Skill 生成以下文件：

```
<团队名>/
├── souls/
│   ├── orchestrator_soul.md   # 主 Agent（调度员）
│   ├── xxx_soul.md            # 子 Agent A
│   └── yyy_soul.md            # 子 Agent B
├── team_workflow.md            # 协作流程
└── xxx_team_spec.md           # 完整规格说明
```

### 第四步：内部自审

Skill 对生成的团队进行结构化自检（角色完整性、调度协议、上下文传递、失败处理等），发现问题自动修订，然后向你输出自审报告，等待确认。

### 第五步：自动部署到 Hermes

你确认后，Skill 自动完成 Profile 部署：

1. **创建 Orchestrator profile**
   - 如果 `orchestrator` 不存在 → 直接创建
   - 如果已存在 → 创建 `orchestrator_<task_name>`，避免覆盖其他团队
   - 内容 = `orchestrator_soul.md` + `team_workflow.md` 合并写入

2. **创建/更新子 Agent profiles**
   - 不存在的 profile → `hermes profile create` 新建
   - 已存在的 profile → 先备份原 `SOUL.md` 为 `SOULBACK.md`，再覆盖

3. **输出部署报告**，告知每个 profile 的操作结果

### 第六步：开始工作

部署完成后，直接用主 Agent 启动：

```bash
orchestrator_<task_name> chat
# 或者（如果 orchestrator 是新建的）
orchestrator chat
```

---

## 团队规模

| 任务复杂度 | 团队规模 | 角色组合 |
|-----------|---------|---------|
| 一般任务 | 3-4 人 | Orchestrator + 2-3 个专业角色 |
| 复杂任务 | 5-6 人 | Orchestrator + 4-5 个专业角色 |

Orchestrator 只做调度，不做具体工作。

---

## 核心特点

**真调用，不是角色扮演**
子 Agent 收到任务后真的在终端执行，会调用工具、读写文件、跑代码，不是在主 Agent 的回复里模拟对话。

**上下文完整传递**
主 Agent 调度子 Agent 时，任务描述包含完整的输入材料、输出要求和背景约束，子 Agent 收到什么执行什么。

**失败有处理，不静默**
子 Agent 失败时，主 Agent 最多重试一次，之后向用户报告原因，不无限重试、不静默跳过。

**成员按需参与**
不是每次任务都全员参与。主 Agent 根据实际需要决定谁上场，交付时说明谁参与了、谁跳了及原因。

**越用越强**
子 Agent 的 session 历史会积累用户反馈，长期使用后对你的偏好越来越熟悉。

---

## 示例：数据分析团队

**需求**：分析 CSV 文件，输出文字报告

**Skill 生成并部署的团队**：

| 角色 | Profile 名 | 职责 |
|------|-----------|------|
| 数据分析主管 | `orchestrator_data_analysis` | 调度、汇总、交付 |
| Data Analyst | `analyst` | 数据加载、清洗、分析 |
| Insight Writer | `writer` | 撰写文字报告 |

**部署完成后直接启动**：

```bash
orchestrator_data_analysis chat
```

**工作流**：
```
用户 → 数据分析主管：分析这份CSV
     → analyst（terminal 执行）：加载数据、清洗、分析
     → writer（terminal 执行）：写报告
     → 数据分析主管 → 用户：交付报告
```

---

## 团队迭代

使用一段时间后，如果某个角色表现不符合预期，可以重新调用 Skill 修改对应的 soul.md，然后重新部署。原 SOUL.md 会自动备份为 SOULBACK.md，可以随时回滚。

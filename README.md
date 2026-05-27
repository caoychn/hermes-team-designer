# Agent Team Designer 使用指南

## 什么是 Agent Team Designer

一个技能（Skill），用于根据具体任务需求，快速设计一个小型 Agent 协作团队。

典型场景：
- 数据分析（加载数据 → 分析 → 写报告）
- 内容创作（策划 → 写作 → 审核）
- 任何需要多角色分工配合的任务

---

## 工作流程

### 第一步：提出需求

用户描述任务需求，比如：
- "帮我分析这份销售数据，输出文字报告"
- "做一个读书笔记账号，每周产出 3 篇笔记"

### 第二步：调用 Skill 组队

Hermes 调用 `agent-team-designer` skill，进入五阶段流程：

```
讨论 → 初稿 → 评审 → 修订 → 交付
```

Skill 会询问几个关键问题（任务复杂度、团队规模偏好、交付形式等），然后生成团队设计。

### 第三步：获得团队文件

Skill 生成以下文件：

```
<团队名>/
├── souls/
│   ├── orchestrator_soul.md   # 主Agent（调度员）
│   ├── xxx_soul.md            # 子Agent A
│   └── yyy_soul.md            # 子Agent B
├── team_workflow.md            # 协作流程
└── xxx_team_spec.md           # 完整规格说明
```

### 第四步：创建 Profile

用户自己在 Hermes Agent 应用上：
1. 为每个子 Agent 创建 Profile
2. Profile 名必须是**纯小写英文**（如 `analyst`、`writer`）
3. 将对应 soul.md 的内容复制到 Profile 的 soul 配置中
4. 修改角色名字（可以中文，如"数据分析师"）

主 Agent（Orchestrator）不需要单独创建 Profile，用户直接和它对话即可。

### 第五步：通过主 Agent 工作

用户向主 Agent 发起任务，主 Agent 自动：
1. 拆解任务，决定哪些成员参与
2. 用 terminal 调度子 Agent 执行
3. 收集结果，汇总交付给用户
4. 说明本次谁参与了、谁跳了及原因

---

## 团队规模

- 一般任务：**3-4 人**（Orchestrator + 2-3 个专业角色）
- 复杂任务：**5-6 人**（Orchestrator + 4-5 个专业角色）
- Orchestrator 只做调度，不做具体工作

---

## 核心特点

### 真调用，不是角色扮演

子 Agent 收到任务后，**真的在终端执行**，会真正调用工具、读写文件、跑代码。不是在主 Agent 的回复里假装对话。

### 成员按需参与

不是每次任务都全员参与。主 Agent 根据任务实际需要决定谁上场、谁休息，并在交付时说明情况。

### 越用越强

子 Agent 每次执行后，用户可以反馈结果。这个反馈会进入它的 session 历史，影响后续表现。长期使用后，Agent 会对你的偏好越来越熟悉。

---

## 示例：数据分析团队

**需求**：分析 CSV 文件，输出文字报告

**Skill 生成的团队**：

| 角色 | Profile 名 | 职责 |
|------|-----------|------|
| 数据分析主管 | （直接对话） | 调度、汇总、交付 |
| Data Analyst | `analyst` | 数据加载、清洗、分析 |
| Insight Writer | `writer` | 撰写文字报告 |

**用户创建 Profile**：
- Profile 1：`analyst`，soul = analyst_soul.md 内容，角色名"数据分析师"
- Profile 2：`writer`，soul = writer_soul.md 内容，角色名"报告撰写员"

**工作**：
```
用户 → 数据分析主管：分析这份CSV
     → analyst（terminal 执行）：加载数据、清洗、分析
     → writer（terminal 执行）：写报告
     → 数据分析主管 → 用户：交付报告（说明analyst和writer都参与了）
```

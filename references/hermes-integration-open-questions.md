# Hermes 多Agent团队集成 — 开放问题

> 本文档记录设计多Agent团队时遇到的技术集成问题，供后续探索。

---

## 问题：设计好的Agent团队如何在Hermes中真正运行？

hermes-team-designer skill可以生成：
- 每个Agent的 soul.md（人格定义）
- team_workflow.md（协作流程）
- 完整的团队规格文档

但以下问题尚未解决：

1. **Agent发现机制** — 如何让一个运行中的Agent识别并调用设计好的其他Agent？是通过共享context文件、还是某种注册机制？

2. **Orchestrator角色** — 是由当前会话Agent（即主Agent）兼任，还是需要通过cronjob或独立进程启动一个独立Agent？

3. **多Agent通信** — Agent之间是共享context来协作，还是通过文件/消息队列来传递信息？

4. **Soul文件加载** — 如何让Hermes加载soul.md作为Agent身份？是通过配置还是运行时指令？

---

## 可能的解决方向

- 检查Hermes是否有多Agent协作的文档或示例
- 查看Hermes的github issues是否有关于multi-agent的讨论
- 尝试用delegate_task模拟团队协作，观察行为
- 参考data-analysis-team skill的实现方式（如果可行）

---

## 相关文件

- 设计输出：~/.hermes/agent-teams/
- 团队设计skill：~/.hermes/skills/hermes-team-designer/SKILL.md

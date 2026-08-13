# Subagent

为了节省 main session 的 ctx， 我们常常需要开启 subagent 来帮忙处理一些中间的产物，比如说 log，等垃圾内容，防止这些东西消耗和污染我们宝贵的 main session 上下文

在 codex 中的系统提示词中，有一句类似的表述：除非用户,agents.md, skill 主动要求使用 subagent 否则就绝不使用。

所以我们往往需要在 global AGENTS.md 中添加上这一句

```markdown
## Subagents

- Use subagents for bounded, independent work when parallelism or context isolation would materially help, especially for noisy investigations and experiments; keep trivial or tightly coupled work in the main agent.
- Keep goals, decisions, and final synthesis in the main agent; require subagents to return concise evidence and conclusions.
- When a subagent completes its task, collect its result, then close its thread to release the slot; use a fresh subagent for unrelated work.
```

关于 global AGENTS.md 的其他条目，可以参考： https://github.com/hanjie-chen/personal-config/blob/main/codex/AGENTS.md
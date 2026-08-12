# Context Engineering

session 的上下文会随会话消失，repo 里的东西会留下来。

context engineering 就是管理这两者之间的信息流动。

做个比喻来说的话，类似于干木工：

- 开工前，从仓库把需要的图纸和材料搬上工作台 → 入
- 干活时，台面就这么大，锯末刨花别堆上去 → 中
- 收工前，成品和半成品必须归位，因为台面明早会被清空 → 出

分成三个方向：

## 入口：repo → session

让 agent 用最少的 token 建立对 repo 的正确认知。

- 文档分层（README 三层 / AGENTS 三层 / docs/ ）→ doc-layering.md
- 规则跟着目标仓库走 → cross-git-repo-in-one-session.md
- 路径即分类 → ../agent-foundations/filesystem-as-context.md

## 运行中：运行时的流量控制

防止中间产物污染主 context。

- subagent 隔离：实验的 log、代码、设计过程留在子会话，只回来一个结论

  在 global agents.md 中写上

  ```markdown
  ## Subagents
  
  - Use subagents for bounded, independent work when parallelism or context isolation would materially help, especially for noisy investigations and experiments; keep trivial or tightly coupled work in the main agent.
  - Keep goals, decisions, and final synthesis in the main agent; require subagents to return concise evidence and conclusions.
  - When a subagent completes its task, collect its result, then close its thread to release the slot; use a fresh subagent for unrelated work.
  ```

  关于 global agents.md 可以参考 https://github.com/hanjie-chen/personal-config/blob/main/codex/AGENTS.md

- skill：把重复摸索固化，避免每次都用 token 重新试错 → skill-guide.md

## 出：session → repo

会话中产生的状态，必须在 session 结束前落盘。

- commit：代码
- decisions.md：已经想清楚的结论
- handoff.md：还没成型、没有归宿的半成品 → handoff-vs-auto-compact.md
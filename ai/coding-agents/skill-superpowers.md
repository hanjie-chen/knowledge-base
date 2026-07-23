# superpowers

source: [obra/superpowers: An agentic skills framework & software development methodology that works.](https://github.com/obra/superpowers)

superpowers 包含一整套 skill，包括一个顶层 using-superpowers 作为总入口和使用指南，其他的 skill 分别对应设计、计划、调试、测试、评审、分支收尾等具体环节。

默认是：澄清需求/设计（braining storm） → 写实施计划（writing plans） → 实现（worktree + subagnet drive） → 做测试和 review → 收尾验证。

## download

直接告诉 codex 让他自行安装即可

Codex 启动时会扫描 `~/.agents/skills/`，读取 skill 的说明，然后在对话中按需触发。`using-superpowers` 这个总控 skill 会被自动发现，并负责把其他 skill 带起来。

# Learn superpowers

安装好之后，日常使用时，基本上可以依赖自动触发（using-superpowers）然后观察它的流程，自然的就能明白这些 skill 是做什么的

# Personal usage notes

## subagent-drive

当写完 plan 开始使用 Subagent-Driven 的时候，最好关闭 codex 的 fast mode(1.5 speed, 2 times usage) 不然很容易就把这个 5 hours limits 给消耗完。

> [!note]
>
> 像 `dispatching-parallel-agents` 和 `subagent-driven-development` 这类 skill，需要在 Codex 配置里开启 `multi_agent = true`

关于页面上小的改动，就不要开启 subagent-drive 模式，也不需要写什么 plan, 因为这里及其消耗额度和 token

原因是，他的 code review 会新开一个 subagent 来做，而主进程则是一个 manager 的类似职责




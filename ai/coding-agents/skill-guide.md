# skills

随着 agent 能力越来越强，skills 需要遵循一个原则，less is more

越少的 skill 越好

当你的 skill 越来越多，就意味着你的上下文被占用的越多。如果一个 skill 可以帮你解决特定的问题。

请阅读：https://x.com/systematicls/status/2028814227004395561

# superpowers

source: [obra/superpowers: An agentic skills framework & software development methodology that works.](https://github.com/obra/superpowers)

superpowers 包含一整套 skill，包括一个顶层 using-superpowers 作为总入口和使用指南，其他的 skill 分别对应设计、计划、调试、测试、评审、分支收尾等具体环节。

默认是：澄清需求/设计（braining storm） → 写实施计划（writing plans） → 实现（worktree + subagnet drive） → 做测试和 review → 收尾验证。

直接告诉 codex 让他自行安装即可

Codex 启动时会扫描 `~/.agents/skills/`，读取 skill 的说明，然后在对话中按需触发。`using-superpowers` 这个总控 skill 会被自动发现，并负责把其他 skill 带起来。

安装好之后，日常使用时，基本上可以依赖自动触发（using-superpowers）然后观察它的流程，自然的就能明白这些 skill 是做什么的




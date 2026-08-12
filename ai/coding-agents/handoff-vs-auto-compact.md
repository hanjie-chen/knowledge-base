# background

当我们在和一个 coding agent 协作的时候，如果触发了上下文的限制，这个时候存在两种选择：

1. 触发 coding agent 自动压缩，然后继续聊
2. 我们主动写一个 docs/handoff.md 记录当前的信息，然后新开一个 session 去读这个 handoff.md 然后继续
3. `/compact <instructions>` 主动压缩，但是保留 session
4. `/clear` + 让 agent 自读 `AGENTS.md` + `git log` 的极简派。

所以问题在于这里两个方案哪一个更好呢？

我个人倾向于方案2

因为从本质上来说，auto-compact 和 handoff.md 都是压缩，都会丢失信息，但是：

- auto-compact 是被动压缩，是黑盒，你无法看见丢失了什么信息，保存了什么信息。
- handoff.md 则是主动压缩，由你可以决定（告诉模型以及可以自行修改），保存什么信息。

那么接下来，有一个实际的问题，什么时候写 handoff.md 呢？

1. 观察 ctx(context windows) 到了多少，比如说目前的 ctx 已经被占用了 80% 那么就开始写 handoff.md 否则就继续聊。
2. 按照 task 阶段边界，当 coding agent 进行某个 task 快要做完的时候，写一份 handoff 开新的 session

## 如何写一个好的 handoff.md 呢

那么什么样子的 handoff.md 是足够合格的呢？

我认为至少要回答这里三个问题：

1. 现在做到哪了（做完了什么、改了哪些文件）
2. 接下来第一步是什么
3. 有什么坑/未决问题（比如"这个方案还没测过"、"用户说过 X 但还没写进文档"）

# next action

这里其实本质还是优化 coding agent context windows 的问题，我们如何优化上下文呢？合理使用 subagent 是一个很好的选择。

让 subagent 去完成一个容易隔离的实验和任务，然后回来汇报结果就行了。而如果是在 main session 中进行这个实验/任务的话，就会有大量的中间产物（实验设计，代码，log）等来消耗和污染上下文。
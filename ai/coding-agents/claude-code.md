# claude-code cli

下载 claude code（以下简称 cc）

[Quickstart - Claude Code Docs](https://code.claude.com/docs/en/quickstart)

windows 上面只需要一行代码

```powershell
irm https://claude.ai/install.ps1 | iex
```

使用下面的命令查看 cc 当前版本和最新版本

```shell
claude --version
claude doctor
```

如果有新版本，可以使用这个命令升级

```shell
claude update
```

# 使用指南

## 常用command

/exit: 退出对话

/btw: 在当前上下文开一个临时的对话框询问信息（by the way）提问完成之后关闭不会污染上下文。使用方法是 ` /btw <question> ` 在命令之后空格直接加上问题。然后 esc 退出

/insights: 会生成一份HTML报告，分析你过去一个月使用Claude Code的习惯，包括你最常用哪些命令，你有哪些重复性的操作模式，然后给你推荐一些自定义命令和Skills。
这个命令生成的报告存储位置是固定在 ` ~/.claude/usage-data/report.html ` 每次运行 `/insights` 会覆盖上一次的报告，不会保留历史。

/simplify: 启动三个平行的Agent，分别从代码复用、代码质量、运行效率三个角度审查你的改动。注意，是没有 commit 过的代码.

/export: 导出对话（如果有遇到自动压缩，那么别被压缩的部分只有摘要）

## 其他技巧

ctrl + j: 换行

ctrl + u: 删除整行输入

@filename: 使用 @ 可以引用文件，需要注意 @ 和之前的字符需要保持一个空格

!: 执行终端命令

# WebSearch vs WebFetch

|                    | WebSearch                                                | WebFetch                                            |
| ------------------ | -------------------------------------------------------- | --------------------------------------------------- |
| **作用**           | 搜索引擎——输入关键词，返回搜索结果列表（标题+链接+摘要） | 网页抓取——输入具体 URL，抓取并解析该页面的完整内容  |
| **执行位置**       | Anthropic 服务端                                         | 你的本地机器                                        |
| **类比**           | 相当于在 Google 搜索栏输入关键词                         | 相当于在浏览器里打开一个具体网址看内容              |
| **典型用途**       | "Claude Code agent teams 怎么配置" → 返回相关文章链接    | "帮我看看这个文档页面说了什么" → 抓取并分析页面内容 |
| **受本地网络限制** | 否                                                       | 是（加代理后可解决）                                |

简单说：WebSearch 找到去哪看，WebFetch 去看具体内容。通常配合使用——先搜索找到相关链接，再用 WebFetch 抓取具体页面深入阅读。

让 Claude Code 使用代理 — 在 `~/.claude/settings.json` 的 `env` 字段中设置了：
```json
"env": {
    "http_proxy": "http://127.0.0.1:10808",
    "https_proxy": "http://127.0.0.1:10808"
}
```

这样 Claude Code 启动时会注入这两个环境变量，WebFetch 等网络工具就会走代理。如何使用了这个设置，那么平时的 claude code 链接api也会走这个代理

# CLAUDE.md

一个好的 claude.md 需要包含这些内容

 1. 项目一句话简介 — 让cc秒懂这是什么
 2. 目录结构 + 每个文件夹的用途 — 让cc需要找什么就去哪里，不用乱翻
 3. 关键设定文件索引 — 按需读取
 4. 当前进度 — 做到那里，现在在解决什么问题

# claude code 配置

claude code 模型权限、默认模式、项目规则、hooks、sandbox 等正式配置主要放在 `settings.json`，

如果要手动配 Claude Code，主要看这三层：

- 用户级：`~/.claude/settings.json`，对所有项目生效
- 项目级：`<repo>/.claude/settings.json`，会随 git 分享给团队
- 本地项目级：`<repo>/.claude/settings.local.json`，只在你本机这个仓库生效，且会被 git ignore

优先级是 Local > Project > User。

## user settings.json

接下来我们来详细解释这个文件，我们先来看一个最基础的 settings.json

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "opus[1m]",
  "effortLevel": "medium",
  "defaultMode": "acceptEdits"
}
```

- schema: 配置官方给了 JSON Schema，编辑器能直接做自动补全和校验，减少拼错 key、配错位置这种低级错误。
- model: 选择模型
- effortLevel: 选择思考强度

接下来我们来看这一个字段 defaultMode 字段

权限模式，支持这些值：

- default 标准模式。
  - 默认可以读取文件
  - 想要执行命令的时候会问你，如果点击了 yes, don't ask again, 那么 cluade 会按项目目录 + 命令记住，以后类似命令就不再问
  - 修改文件是也会询问，但是这个 don't ask again 只到本轮 session 结束
- acceptEdits
  - 在 default 基础上加上默认可以修改文件的权限
- plan 只能分析，不能改文件，也不能跑命令
- dontAsk 没有预先放行的工具，一律自动拒绝
- bypassPermissions 几乎跳过权限提示

然后我们再来看 permission 字段

这个字段是“精细权限规则”。

- `allow`：直接允许
- `ask`：每次问
- `deny`：直接拒绝

并且规则匹配顺序是：先 deny，再 ask，再 allow，第一条匹配就生效。

# Context engineer

参考这篇文章：https://www.zhihu.com/question/1945503640539333416/answer/2016306836769355199

# 自动压缩造成的问题

当我们的 ctx(context windows) 达到 95% 的时候，会出发自动压缩，这往往会造成一些问题，比如说忘记我们之前的约定，一个好的方案是，我们在达到这个限制之前主动使用 `/compact` 命令，压缩。

但是我们不可能每个对话结束之后都使用 `/cost`, `/usage` 命令看当前的使用情况，所以我们可以在 cc(version ≥ v2.1.132) 底部配置一个状态行（Status Line）实时显示 token 用量。

直接使用 cc-usage 即可

# claude code memory Mechanism

claude code memory 是两套并行系统。

1. CLAUDE.md（你自己写的），用来放项目指令、编码规范、架构说明等。它有一整套层级结构——从组织级（managed policy）到用户级（`~/.claude/CLAUDE.md`）到项目级（`./CLAUDE.md`）再到本地个人级（`./CLAUDE.local.md`），启动时沿目录树向上逐级加载并拼接。还支持 `@path/to/file` 语法引入外部文件，以及 `.claude/rules/` 目录做按路径触发的条件规则。
2. Auto Memory（Claude 自己写的）

## Auto Memory

这是项目级别的的 memory

关于加载上限，MEMORY.md 的前 200 行或前 25KB，取先到者。

关于存储位置，每个项目的 auto memory 存在 `~/.claude/projects/<project>/memory/` 下，同一个 git 仓库的所有 worktree 和子目录共享同一个 memory 目录

第一层：MEMORY.md 索引 — 始终加载

每次对话开始时，MEMORY.md 的内容会自动注入到 context 中。可以在系统消息里看到它实际上已经被加载了：

第二层：具体记忆文件 — 按需读取

像 feedback_search_before_answer.md 这样的详细内容，不会自动加载。

只有当模型判断某条记忆和当前对话相关时，才会用 Read 工具去读取它。


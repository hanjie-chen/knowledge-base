# Knowledge Base

这是我的个人知识库，持续整理可复用的知识、经验和笔记，同时作为个人网站的内容来源。

- 个人网站：https://hanjie-chen.com/
- 网站源码：https://github.com/hanjie-chen/website

## How To Navigate

原则：路径即分类。

每深入一层目录都表示一个更具体的主题范围，文章的分类尽可能交给目录路径表达，从而使得文件名保持简洁。例如：`tools/git/commands.md`。

分类信息只存于路径，不额外维护：移动文件即重新分类。目录树则是人和 AI agent 共用的导航接口。

当前的顶层主题目录：

- `ai/` — AI 相关：LLM、coding agents、自动化平台
- `cloud-infra/` — 云平台（Azure / GCP / Cloudflare）与 IaC（Terraform / Ansible）
- `code/` — 编程语言、算法与工程实践
- `devops/` — Docker、Kubernetes 等
- `machine-setup/` — 个人机器配置：Windows / Linux / 网络
- `personal-growth/` — 非技术类：生活经验、理财、世界观
- `tools/` — 日常工具指南：Vim、PowerShell、PowerToys 等
- `web-dev/` — Web 开发：前端、后端、安全

如果你是第一次进入这个仓库，可以从上面的主题目录挑一个进入，再顺着各级目录的 `README.md` 往下阅读。

## Content Organization

除了本 `README.md`，部分目录也会有自己的 `README.md`。

它通常是该目录的入口页，用来：

- 列出下面有哪些子主题或子目录
- 记录该主题（目录）下值得知道的 notes 或 guiding ideas
- 其他可能对该主题有用的信息

## Article Layout

文章目录通常会把正文和附属资源放在一起管理。常见形式如下：

```text
articles-dir/
├── example-1.md
├── example-2.md
└── resources/
    ├── images/
    └── i18n/
        ├── example-1-en.md
        └── example-2-en.md
```

一般来说：

- `resources/images/` 存放与文章对应的图片等资源文件
- `resources/i18n/` 用于存放主文档的翻译 sidecar

有些目录如果只有图片资源，也可能直接使用 `images/`，不一定强制引入完整的 `resources/` 结构。

## Special Directories

这个仓库里有一些特殊目录

1. `__template__/` 用于存放文章模板。
2. `.githooks/` 用于存放仓库的 Git hooks；`pre-commit` 是统一入口，具体检查按顺序放在 `pre-commit.d/`。
3. `.kb-tools/` 用于存放这个仓库的辅助工具和脚本。如果要使用或修改其中的工具，请优先阅读对应子目录下的 `README.md`。
   - `.kb-tools/translator/` 用于生成或更新文章的英文翻译
   - `.kb-tools/website_sync/` 用于判断一次变更是否会影响网站发布结果

## Repository Setup

维护这个仓库需要的配置。

[Windows OS only] 启动大小写敏感

```shell
git config core.ignorecase false
```

配置 githook 路径

```shell
git config core.hooksPath .githooks
# 验证
git config --get core.hooksPath
```

同时需要保证命令行中可以正常运行：

```shell
python3 --version
codex exec
```

`.kb-tools/translator` 和翻译 pre-commit hook 会调用 codex exec。如果默认模型不可用，请先升级 Codex CLI，或显式指定模型。

## Publishing

Git push  会触发 github action 的检测规则，如果改动了那么满足发布标准的文章，那么就会触发网站同步。

具体规则见 `.kb-tools/website_sync/` 和 `.github/workflows/`。

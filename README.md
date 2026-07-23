# Knowledge Base

这是我的个人知识库，持续整理可复用的知识、经验和笔记，同时作为个人网站的内容来源。

- 个人网站：https://hanjie-chen.com/
- 网站源码：https://github.com/hanjie-chen/website

## Navigation

原则：路径即分类。

每一级目录都表示更具体的主题。内容的分类只由路径表达，文件名保留路径尚未提供的信息；移动文件即重新分类，不额外维护分类信息。例如：`tools/git/commands.md`。

目录树是人和 AI agent 共用的导航入口。当前的顶层主题目录：

- `ai/` — AI 相关：LLM、coding agents、自动化平台
- `cloud-infra/` — 云平台（Azure / GCP / Cloudflare）与 IaC（Terraform / Ansible）
- `code/` — 编程语言、算法与工程实践
- `devops/` — Docker、Kubernetes 等
- `machine-setup/` — 个人机器配置：Windows / Linux / 网络
- `personal-growth/` — 非技术类：生活经验、理财、世界观
- `tools/` — 日常工具指南：Vim、PowerShell、PowerToys 等
- `web-dev/` — Web 开发：前端、后端、安全

部分目录包含自己的 `README.md`，作为该目录的入口，用于介绍子主题以及需要了解的 notes 或 guiding ideas。首次浏览时，可以先选择一个顶层主题，再沿目录中的 `README.md` 继续阅读。

## Content Resources

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

## Setup

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

有时候我们往往能看到一个 python 项目中，包含 `pyproject.toml` 这个文件，这是是 Python 项目更加现代、标准化的方式。Python Packaging 官方也明确推荐现代项目使用它：[Writing your pyproject.toml - Python Packaging User Guide](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)

之前我的一个 website 项目使用，requirements.txt 来作为构建一个 python container 的依赖环境包

而 `pyproject.toml` 负责的范围更大。

可以先用一个简单比喻：

- `requirements.txt`：环境的“购物清单”
- `pyproject.toml`：这个 Python 项目的“说明书”

`requirements.txt` 通常只告诉 `pip`：

```
pypdf==6.0.0
trafilatura==2.0.0
```

意思是：“请给这个环境安装这些包。”

而项目的 `pyproject.toml` 还会说明：

```
[project]
name = "daily-brief"
version = "0.1.0"
requires-python = ">=3.12"
```

也就是：

- 这个项目叫什么
- 版本是多少
- 至少需要什么 Python 版本
- 运行时需要哪些第三方包
- 安装后提供什么命令

以及：

```
[build-system]
requires = ["setuptools>=68"]
build-backend = "setuptools.build_meta"
```

它告诉 Python：“如果要把当前项目安装或打包，请使用 `setuptools` 这个工具。”

因此，当执行：

```
pip install -e .
```

`pip` 会读取 `pyproject.toml`，用 `setuptools` 安装当前项目，同时安装 `pypdf` 和 `trafilatura` 等依赖。

目前只需要记住一句话：

> `requirements.txt` 主要描述“环境里装什么”；`pyproject.toml` 描述“这是一个什么项目，以及怎样安装和构建它”。
---
title: Python uv
date: 2025-12-12T10:05:00+08:00
lastmod: 2025-12-12T10:05:00+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /blog/img/python.webp
# covercaption: a description of the cover image
# images:
#   - /img/pythoned.png
categories:
  - python
tags:
  - python
# nolastmod: true
# math: true
draft: false
---

## 简介

uv 是由 Astral（Ruff 的开发团队）使用 Rust 语言打造的 新一代 Python 包与项目管理工具，目标是 统一并加速整个 Python 开发工作流。

截至 2025 年底，uv 已成为 Python 生态中增长最快、性能最强的工具之一，被广泛视为 Python 工具链现代化的核心组件。

<!--more-->

## 常用命令

```bash
uv init

# 创建虚拟环境
uv venv

# 激活
source .venv/bin/active

# 添加包
uv add fastapi

# 指定包版本
uv add pydantic==2.10.0
uv add pydantic>=2.10.0
uv add "pydantic<=2.10.0" # 注意添加引号


# 删除包
uv remove fastapi

# 打印依赖树
uv tree

# 类似 pip list
uv pip list

# 通过 uv 虚拟环境运行
uv run python main.py

# 安装依赖
# -e 表示“editable”（开发模式），即源码修改后无需重新安装。
# 如果不需要开发模式，去掉 -e 即可：uv pip install .
uv pip install -e .

# 安装依赖，新版方法，仅安装主依赖
uv sync

# 安装主依赖 + 所有可选依赖
uv sync --all-extras
```

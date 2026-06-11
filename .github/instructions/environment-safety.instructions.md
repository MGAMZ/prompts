---
description: "Use when: managing Python environments, installing packages, or modifying conda/pip/virtualenv configurations. Restricts AI from modifying the user's Python environment and defers all environment changes to the user."
applyTo: "**"
---

# 环境安全管理

AI **不得**执行任何以下操作，必须要求用户自行操作：

## 禁止的命令

- 直接运行 `pip install`、`pip uninstall`、`pip upgrade` 等 pip 命令
- 直接运行 `conda install`、`conda remove`、`conda update`、`conda create`、`conda env` 等 conda 命令
- 直接运行 `uv sync`、`uv add`、`uv remove`、`uv pip install` 等 uv 命令
- 直接运行 `python -m pip install`、`python setup.py install`、`poetry install`、`poetry add` 等任何 Python 包管理命令
- 直接修改 conda 环境配置文件（如 `environment.yml`）并自动应用更改
- 直接创建、删除或切换 conda 虚拟环境
- 直接修改系统级 Python 安装（如 `/usr/bin/python` 相关操作）

## 允许的操作

- **检查当前环境状态**：如 `pip list`、`conda list`、`python --version`、`which python`、`conda info --envs` 等只读命令
- **提供环境修改建议**：用文字向用户解释需要安装/更新哪些包、执行什么命令

## 例外情况

只有当用户明确要求 AI 执行环境修改（如明确说"帮我执行这个命令"）时，才能执行，但必须先用 `pip list --format=columns` 或 `conda list` 等只读命令验证当前环境状态后再执行。

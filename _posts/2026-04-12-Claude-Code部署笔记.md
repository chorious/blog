---
layout: post
title:  "Claude Code 部署笔记"
date:   2026-04-12 00:00:00
categories: Base
tags:  Code Tool Experience
author: chorious
---

* content
{:toc}

## 缘起

最近开始频繁使用 AI 辅助写代码，网页版的交互终究还是不够顺手，特别是在处理本地项目文件的时候，复制粘贴显得格外笨拙。听闻 Anthropic 推出了官方的 CLI 工具 Claude Code，可以直接在终端里调用，遂决定部署一套本地的环境，把流程跑通。

<!-- more -->
---

## 部署全过程

整个过程大致分为五步：安装、更新、激活、配置直连、以及最后的版本控制。以下按顺序记录，也给自己留一份备忘。

### 1. 通过 npm 安装

Claude Code 官方提供 npm 包，安装非常直接。前提是本地已经配置好了 Node.js 环境。

```bash
npm install -g @anthropic-ai/claude-code
```

安装完成后，在终端中输入 `claude` 即可启动。首次启动会检查环境并提示后续操作。

### 2. 更新指令

新版本的 Claude Code 不再支持通过 npm 更新。正确的更新方式是在终端中直接执行：

```bash
claude install
```

官方迭代速度较快，建议定期执行该指令，或在启动 `claude` 时留意入口处的升级提示。

### 3. 连接干净 IP 激活

首次使用需要完成设备激活（Device Activation）。由于网络原因，直接在默认网络环境下可能无法顺利完成验证。

我的做法非常简单：把网络切换到一个**干净的 IP 环境**，然后直接在终端启动 `claude`。只要 IP 符合条件，激活流程会自动静默完成，不需要手动输入验证码，也不需要在浏览器里做任何操作。本地的激活状态一旦写入，后续就可以正常使用。

### 4. 配置 ANTHROPIC_AUTH_TOKEN 进行直连

激活之后，其实已经可以对话了。但为了避免每次启动时的网络波动，以及获得更稳定的直连体验，我选择配置 API Token 进行身份校验。

在 Anthropic 官网的 Console 中生成一个 API Key，然后在本地环境变量中写入：

**Windows (PowerShell):**
```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "sk-ant-api03-xxxxxxxx", "User")
```

**Windows (CMD):**
```cmd
setx ANTHROPIC_AUTH_TOKEN sk-ant-api03-xxxxxxxx
```

**Linux / macOS:**
```bash
export ANTHROPIC_AUTH_TOKEN=sk-ant-api03-xxxxxxxx
```

配置完成后，重新打开终端输入 `claude`，即可跳过设备激活的网页验证流程，直接以 API 模式进入。实际使用下来的感受是响应更快，且不会再卡在验证环节。

### 5. 推到 Git 上

Claude Code 本身不依赖 Git，但它非常适合在 Git 仓库中工作。为了让自己在使用时有一个干净的测试环境，我把常用的项目目录都维护在了 Git 仓库下。Claude Code 启动时可以指定工作目录：

```bash
claude /path/to/your/repo
```

进入项目后，它会自动识别 `.git` 信息，并且支持用自然语言指令来查看 `git status`、生成 commit message、或者直接帮你 push 到远程。对于习惯用命令行的人来说，这省去了很多上下文切换的麻烦。

## 总结

整个部署过程不算复杂，核心瓶颈其实在于网络激活那一环。一旦迈过去，配合 API Token 的直连配置，Claude Code 在本地终端里的体验确实要比网页版流畅很多。特别是处理多文件项目时，它可以主动读取、编辑、执行命令，几乎是一个完整的工作流代理。

目前还在磨合提示词和使用习惯的阶段，但已经决定把它作为主力的本地辅助工具了。以上即是全部部署记录，供以后查阅。

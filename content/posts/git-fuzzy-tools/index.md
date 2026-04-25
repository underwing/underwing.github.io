---
title: "Git 命令行文件选择工具"
date: "2026-02-23T00:00:00+08:00"
author: "Amos"
tags: ["Git", "CLI", "效率工具"]
description: "fzf、fzy、git-fuzzy 等交互式文件选择工具的使用指南，让 Git 命令行操作更高效。"
readingTime: true
---

## 概述

交互式文件选择工具可以大幅提升 Git 命令行工作效率，通过模糊搜索和可视化界面快速选择文件进行操作。

## 工具对比

| 工具 | 语言 | 特点 | 适用场景 |
|------|------|------|----------|
| **fzf** | Go | 功能最全，生态最好 | 通用模糊搜索 |
| **fzy** | C | 极简，匹配算法优秀 | 追求性能和简洁 |
| **fpp** | Python | 自动识别路径 | Git 输出处理（有兼容性问题）|
| **git-fuzzy** | Shell | 专为 Git 设计 | Git 工作流 |
| **skim (sk)** | Rust | 最快，fzf 替代 | 追求极致性能 |

---

## 1. fzf（推荐）

> 功能最全面，生态最好的通用模糊搜索工具

### 安装

```bash
brew install fzf
# 安装 shell 集成
$(brew --prefix)/opt/fzf/install
```

### 基础用法

```bash
# 基本搜索
fzf

# 多选模式
fzf -m

# 带预览
fzf --preview 'bat --color=always {}'

# 限制高度
fzf --height 40% --reverse
```

### Git 场景

```bash
# 选择文件添加到暂存区
git status -s | fzf -m | awk '{print $2}' | xargs git add

# 查看文件 diff（带预览）
git diff --name-only | fzf --preview 'git diff --color=always {}'

# 切换分支
git branch | fzf | xargs git checkout

# 查看提交历史
git log --oneline | fzf --preview 'git show --color=always {1}'

# 恢复文件
git diff --name-only | fzf -m | xargs git checkout --
```

### 快捷键

```
Ctrl+J / Down    - 下移
Ctrl+K / Up      - 上移
Tab              - 多选模式下选中
Shift+Tab        - 多选模式下取消选中
Ctrl+A           - 全选
Enter            - 确认
Esc / Ctrl+C     - 取消
```

### Shell 集成（自动启用）

```bash
Ctrl+T    - 文件/目录搜索
Ctrl+R    - 命令历史搜索
Alt+C     - 目录跳转
```

### 实用别名

```bash
# 添加到 ~/.zshrc 或 ~/.bashrc

# 快速编辑文件
alias vf='vim $(fzf)'

# 快速 cd
alias cdf='cd $(find . -type d | fzf)'

# Git add 交互式
alias ga='git status -s | fzf -m | awk "{print \$2}" | xargs git add'

# Git diff 预览
alias gd='git diff --name-only | fzf --preview "git diff --color=always {}"'

# Git checkout 交互式
alias gco='git status -s | fzf -m | awk "{print \$2}" | xargs git checkout --'

# 进程查找并 kill
alias fkill='ps aux | fzf | awk "{print \$2}" | xargs kill'
```

### 环境变量配置

```bash
# 添加到 ~/.zshrc 或 ~/.bashrc

# 默认选项
export FZF_DEFAULT_OPTS='--height 40% --reverse --border'

# 默认命令（需要先安装 fd）
export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git'

# Ctrl+T 的命令
export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"

# Alt+C 的命令
export FZF_ALT_C_COMMAND='fd --type d --hidden --follow --exclude .git'
```

---

## 2. fzy（极简高效）

> 极简设计，匹配算法优秀，C 语言实现性能出色

### 安装

```bash
brew install fzy
```

### 基础用法

```bash
# 基本搜索
ls | fzy

# 指定显示行数
fzy -l 20

# 自定义提示符
fzy -p "Select: "

# 预填充查询
fzy -q "init"
```

### Git 场景

```bash
# 选择文件添加
git status -s | fzy | awk '{print $2}' | xargs git add

# 切换分支
git branch | sed 's/^[* ]*//' | fzy | xargs git checkout

# 查看提交
git log --oneline | fzy | awk '{print $1}' | xargs git show
```

### Shell 集成（Zsh）

```bash
# 添加到 ~/.zshrc

# Ctrl+T - 文件选择
fzy-file-widget() {
  local file=$(find . -type f 2>/dev/null | fzy)
  if [ -n "$file" ]; then
    LBUFFER="${LBUFFER}${file}"
  fi
  zle reset-prompt
}
zle -N fzy-file-widget
bindkey '^T' fzy-file-widget

# Ctrl+R - 历史搜索
fzy-history-widget() {
  local cmd=$(history -n 1 | fzy)
  if [ -n "$cmd" ]; then
    BUFFER="$cmd"
    CURSOR=$#BUFFER
  fi
  zle reset-prompt
}
zle -N fzy-history-widget
bindkey '^R' fzy-history-widget

# Alt+C - 目录跳转
fzy-cd-widget() {
  local dir=$(find . -type d 2>/dev/null | fzy)
  if [ -n "$dir" ]; then
    cd "$dir"
  fi
  zle reset-prompt
}
zle -N fzy-cd-widget
bindkey '\ec' fzy-cd-widget
```

### 实用别名

```bash
# 快速编辑
alias vf='vim $(find . -type f | fzy)'

# 快速 cd
alias cdf='cd $(find . -type d | fzy)'

# Git add
alias ga='git status -s | fzy | awk "{print \$2}" | xargs git add'
```

---

## 3. git-fuzzy（Git 专用）

> 专为 Git 工作流设计的交互式工具

### 安装

```bash
brew tap bigH/tap
brew install git-fuzzy

# 配置别名
git config --global alias.fuzzy '!git-fuzzy'
```

### 使用

```bash
git fuzzy status    # 交互式 git status
git fuzzy diff      # 交互式 git diff
git fuzzy log       # 交互式 git log
git fuzzy branch    # 交互式分支管理
```

---

## 4. 其他类似工具

### skim (sk) - Rust 版 fzf

```bash
brew install sk

# 用法与 fzf 相同
ls | sk
sk --preview 'cat {}'
```

### peco - 简洁的交互式过滤

```bash
brew install peco

ls | peco
git branch | peco | xargs git checkout
```

### gum - 现代 shell 脚本 UI

```bash
brew install gum

# 选择器
gum choose "Option 1" "Option 2" "Option 3"

# 过滤
ls | gum filter

# 输入框
gum input --placeholder "Enter name"
```

### zoxide - 智能目录跳转

```bash
brew install zoxide

# 添加到 shell
echo 'eval "$(zoxide init zsh)"' >> ~/.zshrc

# 使用
z documents  # 跳转到最常用的 documents 目录
zi           # 交互式选择（使用 fzf）
```

---

## 推荐配置

### 完整的 Git 工作流别名

将以下配置添加到 `~/.zshrc` 或 `~/.bashrc`：

```bash
# === fzf + Git 别名 ===

# Git add 交互式（带预览）
alias ga='git status -s | fzf -m --preview "git diff --color=always {2}" | awk "{print \$2}" | xargs git add'

# Git checkout 交互式
alias gco='git status -s | fzf -m | awk "{print \$2}" | xargs git checkout --'

# Git diff 交互式（带预览）
alias gd='git diff --name-only | fzf --preview "git diff --color=always {}"'

# Git 分支切换
alias gb='git branch | fzf | xargs git checkout'

# Git log 查看（带预览）
alias gl='git log --oneline --color=always | fzf --ansi --preview "git show --color=always {1}"'

# Git stash 查看
alias gs='git stash list | fzf --preview "git stash show -p {1}"'

# === 通用别名 ===

# 快速编辑文件
alias vf='vim $(fzf --preview "bat --color=always {}")'

# 快速 cd
alias cdf='cd $(fd -t d | fzf)'

# 进程管理
alias fkill='ps aux | fzf | awk "{print \$2}" | xargs kill'

# 历史命令
alias h='history | fzf | cut -c 8-'
```

### 推荐工具组合

```bash
# 基础组合
brew install fzf bat fd ripgrep

# 追求性能
brew install sk bat fd ripgrep

# Git 工作流
brew install fzf bat fd git-fuzzy

# 完整工具链
brew install fzf bat fd ripgrep zoxide git-fuzzy
```

---

## 快速参考

### fzf 常用选项

```bash
-m                          # 多选
--preview 'cmd {}'          # 预览
--preview-window=right:50%  # 预览窗口位置
--height 40%                # 限制高度
--reverse                   # 反转布局
--prompt="提示> "           # 自定义提示符
--header="标题"             # 添加标题
```

### Git 常用命令模板

```bash
# 选择文件并执行操作
git <command> | fzf -m | awk '{print $2}' | xargs <action>

# 带预览的选择
git <command> | fzf --preview 'git <preview-cmd> {}'

# 分支操作
git branch | fzf | xargs git <branch-action>

# 提交历史
git log --oneline | fzf --preview 'git show {1}'
```

---

## 故障排除

### fpp 兼容性问题

fpp 在 Python 3.14 上有兼容性问题，推荐使用替代方案：
- 使用 git-fuzzy
- 使用 fzf + git 别名
- 降级到 Python 3.12

### 性能优化

```bash
# 使用 fd 代替 find（更快）
brew install fd
export FZF_DEFAULT_COMMAND='fd --type f'

# 使用 bat 代替 cat（更好的预览）
brew install bat
alias preview='fzf --preview "bat --color=always {}"'
```

---

## 总结

### 使用建议

- **日常使用**: fzf（功能全面，生态好）
- **追求性能**: skim 或 fzy（更快）
- **Git 专用**: git-fuzzy（开箱即用）
- **脚本开发**: gum（UI 组件丰富）
- **目录导航**: zoxide（智能学习）

### 入门路径

推荐从 **fzf + 基础别名** 开始，根据需要逐步添加其他工具。

---

## 相关资源

- [fzf GitHub](https://github.com/junegunn/fzf)
- [fzy GitHub](https://github.com/jhawthorn/fzy)
- [git-fuzzy GitHub](https://github.com/bigH/git-fuzzy)

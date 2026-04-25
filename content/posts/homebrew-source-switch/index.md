+++
title = "macOS 更换 Homebrew 源"
date = "2026-02-14T00:00:00+08:00"
author = "Amos"
tags = ["macOS", "Homebrew", "开发环境"]
description = "macOS 更换 Homebrew 源为国内镜像的完整指南，包含中科大源和清华源两种方案。"
readingTime = true
+++

## 更换为中科大源

```bash
# 替换 brew.git
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

# 替换 homebrew-core.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

# 替换 homebrew-cask.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

# 替换 Homebrew Bottles 源
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

## 更换为清华源

```bash
# 替换 brew.git
cd "$(brew --repo)"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

# 替换 homebrew-core.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

# 替换 homebrew-cask.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git

# 替换 Homebrew Bottles 源
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

## 恢复官方源

```bash
cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git

cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core.git

cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin https://github.com/Homebrew/homebrew-cask.git

# 删除 HOMEBREW_BOTTLE_DOMAIN 环境变量
# 手动编辑 ~/.zshrc 删除相关行
```

## 验证更换

```bash
brew update
```

## 注意事项

- 如果使用 bash 而不是 zsh，将 `~/.zshrc` 替换为 `~/.bash_profile`
- 更换源后首次运行 `brew update` 可能需要一些时间

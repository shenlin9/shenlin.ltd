---
title: Linux - Zsh & Oh-My-Zsh
categories:
  - Linux
tags:
  - Linux
  - Zsh
---

Linux - Zsh & Oh-My-Zsh

<!--more-->

## Zsh 概述


## oh-my-zsh

自动安装，通常第一次会卡住
```zsh
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# 或者

$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

手动安装
```zsh
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```


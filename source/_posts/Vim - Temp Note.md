---
title: Vim - Temp Note
categories:
  - Vim
tags:
  - Vim
---

Temp Note

<!--more-->

```vim
" enter the current millenium
set nocompatible

" enter syntax and plugins (for netrw)
syntax enable
filetype plugin on
```

## FINDING FILES

Search down into subfolders
Provies tab-completion for all file-related tasks
```vim
set path+=**
```

Display all matching files when we tab complete
```vim
set wildmenu
```

NOW WE CAN：
* Hit tab to `:find` by partial match
* Use `*` to make it fuzzy

THINGS TO CONSIDER:
* `:b <tab>` lets you autocomplete any openbuffer


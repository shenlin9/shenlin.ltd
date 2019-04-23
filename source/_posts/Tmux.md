---
title: Tmux - 高效终端
categories:
  - Tmux
tags:
  - Tmux
---

Tmux - 高效终端

<!--more-->

## 概述

start new:

    tmux

start new with session name:

    tmux new -s myname

attach:

    tmux a  #  (or at, or attach)

attach to named:

    tmux a -t myname

list sessions:

    tmux ls

kill session:

    tmux kill-session -t myname

Kill all the tmux sessions:

    tmux ls | grep : | cut -d. -f1 | awk '{print substr($1, 0, length($1)-1)}' | xargs kill

## Shortcuts

先按 prefix 键，默认为 `ctrl+b`

### Shortcuts Groups By Type

**Sessions**

    :new<CR>  new session
    s  list sessions
    $  name session

**Windows (tabs)**

    c  create window
    &  kill window

    w  list windows
    n  next window
    p  previous window
    f  find window

    ,  name window

**Panes (splits)**

    %  vertical split
    "  horizontal split

    o  swap panes
    q  show pane numbers
    x  kill pane
    +  break pane into window (e.g. to select text by mouse to copy)
    -  restore pane from window
    ⍽  space - toggle between layouts
    <prefix> q (Show pane numbers, when the numbers show up type the key to goto that pane)
    <prefix> { (Move the current pane left)
    <prefix> } (Move the current pane right)
    <prefix> z toggle pane zoom

### Shortcuts Groups By Functionality

**Create**

    :new<CR>  new session
    c  create window
    %  vertical split
    "  horizontal split

**List**

    s  list sessions
    w  list windows

    f  find window
    q  show pane numbers(when the numbers show up type the key to goto that pane)

**Rename**

    $  name session
    ,  name window

**Kill**

    &  kill window
    x  kill pane

**Switch**

    n  next window
    p  previous window
    o  swap panes

**Layouts**

    ⍽  space - toggle between layouts
    <prefix> { (Move the current pane left)
    <prefix> } (Move the current pane right)
    <prefix> z toggle pane zoom

    +  break pane into window (e.g. to select text by mouse to copy)
    -  restore pane from window

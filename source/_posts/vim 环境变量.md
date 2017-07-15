# vim 环境变量

Tags ： AAA vim

---

//查看环境变量
```
:echo $HOME
C:\Users\ssl

:echo $VIM
C:\Program Files\Vim

:echo $VIMRUNTIME
C:\Program Files\Vim\vim80
```

//查看路径
```
:set runtimepath
:set rtp
```

//设置路径
```
:set rtp+=~/.vim/bundle/Vundle.vim
:set rtp+=$ProgramFiles\Git\bin
:set rtp+=$ProgramFiles\Git\mingw32\bin
:set rtp+=$ProgramFiles\Git\mingw32\libexec\git-core
```

重新加载配置文件
```
//windows
:source $VIM\_vimrc
:so $VIM\_vimrc

//linux
:source ~\.vimrc
:so ~\.vimrc
```

? MyDiff 已存在，要使用 ! 覆盖
? source $VIMRUNTIME/vimrc_example.vim 作用
? Pathogen 和 Vundle

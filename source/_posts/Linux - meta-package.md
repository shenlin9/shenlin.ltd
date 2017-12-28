---
title: linux - meta-package
categories:
  - Linux
  - meta-package
tags:
  - Linux
  - meta-package
---

它是很多linux包管理器（比如apt和pacman）使用的一种虚包（virtual package ,dummy package），它本身可以被看作一种“包的集合”概念，这种包中并不含有任何的程序或者链接库信息，它仅仅是个外壳，依赖于集合中的所有包。

<!--more-->

删除meta-package的一部分可能会造成包管理系统的升级或维护错误，威胁到系统稳定性。 所以，会出现卸载meta-package中某个包时，出现卸载掉整个meta-package的情况，即要不就全装，要不就全卸载……






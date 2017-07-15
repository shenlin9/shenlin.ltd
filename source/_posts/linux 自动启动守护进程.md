# linux 自动启动守护进程

tags ： AAA linux 

---

```
initctl start local-git-daemon
initctl: Unknown job: local-git-daemon
```

sysvinit 系统中的 xinetd 脚本

inetd (internet service daemon) :

inetd is a super-server daemon on many Unix systems that provides Internet services. For each configured service, it listens for requests from connecting clients. Requests are served by spawning a process which runs the appropriate executable, but simple services such as echo are served by inetd itself. External executables, which are run on request, can be single- or multi-threaded. First appearing in 4.3BSD , it is generally located at /usr/sbin/inetd.

Xinetd (extended Internet daemon) :

In computer networking, xinetd (extended Internet daemon) is an open-source super-server daemon which runs on many Unix-like systems and manages Internet-based connectivity. It offers a more secure extension to or version of inetd, the Internet daemon, thus most modern Linux distributions have switched to it.


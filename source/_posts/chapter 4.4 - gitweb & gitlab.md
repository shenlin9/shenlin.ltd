# chapter 4.4 : gitweb & gitlab

tags ： AAA Book-ProGit git

---

## gitweb

http://www.cnblogs.com/dudu/archive/2012/12/10/linux-apache-gitweb.html

安装失败貌似和apache的安装路径有关
我的是自定义路径httpd-2.4.25，而不是默认的apache2
/usr/local/httpd-2.4.25/modules/

git instaweb 位置：
/usr/libexec/git-core/git-instaweb
第 60 行
好像是因为路径都写死在里面了，固定的去/usr/local/sbin 和/usr/sbin 

https://stackoverflow.com/questions/1508752/git-instaweb-httpd-configuration-to-use-apache2-on-osx-leopard-server

```
>> git instaweb
fatal: Not a git repository (or any of the parent directories): .git

>> cd /opt/git/project.git/

>> git instaweb
lighttpd not found. Install lighttpd or use --httpd to specify another httpd daemon.

//只有httpd为apache时需要指定路径参数
>> git instaweb --httpd=apache2 --module-path=/usr/local/httpd-2.4.25/modules/
```
https://git-scm.com/docs/git-instaweb

## gitlab


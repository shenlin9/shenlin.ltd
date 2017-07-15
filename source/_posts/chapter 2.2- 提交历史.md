# chapter 2.2: 提交历史

tags ： AAA Book-ProGit git

---

## 提交历史

### 1. 常用命令
```
//默认按提交时间倒叙排列所有的提交
$ git log

//显示最近两次的提交并显示每次提交的内容差异
//代码审查时用 或 查看同事提交的内容
$ git log -p -2

//附带简略统计信息
//显示多少文件被修改及哪些儿行被删除或添加
$ git log --stat

//每个提交显示为一行
//提交比较多时用
$ git log --oneline
$ git log --pretty=oneline

//定制显示格式
//对于后期提取分析比较有用，输出格式不会随 git 更新而变化
$ git log --pretty=format:"%h - %an, %ar : %s"

//使用了ASCII字符形象的显示分支、合并历史
//常和oneline,format搭配使用
$ git log --pretty=oneline --graph
$ git log --pretty=format:"%h %s" --graph

//使用限制输出选项
$ git log --since=2.weeks
$ git log --since="2008-10-01"
$ git log --since="2 years 1 day 3 minutes ago"

//添加或移除了特定字符串"function_name"的提交
$ git log -Sfunction_name

//路径选项，放在最后位置，使用两个短线隔开前面的选项和后面的路径
//表示只关心某些文件或者目录的历史提交
$ git log --oneline -- index.html
$ git log --oneline -- vendor/test.txt

//综合举例：2008 年 10 月期间，Junio Hamano 提交的但未合并的测试文件
$ git log --pretty="%h - %s" --author=gitster --since="2008-10-01" \
   --before="2008-11-01" --no-merges -- t/
```

### 2. `git log`常用选项

|选项|说明|
|-|-|
|-p|按补丁格式显示每个更新之间的差异。|
|--stat|显示每次更新的文件修改统计信息。|
|--shortstat|只显示 --stat 中最后的行数修改添加移除统计。|
|--name-only|仅在提交信息后显示已修改的文件清单。|
|--name-status|显示新增、修改、删除的文件清单。|
|--abbrev-commit|仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。|
|--relative-date|使用较短的相对时间显示（比如，“2 weeks ago”）。|
|--graph|显示 ASCII 图形表示的分支合并历史。|
|--pretty|使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。|

### 3. `git log`限制输出常用选项

|选项|说明|
|-|-|
|-(n)|仅显示最近的 n 条提交|
|--since, --after|仅显示指定时间之后的提交。|
|--until, --before|仅显示指定时间之前的提交。|
|--author|仅显示指定作者相关的提交。|
|--committer|仅显示指定提交者相关的提交。|
|--grep|仅显示含指定关键字的提交|
|-S|仅显示添加或移除了某个关键字的提交|


### 4. `--pretty=format:"FFF"`常用选项

|选项|说明|
|-|-|
|%H|提交对象（commit）的完整哈希字串|
|%h|提交对象的简短哈希字串|
|%T|树对象（tree）的完整哈希字串|
|%t|树对象的简短哈希字串|
|%P|父对象（parent）的完整哈希字串|
|%p|父对象的简短哈希字串|
|%an|作者（author）的名字|
|%ae|作者的电子邮件地址|
|%ad|作者修订日期（可以用 --date= 选项定制格式）|
|%ar|作者修订日期，按多久以前的方式显示|
|%cn|提交者（committer）的名字|
|%ce|提交者的电子邮件地址|
|%cd|提交日期|
|%cr|提交日期，按多久以前的方式显示|
|%s|提交说明|




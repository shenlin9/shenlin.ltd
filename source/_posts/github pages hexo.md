# github pages hexo

tags ：AAA git github hexo 

---

文章指的就是组成博客的一篇篇post，一般用md格式，除非你的md原稿找不到了，html格式也认。

把写好的md文件放在source/_posts目录下

文章范例hello-world.md内容：

    title : hello world
    date : 2014-03-13
    tags :
    ---
    welcome to hexo
    this is my first post

三条横线上面的部分是文章的元数据
date还是要强调一下。因为经常要按时间排序、归档，所以hexo给每篇文章都打了一个时间戳。如果不显式指定时间，hexo默认会用md文件的创建时间。

## 安装 hexo

```
//安装hexo
C:\Users\ssl>npm install -g hexo-cli

C:\Users\ssl\AppData\Roaming\npm\hexo -> C:\Users\ssl\AppData\Roaming\npm\node_m
odules\hexo-cli\bin\hexo

> dtrace-provider@0.8.3 install C:\Users\ssl\AppData\Roaming\npm\node_modules\he
xo-cli\node_modules\dtrace-provider
> node scripts/install.js


> hexo-util@0.6.0 postinstall C:\Users\ssl\AppData\Roaming\npm\node_modules\hexo
-cli\node_modules\hexo-util
> npm run build:highlight


> hexo-util@0.6.0 build:highlight C:\Users\ssl\AppData\Roaming\npm\node_modules\
hexo-cli\node_modules\hexo-util
> node scripts/build_highlight_alias.js > highlight_alias.json

C:\Users\ssl\AppData\Roaming\npm
`-- hexo-cli@1.0.3
  +-- abbrev@1.1.0
  +-- bluebird@3.5.0
  +-- chalk@1.1.3
  | +-- ansi-styles@2.2.1
  | +-- escape-string-regexp@1.0.5
  | +-- has-ansi@2.0.0
  | | `-- ansi-regex@2.1.1
  | +-- strip-ansi@3.0.1
  | `-- supports-color@2.0.0
  +-- command-exists@1.2.2
  +-- hexo-fs@0.2.1
  | +-- chokidar@1.7.0
  | | +-- anymatch@1.3.0
  | | | +-- arrify@1.0.1
  | | | `-- micromatch@2.3.11
  | | |   +-- arr-diff@2.0.0
  | | |   | `-- arr-flatten@1.1.0
  | | |   +-- array-unique@0.2.1
  | | |   +-- braces@1.8.5
  | | |   | +-- expand-range@1.8.2
  | | |   | | `-- fill-range@2.2.3
  | | |   | |   +-- is-number@2.1.0
  | | |   | |   +-- isobject@2.1.0
  | | |   | |   +-- randomatic@1.1.7
  | | |   | |   | +-- is-number@3.0.0
  | | |   | |   | | `-- kind-of@3.2.2
  | | |   | |   | `-- kind-of@4.0.0
  | | |   | |   `-- repeat-string@1.6.1
  | | |   | +-- preserve@0.2.0
  | | |   | `-- repeat-element@1.1.2
  | | |   +-- expand-brackets@0.1.5
  | | |   | `-- is-posix-bracket@0.1.1
  | | |   +-- extglob@0.3.2
  | | |   +-- filename-regex@2.0.1
  | | |   +-- kind-of@3.2.2
  | | |   | `-- is-buffer@1.1.5
  | | |   +-- normalize-path@2.1.1
  | | |   | `-- remove-trailing-separator@1.0.2
  | | |   +-- object.omit@2.0.1
  | | |   | +-- for-own@0.1.5
  | | |   | | `-- for-in@1.0.2
  | | |   | `-- is-extendable@0.1.1
  | | |   +-- parse-glob@3.0.4
  | | |   | +-- glob-base@0.3.0
  | | |   | `-- is-dotfile@1.0.3
  | | |   `-- regex-cache@0.4.3
  | | |     +-- is-equal-shallow@0.1.3
  | | |     `-- is-primitive@2.0.0
  | | +-- async-each@1.0.1
  | | +-- glob-parent@2.0.0
  | | +-- inherits@2.0.3
  | | +-- is-binary-path@1.0.1
  | | | `-- binary-extensions@1.8.0
  | | +-- is-glob@2.0.1
  | | | `-- is-extglob@1.0.0
  | | +-- path-is-absolute@1.0.1
  | | `-- readdirp@2.1.0
  | |   +-- minimatch@3.0.4
  | |   | `-- brace-expansion@1.1.8
  | |   |   +-- balanced-match@1.0.0
  | |   |   `-- concat-map@0.0.1
  | |   +-- readable-stream@2.3.3
  | |   | +-- core-util-is@1.0.2
  | |   | +-- isarray@1.0.0
  | |   | +-- process-nextick-args@1.0.7
  | |   | +-- safe-buffer@5.1.1
  | |   | +-- string_decoder@1.0.3
  | |   | `-- util-deprecate@1.0.2
  | |   `-- set-immediate-shim@1.0.1
  | `-- graceful-fs@4.1.11
  +-- hexo-log@0.1.3
  | `-- bunyan@1.8.10
  |   +-- dtrace-provider@0.8.3
  |   | `-- nan@2.6.2
  |   +-- moment@2.18.1
  |   +-- mv@2.1.1
  |   | +-- mkdirp@0.5.1
  |   | | `-- minimist@0.0.8
  |   | +-- ncp@2.0.0
  |   | `-- rimraf@2.4.5
  |   |   `-- glob@6.0.4
  |   |     +-- inflight@1.0.6
  |   |     | `-- wrappy@1.0.2
  |   |     `-- once@1.4.0
  |   `-- safe-json-stringify@1.0.4
  +-- hexo-util@0.6.0
  | +-- camel-case@3.0.0
  | | +-- no-case@2.3.1
  | | | `-- lower-case@1.1.4
  | | `-- upper-case@1.1.3
  | +-- cross-spawn@4.0.2
  | | +-- lru-cache@4.1.1
  | | | +-- pseudomap@1.0.2
  | | | `-- yallist@2.1.2
  | | `-- which@1.2.14
  | |   `-- isexe@2.0.0
  | +-- highlight.js@9.12.0
  | +-- html-entities@1.2.1
  | `-- striptags@2.2.1
  +-- minimist@1.2.0
  +-- object-assign@4.1.1
  `-- tildify@1.2.0
    `-- os-homedir@1.0.2

npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\he
xo-cli\node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@
1.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"ia32"
})
```

## 初始化目录

```
//初始化目录
$ cd /d/git/bitbiteme.github.io/

//非空目录出错
$ hexo init
FATAL D:\git\bitbiteme.github.io\ not empty, please run `hexo init` on an empty folder and then copy your files into it
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: target not empty

//如果hexo init命令执行一半 Ctrl+C 强制退出，则出错
$ hexo init
ERROR Local hexo not found in D:\git\bitbiteme.github.io
ERROR Try running: 'npm install hexo --save'

$ hexo -v
ERROR Local hexo not found in D:\git\bitbiteme.github.io
ERROR Try running: 'npm install hexo --save'

//清空目录
$ rm -Rf ./*

$ hexo init
INFO  Cloning hexo-starter to D:\git\bitbiteme.github.io
Cloning into 'D:\git\bitbiteme.github.io'...
remote: Counting objects: 56, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 56 (delta 0), reused 1 (delta 0), pack-reused 53
Unpacking objects: 100% (56/56), done.
Submodule 'themes/landscape' (https://github.com/hexojs/hexo-theme-landscape.git) registered for path 'themes/landscape'
Cloning into 'D:/git/bitbiteme.github.io/themes/landscape'...
remote: Counting objects: 785, done.
remote: Total 785 (delta 0), reused 0 (delta 0), pack-reused 785
Receiving objects: 100% (785/785), 2.54 MiB | 105.00 KiB/s, done.
Resolving deltas: 100% (404/404), done.
Submodule path 'themes/landscape': checked out 'decdc2d9956776cbe95420ae94bac87e22468d38'
INFO  Install dependencies
信息: 用提供的模式无法找到文件。
npm WARN deprecated swig@1.4.2: This package is no longer maintained
npm WARN prefer global marked@0.3.6 should be installed with -g

> dtrace-provider@0.8.3 install D:\git\bitbiteme.github.io\node_modules\dtrace-provider
> node scripts/install.js


> hexo-util@0.6.0 postinstall D:\git\bitbiteme.github.io\node_modules\hexo-util
> npm run build:highlight


> hexo-util@0.6.0 build:highlight D:\git\bitbiteme.github.io\node_modules\hexo-util
> node scripts/build_highlight_alias.js > highlight_alias.json

hexo-site@0.0.0 D:\git\bitbiteme.github.io
+-- hexo@3.3.7
| +-- abbrev@1.1.0
| +-- archy@1.0.0
| +-- bluebird@3.5.0
| +-- chalk@1.1.3
| | +-- ansi-styles@2.2.1
| | +-- escape-string-regexp@1.0.5
| | +-- has-ansi@2.0.0
| | | `-- ansi-regex@2.1.1
| | +-- strip-ansi@3.0.1
| | `-- supports-color@2.0.0
| +-- cheerio@0.20.0
| | +-- css-select@1.2.0
| | | +-- boolbase@1.0.0
| | | +-- css-what@2.1.0
| | | +-- domutils@1.5.1
| | | `-- nth-check@1.0.1
| | +-- dom-serializer@0.1.0
| | | `-- domelementtype@1.1.3
| | +-- entities@1.1.1
| | +-- htmlparser2@3.8.3
| | | +-- domelementtype@1.3.0
| | | +-- domhandler@2.3.0
| | | +-- entities@1.0.0
| | | `-- readable-stream@1.1.14
| | |   +-- core-util-is@1.0.2
| | |   +-- isarray@0.0.1
| | |   `-- string_decoder@0.10.31
| | `-- jsdom@7.2.2
| |   +-- abab@1.0.3
| |   +-- acorn@2.7.0
| |   +-- acorn-globals@1.0.9
| |   +-- cssom@0.3.2
| |   +-- cssstyle@0.2.37
| |   +-- escodegen@1.8.1
| |   | +-- esprima@2.7.3
| |   | +-- estraverse@1.9.3
| |   | +-- esutils@2.0.2
| |   | +-- optionator@0.8.2
| |   | | +-- deep-is@0.1.3
| |   | | +-- fast-levenshtein@2.0.6
| |   | | +-- levn@0.3.0
| |   | | +-- prelude-ls@1.1.2
| |   | | +-- type-check@0.3.2
| |   | | `-- wordwrap@1.0.0
| |   | `-- source-map@0.2.0
| |   +-- nwmatcher@1.4.1
| |   +-- parse5@1.5.1
| |   +-- request@2.81.0
| |   | +-- aws-sign2@0.6.0
| |   | +-- aws4@1.6.0
| |   | +-- caseless@0.12.0
| |   | +-- combined-stream@1.0.5
| |   | | `-- delayed-stream@1.0.0
| |   | +-- extend@3.0.1
| |   | +-- forever-agent@0.6.1
| |   | +-- form-data@2.1.4
| |   | | `-- asynckit@0.4.0
| |   | +-- har-validator@4.2.1
| |   | | +-- ajv@4.11.8
| |   | | | +-- co@4.6.0
| |   | | | `-- json-stable-stringify@1.0.1
| |   | | |   `-- jsonify@0.0.0
| |   | | `-- har-schema@1.0.5
| |   | +-- hawk@3.1.3
| |   | | +-- boom@2.10.1
| |   | | +-- cryptiles@2.0.5
| |   | | +-- hoek@2.16.3
| |   | | `-- sntp@1.0.9
| |   | +-- http-signature@1.1.1
| |   | | +-- assert-plus@0.2.0
| |   | | +-- jsprim@1.4.0
| |   | | | +-- assert-plus@1.0.0
| |   | | | +-- extsprintf@1.0.2
| |   | | | +-- json-schema@0.2.3
| |   | | | `-- verror@1.3.6
| |   | | `-- sshpk@1.13.1
| |   | |   +-- asn1@0.2.3
| |   | |   +-- assert-plus@1.0.0
| |   | |   +-- bcrypt-pbkdf@1.0.1
| |   | |   +-- dashdash@1.14.1
| |   | |   | `-- assert-plus@1.0.0
| |   | |   +-- ecc-jsbn@0.1.1
| |   | |   +-- getpass@0.1.7
| |   | |   | `-- assert-plus@1.0.0
| |   | |   +-- jsbn@0.1.1
| |   | |   `-- tweetnacl@0.14.5
| |   | +-- is-typedarray@1.0.0
| |   | +-- isstream@0.1.2
| |   | +-- json-stringify-safe@5.0.1
| |   | +-- oauth-sign@0.8.2
| |   | +-- performance-now@0.2.0
| |   | +-- qs@6.4.0
| |   | +-- stringstream@0.0.5
| |   | +-- tunnel-agent@0.6.0
| |   | `-- uuid@3.1.0
| |   +-- sax@1.2.4
| |   +-- symbol-tree@3.2.2
| |   +-- tough-cookie@2.3.2
| |   | `-- punycode@1.4.1
| |   +-- webidl-conversions@2.0.1
| |   +-- whatwg-url-compat@0.6.5
| |   | `-- tr46@0.0.3
| |   `-- xml-name-validator@2.0.1
| +-- deep-assign@2.0.0
| | `-- is-obj@1.0.1
| +-- hexo-cli@1.0.3
| | +-- command-exists@1.2.2
| | +-- hexo-fs@0.2.1
| | `-- minimist@1.2.0
| +-- hexo-front-matter@0.2.3
| +-- hexo-fs@0.1.6
| | +-- chokidar@1.7.0
| | | +-- anymatch@1.3.0
| | | | +-- arrify@1.0.1
| | | | `-- micromatch@2.3.11
| | | |   +-- arr-diff@2.0.0
| | | |   | `-- arr-flatten@1.1.0
| | | |   +-- array-unique@0.2.1
| | | |   +-- braces@1.8.5
| | | |   | +-- expand-range@1.8.2
| | | |   | | `-- fill-range@2.2.3
| | | |   | |   +-- is-number@2.1.0
| | | |   | |   +-- isobject@2.1.0
| | | |   | |   | `-- isarray@1.0.0
| | | |   | |   +-- randomatic@1.1.7
| | | |   | |   | +-- is-number@3.0.0
| | | |   | |   | | `-- kind-of@3.2.2
| | | |   | |   | `-- kind-of@4.0.0
| | | |   | |   `-- repeat-string@1.6.1
| | | |   | +-- preserve@0.2.0
| | | |   | `-- repeat-element@1.1.2
| | | |   +-- expand-brackets@0.1.5
| | | |   | `-- is-posix-bracket@0.1.1
| | | |   +-- extglob@0.3.2
| | | |   +-- filename-regex@2.0.1
| | | |   +-- kind-of@3.2.2
| | | |   | `-- is-buffer@1.1.5
| | | |   +-- normalize-path@2.1.1
| | | |   | `-- remove-trailing-separator@1.0.2
| | | |   +-- object.omit@2.0.1
| | | |   | +-- for-own@0.1.5
| | | |   | | `-- for-in@1.0.2
| | | |   | `-- is-extendable@0.1.1
| | | |   +-- parse-glob@3.0.4
| | | |   | +-- glob-base@0.3.0
| | | |   | `-- is-dotfile@1.0.3
| | | |   `-- regex-cache@0.4.3
| | | |     +-- is-equal-shallow@0.1.3
| | | |     `-- is-primitive@2.0.0
| | | +-- async-each@1.0.1
| | | +-- glob-parent@2.0.0
| | | +-- inherits@2.0.3
| | | +-- is-binary-path@1.0.1
| | | | `-- binary-extensions@1.8.0
| | | +-- is-glob@2.0.1
| | | | `-- is-extglob@1.0.0
| | | +-- path-is-absolute@1.0.1
| | | `-- readdirp@2.1.0
| | |   +-- readable-stream@2.3.3
| | |   | +-- isarray@1.0.0
| | |   | +-- process-nextick-args@1.0.7
| | |   | +-- string_decoder@1.0.3
| | |   | `-- util-deprecate@1.0.2
| | |   `-- set-immediate-shim@1.0.1
| | `-- graceful-fs@4.1.11
| +-- hexo-i18n@0.2.1
| | `-- sprintf-js@1.0.3
| +-- hexo-log@0.1.3
| | `-- bunyan@1.8.10
| |   +-- dtrace-provider@0.8.3
| |   | `-- nan@2.6.2
| |   +-- mv@2.1.1
| |   | +-- ncp@2.0.0
| |   | `-- rimraf@2.4.5
| |   |   `-- glob@6.0.4
| |   `-- safe-json-stringify@1.0.4
| +-- hexo-util@0.6.0
| | +-- camel-case@3.0.0
| | | +-- no-case@2.3.1
| | | | `-- lower-case@1.1.4
| | | `-- upper-case@1.1.3
| | +-- cross-spawn@4.0.2
| | | +-- lru-cache@4.1.1
| | | | +-- pseudomap@1.0.2
| | | | `-- yallist@2.1.2
| | | `-- which@1.2.14
| | |   `-- isexe@2.0.0
| | +-- highlight.js@9.12.0
| | +-- html-entities@1.2.1
| | `-- striptags@2.2.1
| +-- js-yaml@3.9.0
| | +-- argparse@1.0.9
| | `-- esprima@4.0.0
| +-- lodash@4.17.4
| +-- minimatch@3.0.4
| | `-- brace-expansion@1.1.8
| |   +-- balanced-match@1.0.0
| |   `-- concat-map@0.0.1
| +-- moment@2.13.0
| +-- moment-timezone@0.5.13
| +-- nunjucks@2.5.2
| | +-- asap@2.0.6
| | `-- yargs@3.32.0
| |   +-- camelcase@2.1.1
| |   +-- cliui@3.2.0
| |   | `-- wrap-ansi@2.1.0
| |   +-- decamelize@1.2.0
| |   +-- os-locale@1.4.0
| |   | `-- lcid@1.0.0
| |   |   `-- invert-kv@1.0.0
| |   +-- string-width@1.0.2
| |   | +-- code-point-at@1.1.0
| |   | `-- is-fullwidth-code-point@1.0.0
| |   |   `-- number-is-nan@1.0.1
| |   +-- window-size@0.1.4
| |   `-- y18n@3.2.1
| +-- pretty-hrtime@1.0.3
| +-- strip-indent@1.0.1
| | `-- get-stdin@4.0.1
| +-- swig@1.4.2
| | +-- optimist@0.6.1
| | | +-- minimist@0.0.10
| | | `-- wordwrap@0.0.3
| | `-- uglify-js@2.4.24
| |   +-- async@0.2.10
| |   +-- source-map@0.1.34
| |   +-- uglify-to-browserify@1.0.2
| |   `-- yargs@3.5.4
| |     +-- camelcase@1.2.1
| |     +-- window-size@0.1.0
| |     `-- wordwrap@0.0.2
| +-- swig-extras@0.0.1
| | `-- markdown@0.5.0
| |   `-- nopt@2.1.2
| +-- text-table@0.2.0
| +-- tildify@1.2.0
| | `-- os-homedir@1.0.2
| +-- titlecase@1.1.2
| `-- warehouse@2.2.0
|   +-- cuid@1.3.8
|   | +-- browser-fingerprint@0.0.1
|   | +-- core-js@1.2.7
|   | `-- node-fingerprint@0.0.2
|   +-- is-plain-object@2.0.4
|   | `-- isobject@3.0.1
|   `-- JSONStream@1.3.1
|     +-- jsonparse@1.3.1
|     `-- through@2.3.8
+-- hexo-generator-archive@0.1.4
| +-- hexo-pagination@0.0.2
| | `-- utils-merge@1.0.0
| `-- object-assign@2.1.1
+-- hexo-generator-category@0.1.3
| `-- object-assign@2.1.1
+-- hexo-generator-index@0.2.1
| `-- object-assign@4.1.1
+-- hexo-generator-tag@0.2.0
+-- hexo-renderer-ejs@0.2.0
| `-- ejs@1.0.0
+-- hexo-renderer-marked@0.2.11
| `-- marked@0.3.6
+-- hexo-renderer-stylus@0.3.3
| +-- nib@1.1.2
| `-- stylus@0.54.5
|   +-- css-parse@1.7.0
|   +-- debug@2.6.8
|   | `-- ms@2.0.0
|   +-- glob@7.0.6
|   | +-- fs.realpath@1.0.0
|   | +-- inflight@1.0.6
|   | | `-- wrappy@1.0.2
|   | `-- once@1.4.0
|   +-- mkdirp@0.5.1
|   | `-- minimist@0.0.8
|   +-- sax@0.5.8
|   `-- source-map@0.1.43
|     `-- amdefine@1.0.1
`-- hexo-server@0.2.1
  +-- compression@1.7.0
  | +-- accepts@1.3.3
  | | +-- mime-types@2.1.15
  | | `-- negotiator@0.6.1
  | +-- bytes@2.5.0
  | +-- compressible@2.0.10
  | | `-- mime-db@1.27.0
  | +-- on-headers@1.0.1
  | +-- safe-buffer@5.1.1
  | `-- vary@1.1.1
  +-- connect@3.6.2
  | +-- debug@2.6.7
  | +-- finalhandler@1.0.3
  | | +-- debug@2.6.7
  | | +-- statuses@1.3.1
  | | `-- unpipe@1.0.0
  | `-- parseurl@1.3.1
  +-- mime@1.3.6
  +-- morgan@1.8.2
  | +-- basic-auth@1.1.0
  | +-- depd@1.1.0
  | `-- on-finished@2.3.0
  |   `-- ee-first@1.1.1
  +-- opn@4.0.2
  | `-- pinkie-promise@2.0.1
  |   `-- pinkie@2.0.4
  `-- serve-static@1.12.3
    +-- encodeurl@1.0.1
    +-- escape-html@1.0.3
    `-- send@0.15.3
      +-- debug@2.6.7
      +-- destroy@1.0.4
      +-- etag@1.8.0
      +-- fresh@0.5.0
      +-- http-errors@1.6.1
      | `-- setprototypeof@1.0.3
      +-- mime@1.3.4
      `-- range-parser@1.2.0

npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"ia32"})
INFO  Start blogging with Hexo!
```
node_modules：是依赖包
public：存放的是生成的页面
scaffolds：命令生成文章等的模板
source：用命令创建的各种文章
themes：主题
_config.yml：整个博客的配置
db.json：source解析所得到的
package.json：项目所需模块项目的配置信息

## ???

```
$ npm install
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"ia32"})
```

## 启动 hexo 服务器预览
```
$ hexo server
```
http://localhost:4000/

## 生成静态文件

刚刚看到的页面并不是静态的。若要得到静态文件，还需执行一条命令：
```
$ hexo generate
```
命令会创建一个目录public，并在public下生成所有的静态文件

## 配置文件

修改_config.yml要格外注意，每项冒号后面都加一个空格，另外不要出现tab。
_config.yml文件中所有冒号后面的空格，格式很严格，必须是只有一个半角空格。不管是多了还是少了都会报错，这是yml解释器所定义的语法，不要使用Tab。

```
deploy: 
    type: git
    repo: https://github.com/bitbiteme/bitbiteme.github.io.git
          git@github.com:bitbiteme/bitbiteme.github.io.git
    branch: master
```
* title: 博客主页标题
* url: 你的博客网址，例如http://yoursite.com
* permalink: 每篇文章的URL链接格式，例如:year/:month/:title/，注意不要漏掉最后的/
* date_format: 每篇文章日期的格式，如YYYY-MM-DD会显示2014-01-01，具体格式设置方法参考moment.js
* per_page: 每页显示的文章数量

## 部署

```
ssl@sslnotebook MINGW32 /d/git/bitbiteme.github.io (master)

$ npm install hexo-deployer-git --save
hexo-site@0.0.0 D:\git\bitbiteme.github.io
`-- hexo-deployer-git@0.3.0
  `-- moment@2.18.1

npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"ia32"})

$ hexo clean
INFO  Deleted database.
INFO  Deleted public folder.

$ hexo generate
INFO  Start processing
INFO  Files loaded in 913 ms
INFO  Generated: index.html
INFO  Generated: archives/index.html
INFO  Generated: archives/2017/07/index.html
INFO  Generated: fancybox/blank.gif
INFO  Generated: fancybox/jquery.fancybox.css
INFO  Generated: fancybox/fancybox_loading.gif
INFO  Generated: fancybox/jquery.fancybox.js
INFO  Generated: fancybox/fancybox_loading@2x.gif
INFO  Generated: fancybox/jquery.fancybox.pack.js
INFO  Generated: archives/2017/index.html
INFO  Generated: fancybox/fancybox_sprite@2x.png
INFO  Generated: fancybox/fancybox_sprite.png
INFO  Generated: fancybox/fancybox_overlay.png
INFO  Generated: css/fonts/FontAwesome.otf
INFO  Generated: js/script.js
INFO  Generated: fancybox/helpers/jquery.fancybox-buttons.css
INFO  Generated: fancybox/helpers/jquery.fancybox-buttons.js
INFO  Generated: fancybox/helpers/jquery.fancybox-thumbs.css
INFO  Generated: fancybox/helpers/jquery.fancybox-media.js
INFO  Generated: css/style.css
INFO  Generated: fancybox/helpers/jquery.fancybox-thumbs.js
INFO  Generated: css/fonts/fontawesome-webfont.eot
INFO  Generated: css/fonts/fontawesome-webfont.woff
INFO  Generated: fancybox/helpers/fancybox_buttons.png
INFO  Generated: css/fonts/fontawesome-webfont.svg
INFO  Generated: css/fonts/fontawesome-webfont.ttf
INFO  Generated: 2017/07/13/git-github/index.html
INFO  Generated: css/images/banner.jpg
INFO  Generated: 2017/07/13/hello-world/index.html
INFO  Generated: 2017/07/13/git-obj/index.html
INFO  30 files generated in 2.52 s

$ hexo deploy

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   package.json

no changes added to commit (use "git add" and/or "git commit -a")

$ git diff
diff --git a/package.json b/package.json
index 0cbcee9..ce8a1ed 100644
--- a/package.json
+++ b/package.json
@@ -7,13 +7,14 @@
   },
   "dependencies": {
     "hexo": "^3.2.0",
+    "hexo-deployer-git": "^0.3.0",
     "hexo-generator-archive": "^0.1.4",
     "hexo-generator-category": "^0.1.3",
     "hexo-generator-index": "^0.2.0",
     "hexo-generator-tag": "^0.2.0",
     "hexo-renderer-ejs": "^0.2.0",
-    "hexo-renderer-stylus": "^0.3.1",
     "hexo-renderer-marked": "^0.2.10",
+    "hexo-renderer-stylus": "^0.3.1",
     "hexo-server": "^0.2.0"
   }
-}
\ No newline at end of file
+}

```

* 部署时根目录下的 CNAME 会被删除，移动到 source 目录即可

## 更改 theme

浏览并找到喜欢的 theme ：https://hexo.io/themes/

clone 到本地
```
$ git clone https://github.com/ppoffice/hexo-theme-hueman.git themes/hueman
```

修改配置文件 _config.yml
```
theme: hueman
```

theme/hueman 下的配置文件原名是 _config.yml.example 改名为 _config.yml
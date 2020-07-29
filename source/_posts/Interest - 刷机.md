---
title: Interest - 刷机
categories:
- Interest
tags:
- Interest
- 
date: 2020-07-15 20:53:46
---

Interest - 刷机

<!--more-->

## 还分卡刷包、线刷包?

线刷包一般是分区镜像，一般需要在下载模式，直接访问闪存。这个状态下闪存上的内容都
可以改动，甚至包括软件层的bootloader，比方说android的通用下载模式fastboot。当然
下载模式不止fastboot，设备在出厂时会有更低级的下载模式(新出厂的闪存里什么都没有
，所以芯片组必须要有向完全空白的闪存写入数据的能力)，比如我们已知的三星挖煤，苹
果dfu等。

而卡刷包一般是指代ota格式的更新包，它需要拥有差分更新的能力(在某些场合，卡刷包只
包含新旧文件的差别以节省空间。如果不使用这个特性，ota格式的包也可以接近成为完整
的刷机包)，因此需要一个能操作单个文件的平台上才能执行，这个平台就是recovery。

> 作者：ff FF
> 链接：https://www.zhihu.com/question/24880799/answer/29307195
> 来源：知乎
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


卡刷包即rom 包，一般是一个 zip 压缩文件，里面有 system 文件夹和其他一两个文件夹
。1卡刷包肯定是*.zip压缩格式。2打开卡刷包后里面一般都会有 system 和 META-INF 、
boot.img(内核)等文件与文件夹。

线刷包通过数据线连接电脑来进行刷机，系统底包，一般是 tg 压缩文件，里面是一大堆
img 文件，某些底包里也有文件夹。

> 作者：奇兔刷机
> 链接：https://www.zhihu.com/question/24880799/answer/260268761
> 来源：知乎
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


最明显的区别是 (卡刷包的文件内容）里有 Recovery 文件，而（线刷包的文件内容）里有
FLASH文件，线刷包文件名里有“FASTBOOT”。


## LG

*#06#  查看 IMEI

IMEI: 358164080527379
部分日版是改IMEI解锁的


你的日版是怎么解锁的啊
酷安上那个问同样问题也是你？刷us998直接解锁


我是LG V30+ 日本AU版V35的小白，求教怎么升9.0呀。
直接刷kdz 保留那三个分区


au版怎么刷9.0 可以刷哪几个版本？
美版 欧版kdz都可以刷。除基带模块xbl xbl2 modem的三个分区不选,其他的全勾选


au版刷20g，20h媒体没声音
回到20e或20a，20b，要么刷到30b，直接上9.0


我相信论坛上有一批机友手里的是au版v30+,日本官方叫lg v35,后壳有au和isai标识，此版
机器极为神秘（操蛋），第一：根本没有官方kdz,基本都是混刷的us998 20a：第二；无论
哪种方式刷机，kdz，或rec卡刷必定导致天线关闭无信号，备份efs再恢复也没用。本人奋
战3天，已经成功找回信号，哈哈哈哈开心。有需要的留言，有必要的话我出个教程。

AU版lg v30+刷机后天线关闭的解决方法：恢复原版modem即可

1,前提root，刷入rec，卡刷原版modem文件
卡刷modem文件： https://pan.baidu.com/s/1uDMuiRCA28QVpph2JsEg-g 提取码:32g2

2,前提root，把提供的modem文件移动到手机根目录，sdcard目录下，注意不是内存卡，手机安装终端模拟器输入su获取权限再输入
dd if=/sdcard/modem of=/dev/block/bootdevice/by-name/modem恢复原版modem，重启即可恢复信号。

原版modem：https://pan.baidu.com/s/10SGg6UyQFMVi4S2P9BG5QQ 提取码:mo8k

注：只适用于日版au版v30。方法一感谢论坛一位机油提供的卡刷包@JIAN3902，经测试简单有效，方法2我自己琢磨出的方法，经测试有效。AU版大胆刷机吧。

另外提示au版用户：kdz混刷别的版本要先在 rec 里备份efs，然后刷机后从rec恢复，否则会出现wifi无法使用的问题。建议混刷h930 31a版本，谷歌补丁最新且自带B&O的几张漂亮壁纸。h930 ds港版混刷后开机搜寻信号太慢，偶尔会掉3g


au版刷成美版nfc就没用了
au开不了nfc  可能是混刷998通病


安卓版本的升级基带都会升级，不同安卓版本必须要刷底包，不然就会变砖。


H930_30Q_Pie9.0精简包是卡刷包，个人认为不用刷底包，直接进TWRP后四清然后卡刷即可。
欧版?现在是美版，可以刷欧版吗?


第三方ROM很多需要vendor分区。使用ROM乐园的文件，开启vendor分区。拥有了Vendor分区
，刷机就方便了很多。下载MIUI或者flyme刷机包，直接格式化data分区，最后刷入MIUI11
等刷机包，完成后重启就等待开机，如果你并没有开启成功vendor分区，可能刷入就会报错
了


Z:\Download\adb-tools-1.0.31>adb devices
List of devices attached
LGUS99832d5fab0 device

Z:\Download\adb-tools-1.0.31>adb devices -l
List of devices attached
LGUS99832d5fab0        device product:joan_nao_us model:LG_US998 device:joan


安卓9 推荐刷 31a 或 30b
刷 MIUI 要先刷 Vendor 分区，再卡刷 MIUI11
没有信号刷 au 基带，
打不开 wifi 恢复 efs，需要先 rec 里备份
使用 *#*#4636#*#* 把天线打开


v30+miui11 可以直接刷 miui12

LGUP 通刷998 无限重启则硬格

lgup通刷us99820a，然后解锁，root，然后用lgup通刷官方安卓9h930 30q开机，然后刷
twrp，刷vendor分区，刷miui，无信号则刷原日版基带

日版频段少 b3 b41 b38

官方原版系统第一次刷入 twrp ， 应是在 fastboot 模式下使用的命令行：
    fastboot flash recovery TWRP.img 
然后使用下列命令重启进入到刷入的 twrp 系统：
    fastboot boot TWRP.img


不要急着刷包，
需要先格式化 data 分区，然后再次重启到 twrp(不要直接重启至系统)，这时 twrp 才稳
定，才可以刷包。

双清、四清失败，显示分区无法挂载，通常都是因为没有格式化 data 分区，
格式化 data 分区将会清除手机所有数据，除了外置存储卡。



强迫症：格式完 data 分区，再四清，然后再刷入 twrp

双清是勾选 data 和 cache 后清除

双清：
Wipe data和Wipe cache partitionWipe data：即恢复出厂设置，将/data下所有的用户数据（包括安装的软件、Dalvik cache、设置、软件的数据等）清空。在较老的设备中是直接格式化/data分区的，内置储存的新设备是保留虚拟SD卡的文件夹（/data/media下），删除其余所有文件及文件夹。Wipe cache partition：清空/cache分区。/cache分区内多储存一些临时文件，比如Play中下载的apk、recovery的log等。一些设备的ota文件也是储存在这里的。

作者：Bruce Luo
链接：https://www.zhihu.com/question/21168134/answer/19223448
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


内核有 zip 和 img 两种格式，zip 通过 twrp 首页的 “安装”选项刷入，img 通过首页
的“恢复”选项刷入(boot 分区)。

网络信号大师 -- 锁频段 -- LTE

刷 kdz 怎么保留 twrp 和 root?
使用分区刷入模式，取消选择 rec 分区，可以保留 twrp


关机状态，按着音量加键插入数据线，则进入 download 模式，这时可以使用 lgup 刷 kdz
退出 download 模式，则同时按音量减和电源按钮直到黑屏，然后手机会自动重启
关机状态，按着音量减键插入数据线，则进入 fastboot 模式



当前手机出厂并非9系统，理论上手机并不带vendor分区，之所有vendor挂载失败，说明你
当前的并不支持，需要更换TWRP_recovery；有意思的是，部分机型出厂不自带vendor分区
，但开发者会后期加入，这样的情况下，我们需要刷入一个支持vendor分区的底包，如
lineage16等，乐视Max2，乐视Pro3就出现过

若刷机包并未集成Vendor分区文件，导致你刷机后，手机没有了Vendor分区，最简单的办法就
是直接刷一个官方原版包，一般都恢复了Vendor分区了



据我所知，目前有VS996 LS998 V300L US998 H930 H930DS H932等这些版本，VS996属
于美版机型，LS998和US998分别为S版和U版，全网通机型；而V300L系列则就是我们常说
的韩版，H930系列欧版系列。具体版本可能整理存在错误，可私信我们改正

首先我们说ROOT，以上版本，除了H932以外，都是可以非常简单的混刷到US998或者H930
版本，到底这个版本之后，就可以根据ROM乐园教程快速解锁手机BootLoader，然后一键刷
入TWRP_recovery，最终刷入magisk获取面具root；

第三方ROM刷入机型：LS998 or V300L VS996 US998中 任意一款，这些都可以完美混刷成
US 998 or H930。只要刷成了US998 OR H930 就可以在X使用一大批类原生ROM了，也包括
目前的国产ROM MIUI12等系统。不过需要注意的是：目前很多第三方ROM需开启Vendor分
区，具体教程ROM乐园也有提供


卡刷完magisk后刷AK3_RCTD_Remover禁用LG固件的root检查，不然可能会影响性能

lgup 混刷时不要选 modem, xbl, xbl2，则不会出现基带丢失

QFIL 恢复 qcn，刷机之前备份 qcn


v30是日本运营商Docomo版，型号L-01k
               au版 LGV35
               
手动操作,任何情况按住电源键和音量减8秒，手机会关机重启，9008除外

硬格：
手机关机状态
按住电源键和音量减
出来LOGO（白字）松开电源键再迅速按两下
出来“yes”界面选“yes”并确认（音量键上下选择，电源键确认）
再一次“yes”并确认,然后就可以了
注：如果刷好TWRP的，此方法进TWRP



twrp 升级：进入 twrp -- 选安装 -- 选安装镜像 -- 选镜像文件 -- 选要刷入的分区为
recovery -- 滑动刷入 -- 完成后返回 -- 重启 -- 重启到 recovery -- 进入确认

android pie 9.0 强制 data 加密，因此刷完安卓 9 要格式化 data 分区，但会丢数据，
或者刷下面的布丁，否则会进不去系统：
Disable_Dm-Verity_ForceEncrypt:
你忘刷 Disable_Dm-Verity_ForceEncrypt 了，data 现在都是强制全盘加密，当然格式化也行，数据就没了。
此工具可以屏蔽禁用 MIUI 等 Android 系统使用第三方 REC 如：TWRP 刷机后，重启手机 Boot 会不断循环的问题。


Flashing a newer KDZ (like to Pie) will not re-lock your bootloader.
With unlocked bootloader, then on Pie KDZ you can install TWRP and root.


安卓9(P 或 Pie) 上安装 twrp，严格按照下列操作：
https://forum.xda-developers.com/showpost.php?p=79972563&postcount=107


AOSP是“Android Open-Source Project”，Android 开放源代码项目，如 AOSP/Lineage

AOKP 的全称为“Android Open-Source Kang Project”，比AOSP 多了一个“Kang”。在Android社区中，Kang是一个术语，即用来表示这是一个被他人修改过的ROM，修改ROM的人不属于制作ROM的团队或没有该团队的授权。


进入rec开始刷入系统
点击清除，选择格式化Data分区，格式化后选择高级清除,勾选 Cache、System、Data、Dalvik/ART Cache，滑动滑块清除。
清除后回到主界面点击安装，选择储存位置
依次刷入 底包、ROM包、Disable_Dm-Verity_ForceEncrypt，刷完后即可重启使用全新的系统了。
如果需要root的用户可在rec中刷入magisk的包


什么是底包?
我们平时刷第三方 ROM，实际上只是刷了 boot 和 system 两个分区（俗称内核和系统）。对于大多数手机来说，除了这两个分区，还包括了大家俗称的基带、Modem、TrustZone 等必不可少的分区。
这些分区的版本是需要和系统或内核里相关的驱动版本一致才可以正常工作。比方说，假如系统里的驱动更新了，而 TrustZone 没有更新，那么指纹传感器可能会不正常；Modem 版本不对，可能会没有网络。


vendor 分区?


先解锁 bl，再升级到 安卓9
ALSO do NOT update to LG stock Pie yet if you wish to run LOS-16 "Pie" custom ROMs 
If you flash pie KDZ then AOSP/(Lineage) ROMs like LOS, RR, havoc won't work properly.
Even if you flash (downgrade back to) Oreo KDZ and then flash AOSP ROMs, the camera and flashlight won't work on enforcing but will work on permissive.
But even on permissive a few sensors other than the camera may not work. This will be the case until AOSP ROMs with Pie blobs come.


安卓9将会导致不可逆的持久性改变，安装前留意


解锁 bl 后有提示画面，可以重新上锁：
adb reboot bootloader
fastboot oem lock
fastboot reboot
但是会恢复出厂设置?
变砖?需要在 0 修改的系统上才可以
I ran "fastboot oem lock" and now I'm bricked with "your device has failed a routine safety check and will not boot" Does anyone have the fix for this?
You need to be on a stock KDZ(literally 0 changes) before locking



现象：OEM解锁是灰色的不可点击，但是处于未解锁状态(正常解锁应该是按钮不可点击但状
态是已解锁)，且进入 fastboot 模式就重启。

有人说的解决方法，重新上锁：
打开ADB，然后fastboot下fastboot oem lock，完事刷KDZ完美解决

但是另外有人的经验是：
没那么简单。。试了多次了。。解锁后再刷KDZ。。OEM解锁就变灰关着了。。虽然开机黄三角还在。。但这时候fastboot模式已经进不去了。。准确的说是插数据线的情况下一进入fastboot就自动重启。。在进入fastboot的瞬间拔下数据线虽然不重启。。但没有数据线的fastboot模式没有任何意义。。
结果送淘宝解了三次。。但这个已经和最初的解锁不一样了。。最初解一次后OEM解锁是灰色开着的。。再刷KDZ解了后OEM解锁就是可关可开了。。
所以个人强烈建议解锁后就别再刷KDZ了。。

但是又有人说：
楼主，我的已经解决了，是驱动问题，把lg驱动卸载了，然后手机打开调试，电脑连接360手机助手，等手机端安装后，自动装驱动，等电脑端能连上手机后，退出360助手，完了adb进fastboot，直接刷twrp就可以了，后续按教程走就可以了。

跳过解锁步骤。直接刷twrp..fastboot重启是电脑的问题。换个口或者换个电脑就行了


刷机注意 lgv30 的单卡、双卡区别
7、最后发现刷机顺序是这样的：
7.1、好像就美版H930、us998才有完整功能：fastboot、OEM、recovery等，其它版本总是少点东西，还可能出现分区表不同，
7.2、发现分区不同的，最好不要强刷，会灵异问题；
7.3、vs996单卡就找us998玩，，
7.4、双卡就找H930玩，，
http://bbs.gfan.com/android-9606122-1-1.html

8.0以上版本打开oem解锁需要登录谷歌账号



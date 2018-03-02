---
title: chapter 6.1 - GitHub 创建账户和配置
categories:
  - Git
  - Book-ProGit
tags:
  - Git
  - GitHub
---

GitHub 是最大的 Git 版本库托管商，免费账户的版本库都将被公开，收费账户可以有私有版本库，如果只是克隆公开的版本库，无需注册账户

<!--more-->

### 标识

|Logo|官方标识|吉祥物章鱼猫Octocat|
|---|---|---|
|<img src="https://assets-cdn.github.com/images/modules/logos_page/GitHub-Logo.png" width="100" height="100"/>|<img src="https://assets-cdn.github.com/images/modules/logos_page/GitHub-Mark.png" width="100" height="100" />|<img src="https://assets-cdn.github.com/images/modules/logos_page/Octocat.png" width="100" height="100" />

### 账户设置

#### SSH 公钥

Personal settings --> SSH and GPG keys

![](https://git-scm.com/book/en/v2/images/ssh-keys.png)
* 公钥应该起一个有意义的名字，以后删除密钥时方便区分

#### 头像

Personal settings --> Profile

github 可以使用 Gravatar 托管服务

Gravatar
: Globally Recognized Avatar 的缩写,是 gravatar 推出的一项服务，意为“全球通用头像”。如果在 Gravatar 的服务器上放置了你自己的头像，那么在任何支持 Gravatar 的 blog 或者留言本上留言时，只要提供你与这个头像关联的 email 地址，就能够显示出你的 Gravatar 头像来。

#### Email

Personal settings --> Emails

![](https://git-scm.com/book/en/v2/images/email-settings.png)
* 可以设置多个邮件地址，但只能有一个主要地址
* 主要地址将会接收到所有的通知和回复
* 任意版本库的任意提交包含了你的邮件地址，github 会将它们链接到你的账户

#### 两步验证

Two-factor authentication，简写为2FA，是一种用于降低因密码被盗而带来的账户风险的验证机制，现在已经变得越来越流行。 

开启两步验证，GitHub 会要求你用两种不同的验证方法，这样，即使其中一个被攻破，攻击者也不能访问你的账户。

![](https://github.com//images/modules/settings/2fa_guide.png)

* 两步验证
    * 第一步还是传统的用户名、密码
    * 第二步有两个方法
        * 使用手机 app 生成认证码，认证码是一种基于时间的一次性密码 A Time-based One-Time Password (TOTP)
        * 通过手机短信发送验证码
* 第二步建议使用 TOTP，比手机短信更可靠（跨国发短信的问题），尤其是美国国外的手机用户
* 使用的手机APP：
    * For Android, iOS, and Blackberry: Google Authenticator
    * For Android and iOS: Duo Mobile
    * For Windows Phone: Authenticator

更多说明：
https://help.github.com/articles/configuring-two-factor-authentication-via-a-totp-mobile-app/


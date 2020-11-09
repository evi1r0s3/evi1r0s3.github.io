---
layout: post
title:  "pwnlab_init"
description: walkthrough
category: vulnhub
---
# pwnlab_init

![image-20200628014413093]({{ site.url }}/assets/pwnlab_init.assets/image-20200628014413093.png)

![image-20200628014539988]({{ site.url }}/assets/pwnlab_init.assets/image-20200628014539988.png)

![image-20200628020414612]({{ site.url }}/assets/pwnlab_init.assets/image-20200628020414612.png)

![image-20200628020608091]({{ site.url }}/assets/pwnlab_init.assets/image-20200628020608091.png)

login测试sql绕过失败，upload需要登录后使用，config页面空白

![image-20200628020543374]({{ site.url }}/assets/pwnlab_init.assets/image-20200628020543374.png)

查看代码，发现应该可以使用php过滤器查看页面后台代码

![image-20200628020501053]({{ site.url }}/assets/pwnlab_init.assets/image-20200628020501053.png)



查看login

![image-20200628020918397]({{ site.url }}/assets/pwnlab_init.assets/image-20200628020918397.png)

![image-20200628020945328]({{ site.url }}/assets/pwnlab_init.assets/image-20200628020945328.png)

登录使用了config，查看config页面代码

![image-20200628021032918]({{ site.url }}/assets/pwnlab_init.assets/image-20200628021032918.png)

![image-20200628021151758]({{ site.url }}/assets/pwnlab_init.assets/image-20200628021151758.png)

得到数据库凭证

```bash
root:H4u%QJ_H99
```

登录数据库

![image-20200628021926433]({{ site.url }}/assets/pwnlab_init.assets/image-20200628021926433.png)

![image-20200628022110643]({{ site.url }}/assets/pwnlab_init.assets/image-20200628022110643.png)

分别用base64解密

```bash
kent:Sld6WHVCSkpOeQ==:JWzXuBJJNy
mike:U0lmZHNURW42SQ==:SIfdsTEn6I
kane:aVN2NVltMkdSbw==:iSv5Ym2GRo
```

随便使用一个账户登录

进入upload

使用之前同样的方法查看upload源码

![image-20200628023915311]({{ site.url }}/assets/pwnlab_init.assets/image-20200628023915311.png)



![image-20200628023810100]({{ site.url }}/assets/pwnlab_init.assets/image-20200628023810100.png)

发现upload会审查图片格式后缀，然后修改文件名并上传到upload文件夹下。

制作rshell

![image-20200628024203598]({{ site.url }}/assets/pwnlab_init.assets/image-20200628024203598.png)

![image-20200628024242762]({{ site.url }}/assets/pwnlab_init.assets/image-20200628024242762.png)

上传

![image-20200628025905796]({{ site.url }}/assets/pwnlab_init.assets/image-20200628025905796.png)

查看upload

![image-20200628025951237]({{ site.url }}/assets/pwnlab_init.assets/image-20200628025951237.png)

但是在upload文件夹下，文件并不能被执行。

![image-20200628024809475]({{ site.url }}/assets/pwnlab_init.assets/image-20200628024809475.png)

这应该是个兔子洞，这种形式的图片显示通常不能得到shell

继续查看其他页面后台代码。

在查看index的后台代码时发现，对cookie的处理是存在包含的。应该可以提供一个LFI给我们

![image-20200628025449030]({{ site.url }}/assets/pwnlab_init.assets/image-20200628025449030.png)

![image-20200628025430800]({{ site.url }}/assets/pwnlab_init.assets/image-20200628025430800.png)

尝试验证，重新浏览主页抓包修改cookie，使用lang参数尝试包含passwd。成功执行。

![image-20200628025650088]({{ site.url }}/assets/pwnlab_init.assets/image-20200628025650088.png)

现在我们可以包含我们之前上传的gif php-rshell文件了

在kali本地1337端口建立监听，复制之前图片的地址，替换并发包

![image-20200628030131318]({{ site.url }}/assets/pwnlab_init.assets/image-20200628030131318.png)

![image-20200628030226562]({{ site.url }}/assets/pwnlab_init.assets/image-20200628030226562.png)

进入home文件夹后发现

![image-20200628030748778]({{ site.url }}/assets/pwnlab_init.assets/image-20200628030748778.png)

除了之前得到凭证的三个用户外存在新用户john

使用之前得到的三个凭证分别切换用户，搜集信息。

kent目录下没有什么有用的信息

![image-20200628030938750]({{ site.url }}/assets/pwnlab_init.assets/image-20200628030938750.png)

mike不能切换

![image-20200628031026911]({{ site.url }}/assets/pwnlab_init.assets/image-20200628031026911.png)

而kane用户目录下存在一个可以以mike身份执行的suid可执行文件，相信它可以帮助我们切换到mike的身份

![image-20200628030911457]({{ site.url }}/assets/pwnlab_init.assets/image-20200628030911457.png)



![image-20200628031342850]({{ site.url }}/assets/pwnlab_init.assets/image-20200628031342850.png)

在执行后发现应该是调用了cat命令，这种方式可以简单的通过伪造环境变量来欺骗程序

首先伪造cat

![image-20200628031742461]({{ site.url }}/assets/pwnlab_init.assets/image-20200628031742461.png)

然后修改环境变量，并执行程序

![image-20200628031816851]({{ site.url }}/assets/pwnlab_init.assets/image-20200628031816851.png)



用户目录下

![image-20200628031941301]({{ site.url }}/assets/pwnlab_init.assets/image-20200628031941301.png)

![image-20200628032019117]({{ site.url }}/assets/pwnlab_init.assets/image-20200628032019117.png)

看样子调用了echo

并且我们可以输入。

尝试命令注入

![image-20200628032223405]({{ site.url }}/assets/pwnlab_init.assets/image-20200628032223405.png)

存在。现在我们只需要直接拉起shell。

![image-20200628032512073]({{ site.url }}/assets/pwnlab_init.assets/image-20200628032512073.png)

忘了伪造了cat。。LOL
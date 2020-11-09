---
layout: post
title:  "DeRPnStiNK"
description: walkthrough
category: vulnhub
---
# DeRPnStiNK

![image-20200618215334556]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618215334556.png)



![image-20200618215439742]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618215439742.png)

![image-20200618220148829]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618220148829.png)

![image-20200618221021849]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221021849.png)

![image-20200618221136381]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221136381.png)

![image-20200618221204314]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221204314.png)

![image-20200618221224944]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221224944.png)











![image-20200618221105492]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221105492.png)

![image-20200618221641349]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221641349.png)

![image-20200618221729517]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221729517.png)

修改hosts以便正常显示博客内容

![image-20200618221938058]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618221938058.png)

![image-20200618222030747]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618222030747.png)

```bash
wpscan --url http://derpnstink.local/weblog/ -e ap,vt,cb,u --force
```

![image-20200618222253219]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618222253219.png)

试了一下弱密成功登录`admin：admin`

![image-20200618222638969]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618222638969.png)

制作一个phprshell



![image-20200618223719451]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618223719451.png)

点击上传会直接返回shell

![image-20200618223815404]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618223815404.png)

上传linpeas做一些枚举

![image-20200618225426340]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618225426340.png)

linpeas，3306数据库

![image-20200618225341521]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618225341521.png)

也扫描出了数据库凭证

![image-20200618225540649]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618225540649.png)

机器上还有phpmyadmin

![image-20200618230307660]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618230307660.png)



![image-20200618230825683]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200618230825683.png)

顺便写一下mysql的基本操作,语句记得使用`;`结束

```bash
mysql> status;		#查看数据库状态
```

![image-20200619015048984]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619015048984.png)

```bash
mysql> use information_schema;		#使用信息库，为下一步查询做准备
mysql> select schema_name from information_schema.schemata;		#看看一共有那些数据库
```

![image-20200619015239271]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619015239271.png)

```bash
mysql> use wordpress;		#选择wp库
mysql> show tables;		#显示所有表
```

![image-20200619015841706]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619015841706.png)

```bash
mysql> select * from wp_users;		#查看所有内容
```

![image-20200619020332060]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619020332060.png)

```bash
unclestinky
$P$BW6NTkFvboVVCHU2R9qmNai1WfHSC41
```

参考home目录下用户，这个hash应该是stinky用户的

![image-20200619020629295]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619020629295.png)

```bash

```





![image-20200619021545475]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619021545475.png)

经过一些查看发现在home文件夹的纵深文件夹中存有一个ssh私钥

![image-20200619022101349]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619022101349.png)

![image-20200619022449317]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619022449317.png)

虽然有了私钥，但是我们还是需求密码。

继续查询，发现一段对话

![image-20200619023005029]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619023005029.png)

并在Documents下发现了一个pcap流量包。

![image-20200619023416242]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619023416242.png)

有nc，直接利用nc传回kali

![image-20200619023643444]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619023643444.png)

![image-20200619023704097]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619023704097.png)

在查阅包时发现mrderp曾经新建用户，

![image-20200619024613587]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619024613587.png)

```bash
mrderp:derpderpderpderpderpderpderp
```

ssh尝试一下密码复用，使用之前的私钥

![image-20200619024834532]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619024834532.png)

成功

![image-20200619024903823]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619024903823.png)



![image-20200619024952629]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619024952629.png)

![image-20200619025223080]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619025223080.png)

但是显然，当前用户的目录下并没有这个文件夹。这应该是一个遗留的设置，这导致，我们可以通过自己生成符合sudo条件的脚本从而直接root

![image-20200619025828739]({{ site.url }}/assets/DeRPnStiNK.assets/image-20200619025828739.png)
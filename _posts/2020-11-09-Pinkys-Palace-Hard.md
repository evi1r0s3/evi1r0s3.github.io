---
layout: post
title:  "Pinkys-Palace-Hard"
description: walkthrough
category: vulnhub
---
# Pinkys-Palace-Hard

![image-20200624020321480]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624020321480.png)



![image-20200624020457892]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624020457892.png)

![image-20200624023427881]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624023427881.png)

![image-20200624023925144]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624023925144.png)

![image-20200624023447134]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624023447134.png)

![image-20200624023510067]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624023510067.png)

这三个数字看起来很像是端口号，而pinkydb则应该是域名，先把它加入hosts

![image-20200624024037922]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624024037922.png)

主页面正常显示

![image-20200624024117871]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624024117871.png)

回到这三个数字号，8890,7000,666，考虑的到之前扫描结果中很多端口都被过滤。这三个数字可能是port knock 的顺序端口。

但是按照这个顺序敲端口后扫描并没有打开端口。

如果只是顺序错误，那么我们可以先罗列出可能存在的顺序

```bash
python -c 'import itertools; print list(itertools.permutations([8890,7000,666]))' | sed 's/), /\n/g' | tr -cd '0-9,\n' | sort | uniq > ports.txt
```

![image-20200624032454430]({{ site.url }}/assets/Pinkys-Palace-Hard.assets/image-20200624032454430.png)

然后再写一个利用nmap进行敲端口并测试的脚本。

```bash
#!/bin/bash
TARGET=$1
for ports in $(cat ports.txt); do
    echo "[*] Trying sequence $ports..."
    for p in $(echo $ports | tr ',' ' '); do
        sudo nmap -Pn --host-timeout 100 --max-retries 0 -p $p $TARGET
    done
    sleep 3
	sudo nmap -Pn -p- -sS -T5 $TARGET
done
```




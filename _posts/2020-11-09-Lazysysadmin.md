---
layout: post
title:  "Lazysysadmin"
description: walkthrough
category: vulnhub
---
# Lazysysadmin

![image-20200713202613792]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713202613792.png)

![image-20200713202800741]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713202800741.png)

![image-20200713202849564]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713202849564.png)

![image-20200713204056539]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713204056539.png)

![image-20200713204245975]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713204245975.png)

![image-20200713210455291]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713210455291.png)

```bash
 wpscan --url http://192.168.110.133/wordpress/ -e ap,vt,cb,u --force
```

![image-20200713210003649]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713210003649.png)

![image-20200713210129783]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713210129783.png)

![image-20200713210210466]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713210210466.png)

所以用户名是admin，应该可以暴力。但是这是我们最后的选择



![image-20200713210809265]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713210809265.png)

![image-20200713210841824]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713210841824.png)

在wordpress下的配置文件中发现mysql凭证

![image-20200713211050154]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713211050154.png)

![image-20200713211015858]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713211015858.png)

```bash
Admin:TogieMYSQL12345^^
```

而我们有phpmyadmin

直接登录

![image-20200713211213488]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713211213488.png)

显然需要本地登录，虽然我们能够查看admin的hash，虽然可以尝试破解，但是应该优先把简单的步骤都尝试掉

![image-20200713211500500]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713211500500.png)

因为考虑到之前测试出wp的用户名也是admin所以大概率存在密码复用。尝试使用之前得到的数据库凭证登录wp

![image-20200713211851349]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713211851349.png)

成功登录wp后台，常规操作，404可改，替换内容为php reverse shell，修改并上传

![image-20200713212254568]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713212254568.png)

设立监听，并根据主题浏览

```bash
http://192.168.110.133/wordpress/wp-content/themes/twentyfifteen/404.php
```

得到shell

![image-20200713212814836]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713212814836.png)

![image-20200713214155119]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713214155119.png)

明文凭证，其实这个在一开始就可以在smb的共享文件中发现，但是账户确是只能猜测为togie，而得到shell之后就可准确确定账户名为togie

![image-20200713214026784]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713214026784.png)

尝试使用弱密切换到togie

![image-20200713215114693]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713215114693.png)

![image-20200713215258176]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713215258176.png)

直接可提权

![image-20200713215726520]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713215726520.png)

![image-20200713215523578]({{ site.url }}/assets/Lazysysadmin.assets/image-20200713215523578.png)
---
title: GitHub pages个性域名实现https访问
date: 2017-06-12 14:46:06
tags: 瞎搞
categories: 瞎搞
---

## Netlify 搭建网站
[Netlify](https://www.netlify.com/) 是一家提供静态网络托管服务的初创公司，旨在提升网页加载速度，提升用户网页访问体验。  
用GitHub 账号授权登录 Netlify  

+ 登录后
 ![QQ20170612165254.png](https://tuchuang001.com/images/2017/06/12/QQ20170612165254.png)
+ 选择GitHub
 ![QQ20170612165336.png](https://tuchuang001.com/images/2017/06/12/QQ20170612165336.png)  
+ 点击blog 的repo
 ![QQ20170612165925.png](https://tuchuang001.com/images/2017/06/12/QQ20170612165925.png)
+ Deploy 你的网站
 ![QQ20170612170139.png](https://tuchuang001.com/images/2017/06/12/QQ20170612170139.png) 
+ 等待一段时间后，如下图所示，我设置我的名字为dxx 于是就可以通过 dxx.netlify.com 来访问我的网站，你的就是你设置的 NAME.netlify.com 
 ![QQ20170612170412.png](https://tuchuang001.com/images/2017/06/12/QQ20170612170412.png)
 
## 启用Https
 
+ 请到你的域名管理者那里设置CNAME解析，请把dxx 换成你自己的 NAME
![QQ20170612170913.png](https://tuchuang001.com/images/2017/06/12/QQ20170612170913.png)
 
+ 然后到 Netlify 添加你的域名或子域名，在Netlify 的官网的 HTTPS 菜单下，勾选上 Let’s Encrypt ， 然后如果成功，则如下图所示。
 ![QQ20170612171233.png](https://tuchuang001.com/images/2017/06/12/QQ20170612171233.png)
 

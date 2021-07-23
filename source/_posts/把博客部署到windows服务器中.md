---
title: 把hexo博客部署到windows服务器中
excerpt: 八步教你把博客部署到windows服务器中
tags: [生活杂记]
categories: 生活杂记
banner_img: /img/壁纸.jpg
---

<p class="note note-primary"; style="font-size:22px">
   前提需要：一个三个月以上时间的windows服务器，一个已经备案好的域名，已经会熟悉使用hexo，为了把hexo从github中部署到自己的服务器中。
</p>

#### 1、搜索windows工具搜到远程桌面连接工具，用远程桌面连接连接到服务器，然后输入设置的账号密码

![远程桌面连接](http://hkctfsys.com/img/远程桌面连接.png)

#### 2、把IE增强的安全配置给关了（安全就代表麻烦），win->服务器管理器->IE增强的安全配置：启动点进去然后关闭

#### 3、下载[宝塔](https://www.bt.cn/),选windows版本，点下载，用来集成配置服务器的好工具

![宝塔进入界面](http://hkctfsys.com/img/宝塔进入界面.png)

#### 4、点击打开，登陆，创建一个宝塔账号，然后，进入，选Apach和Nginx套餐都可以

#### 5、宝塔先放放，等下再绑定域名，现在先下载**[git](http://hkctfsys.com/Git-2.30.0.2-64-bit.exe)**和**[node](http://hkctfsys.com/node-v15.10.0-x64.msi) **（点击可获得安装包，可能不是最新版的也可以去官网下载

下载完之后安装，一直点next就行）

node下载安装有一个注意点是,到这一步要选Add To Path

![node安装注意事项](http://hkctfsys.com/img/node安装注意事项.png)

#### 6、右键打开git，点击gitbosh安装hexo，创建一个hexo名字的或者任何名字的文件夹，进去，然后右击点git bosh

![gitbosh](http://hkctfsys.com/img/gitbosh.png)

```undefined
npm install -g hexo-cli //如果没反应可以到cmd运行这个代码
```

初始化hexo程序，如果有hexo 的人可以直接把hexo文件复制进去就可

```kotlin
hexo init 
```

然后输入

```
hexo g
```

```
hexo s
```

打开网页，输入http://localhost:4000 就可以看到hexo界面了，说明成功一大半了



![hexo界面](http://hkctfsys.com/img/hexo 界面.png)

#### 7、下载和配置Nginx

[Nginx安装包](http://hkctfsys.com/nginx-1.16.1.zip)，下载了之后点击nginx-1.16.1->conf->nginx,这个文件用txt打开，然后把下放的root改成你hexo的public地方，其他不用改

```

server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   C:\Users\Administrator\Desktop\hexo\public;
            index  index.html index.htm;
        }

```

下面介绍为什么要下载nignx和为什么要把root 地址改成hexo的public文件夹，因为source文件夹中makedown文件执行hexo g被编译成html文件放在public文件夹中，并且把网站的根目录指向public文件夹，用Nginx反向代理使得人们可以访问我这个网站。

![http://hkctfsys.com/img/Nginx%E5%8E%9F%E7%90%86.png](http://hkctfsys.com/img/Nginx原理.png)

#### 8、接下来让网站根目录指向public文件夹，打开宝塔软件,信息输入密码之后点保存，然后通过面板地址，http: f/hkctfsys. com: 8990/OEsp376s（这是我的面板地址，在面板最上面找）

![http://hkctfsys.com/img/%E5%AE%9D%E5%A1%94%E9%9D%A2%E6%9D%BF%E8%AE%BE%E7%BD%AE.png](http://hkctfsys.com/img/宝塔面板设置.png)

然后点开网站->添加站点,把根目录指向hexo的public文件夹，大功告成啦

![http://hkctfsys.com/img/%E5%AE%9D%E5%A1%94%E6%B7%BB%E5%8A%A0%E7%AB%99%E7%82%B9.png](http://hkctfsys.com/img/宝塔添加站点.png)

现在在浏览器里面输入你的域名，可以正常显示了

![http://hkctfsys.com/img/%E5%8D%9A%E5%AE%A2%E9%A6%96%E9%A1%B5.png](http://hkctfsys.com/img/博客首页.png)
---
layout:     post
title:      阿里云服务器nginx配置ssl证书,给网站添加https
subtitle:  阿里云服务器nginx配置ssl证书,给网站添加https
date:       2018-12-24
author:     BY
header-img: img/tag-bg-o.jpg
catalog: true
tags:
    - ssl证书部署
    - 服务器部署
---

众所周知，网站如果没有ssl证书的话，会被浏览器默认为不安全的链接，如果需要给网站配一个https的协议，则需要购买证书，个人购买证书的话，这里推荐阿里云的 Symantec免费证书。
### 一、证书购买流程
点击ssl证书，点立即购买，进入购买页面，先点击Symantec，选择一个域名，就会出现免费型DV SSL选项，勾选以后点购买就行了。
![ssl证书购买](https://img-blog.csdnimg.cn/20181224152146901.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzQ2Njg3MQ==,size_16,color_FFFFFF,t_70)
###  二、证书使用
![下载nginx需要的ssl证书](https://img-blog.csdnimg.cn/20181224153235276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzQ2Njg3MQ==,size_16,color_FFFFFF,t_70)
将证书绑定上自己的域名，将域名按照流程解析，下载nginx证书，并上传到服务器
![下载的证书](https://img-blog.csdnimg.cn/20181224153602261.png)
下载的证书有两个文件，一个是.key格式的，一个是.pem,在服务器上nginx.conf同级文件下新建一个文件夹，将两个文件放上去。
我用的是命令行上传到服务器的，如果有图形化的软件应该更方便
上传文件到云服务器命令
```
 scp  /Users/dj/Downloads/aaa/a.pem root@云服务器ip:/usr/local/nginx/crt
```
 /Users/dj/Downloads/aaa/a.pem是你本地的文件名
 /usr/local/nginx/crt是云服务器的文件夹
### 三、nginx配置ssl证书
首先在云服务器的安全组上添加端口为443的安全组，80端口是http默认端口，443为https默认端口
然后打开nginx的文件夹下的nginx.conf,找到https server这句话，将后面的server{}里的内容改为
```js
# HTTPS server 
    #
    server {
        listen       443 ssl;
        server_name  你的网站域名;
        ssl on;
        #root html;
        #index index.html index.htm;

       ssl_certificate      cert/a.pem;	//.pem文件的地址
        ssl_certificate_key  cert/a.key;  //.key文件的地址

       # ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        #ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location / {
                proxy_pass  http://127.0.0.1:8080/;//代理到哪个端口
           # root   html;
           # index  index.html index.htm;
        }
    }

}
```
重启nginx 
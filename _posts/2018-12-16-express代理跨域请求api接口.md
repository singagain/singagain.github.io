---
layout:     post
title:      ---
layout:     post
title:      express代理跨域请求api接口
subtitle:    express代理跨域请求api接口
date:       2018-12-16
author:     Teemo
header-img: img/post-bg-rwd.jpg
catalog: true
tags:
    - express
    - 前端知识
    - nodejs
---


经常会发生请求其他外部接口发生跨域，或者暴露key等令人头疼的问题，这时候可以用express做一个转发去请求另外一个接口

话不多说，上代码：
```js
router.post('/proxy', function(req, res, next){
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With , yourHeaderFeild');
    res.header('Access-Control-Allow-Methods', 'PUT, POST, GET, DELETE, OPTIONS');
    console.log(req.body)
     request({
         url:'http:/你要请求的其他网站接口/api/v2',
         method:'POST',
         headers:{
            'Content-Type': 'application/json',
         },
         body: JSON.stringify({//body中放请求参数
            "reqType":0,
            "perception": {
                "inputText": {
                    "text": req.body.info
                },
            },
            "userInfo": {
                "apiKey": "",
                "userId": req.body.userid
            }
        })
     },function (error, response, body) {
                    if (!error&&response.statusCode == 200) {
                        console.error('101' + body);
                        res.send(body);
                    }
                })
       
});
```

返回的body是请求其他网站的接口返回的数据
如果是jsonp请求则用`res.jsonp(body);`a返回数据
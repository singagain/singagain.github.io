---
layout:     post
title:     egg-jwt生成token，登录验证
subtitle:  egg-jwt生成token，登录验证
date:       2020-4-30
author:     Teemo
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - node
    - egg
---






### 生成Token
`controlle`r 中的 `login.js`中
```js
           const { ctx,app} = this;
            const token = app.jwt.sign({

                'username': username, //需要存储的 token 数据
                //......

            }, app.config.jwt.secret);
            console.log(token)
            ctx.set({'authorization':token})//设置headers
```
前端可以根据`response`的`headers`是否携带名为`authorization`的`token`来判断是否需要跳到登陆页

前端
```js
// request拦截器, 改变url 或 options.
request.interceptors.request.use(async (url, options) => {
  let token = localStorage.getItem('authorization');
  let headers = {
    'Content-Type': 'application/json',
    Accept: 'application/json'
  };
console.log(token)
  if (token != null) {
    headers['authorization'] = `Bearer ${token}`; //要在前面加上 Bearer 字符串和一个空格
    return {
      url,
      options: { ...options, headers }
    };
  }else{
    console.log(2222222)
    router.push('/login');
    return {
      url,
      options: { ...options, headers }
    };
  }
  
});
```
### 封装为中间件
```js
//middleware/jwtErr.js
module.exports = (options) => {
    return async function jwtErr(ctx, next) {
        const token = ctx.request.header.authorization;
        let decode = '';
        if (token) {
          try {
            // 解码token
            decode = ctx.app.jwt.verify(token, options.secret);
            await next();
            console.log('decode======>',decode);
          } catch (error) {
            ctx.status = 401;
            ctx.body = {
              message: error.message,
            };
            return;
          }
        } else {
          ctx.status = 401;
          ctx.body = {
            message: '没有token',
          };
          return;
        }
      };
}
```
使用
```js
//router.js
module.exports = app => {
  const { router, controller ,middleware} = app;
  const jwtErr = middleware.jwtErr(app.config.jwt)
  router.post('/api/login',controller.user.login);
  router.post('/api/register',controller.user.register);
  router.get('/api/userInfo',jwtErr,controller.user.userInfo);
  router.post('/api/upload',controller.upload.upload);
};
```
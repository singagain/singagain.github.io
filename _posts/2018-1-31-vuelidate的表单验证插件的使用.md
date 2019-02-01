---
layout:     post
title:     vuelidate的表单验证插件的使用
subtitle:  vuelidate的表单验证插件的使用
date:       2018-1-31
author:     Teemo
header-img: img/tag-bg-o.jpg
catalog: true
tags:
    - vue
    - 表单验证
---


在vue中，我们使用vuelidate实现表单验证，达到限制用户输入的目的，以及提交时验证表单的目的。
 [查看官方文档](https://monterail.github.io/vuelidate/)
 ### 第一步：安装
 可以通过npm安装
 ```
 	npm install vuelidate --save
 ```
 然后导入到main.js中
 ```js
 import Vue from 'vue'
import Vuelidate from 'vuelidate'
Vue.use(Vuelidate)
 ```
### 第二步：在组件中使用
绑定数据需要用==v-model.trim==绑定，使用```this.$v.user.user_name.$touch()```去触发验证事件，```this.$v.user.user_name.required```为true时表示验证通过
```js
<template>
	<form>
		<input type="text" class="userName" placeholder="请输入用户名" v-model.trim="user.user_name">
		 <input type="button" value="登录" class="submit" @click="login">
	</form>
</template>
import { required} from 'vuelidate/lib/validators'
export default {
data(){
        return {
            user:{
                user_name:"",
                }
         }
     },
validations: {
        user: {
            user_name: {
            required,
            },
        }
  },
   methods:{
        login(){
            this.$v.user.user_name.$touch()
            if(!this.$v.user.user_name.required){
				Toast('用户名不能为空');
            }
         }
}
```
### 补充：自定义验证规则
新建js文件，在文件中引入
列子：基于正则表达式的验证器
```js
//js文件
import { helpers } from 'vuelidate/lib/validators'
export var phone = helpers.regex('phone', /^1(3|4|5|7|8)\d{9}$/);

//组件中
import {phone} from "../../api/validate.js"
validations: {
            user: {
                mobile: {
				phone,
                },   
          }
```
基于定位器的验证器
如果您想使用locator策略(与sameAs或requiredIf内置验证器中的定位器策略完全相同)，您可以使用ref helper来实现这一点，其方式与在这两个验证器中使用定位器的方式完全相同。
```js
import { req, ref, withParams } from './common'

export default (prop) =>
  withParams({ type: 'requiredIf', prop }, function(value, parentVm) {
    return ref(prop, this, parentVm) ? req(value) : true
  })
```
---
layout:     post
title:     reactHooks的使用
subtitle:  reactHooks的使用
date:       2019-11-21
author:     Teemo
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - 工具
    - node
---


### useState的使用
在reactHooks中useState代替了原本的state,
`const [age,setAge]=useState(18)`
定义 `age`的初始值为18
通过`setAge`修改`age`的值
```js
import React ,{ useState} from 'react'
function example(){
  const [age,setAge]=useState(18)
  return(
		<div>
			<p>我叫aaa,性别{sex}</p>
			<Button onClick={()=>setAge(age+1)}>年龄加</Button>
		</div>
	)
}
export default example
```
###useEffect的使用
在reactHoooks中如果需要使用生命周期，则要用到useEffect,
```js
import React ,{ useState,useEffect} from 'react'
function example(){
     const [age,setAge]=useState(18)
     useEffect(()=>{
		console.log(1111111)
	})
     return(
		<div>
			<p>我叫aaa,性别{sex}</p>
			<Button onClick={()=>setAge(age+1)}>年龄加</Button>
		</div>
	)
}
export default example
```
这里的useEffect相当于`componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 的组合体。
```js
 	useEffect(()=>{
		console.log(1111111)//组件挂载时触发
		return ()=>{
			console.log('aaaaaaaaaaa')//组件销毁时触发
		}
	},[])
```
useEffect 可以传第二个参数，一个数组（effect 更新依赖）如 `[age]`， 数组中任意元素的改变会触发组件更新， 如果传一个空数组， 则组件更新不会运行 hooks 函数， 仅仅在 mount 和 unmount 执行相应 hook。
### createContext和useContext数组间传参
```js
import React ,{ createContext,useContext} from 'react'
const countContext= createContext();
function Counter(){
	let count = useContext(countContext)
	return(<div>
		<h2>{count}</h2> //count的值显示为0
		</div>)
}
function Component1 (){
	const [cont,setCont]= useState(0)
	return (
			<countContext.Provider value={cont}>
				<Counter/>
			</countContext.Provider>
	)
}
export default Component1
```
当组件上层最近的 `<CountContext.Provider>` 更新时，该 Hook 会触发重渲染，并使用最新传递给 `CountContext provider` 的   `context value` 值。
### useReducer
```js
function Counter(){
	const [num,dispatch]= useReducer((state,action)=>{
		switch(action){
			case 'sub':
				return state - 1
			case 'add':
				return state + 1
			default:
				return state
		}
	},0)
	return(<div>
		<p>现在的分数是{num}</p>
		<Button onClick={()=>{dispatch('add')}}>加</Button><Button onClick={()=>{dispatch('sub')}}>减</Button>
		</div>)
}
```


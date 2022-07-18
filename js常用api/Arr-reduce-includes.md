```js
let arr = [1,3,4,1,4,6,8,9,6,5];
//pre在回调函数第一次执行时 是reduce第二个参数 也就是默认值 之后就是回调函数的返回值
//pre就是上次的返回值，但是在第一次时为我们的默认值（reduce第二个参数）、
//cur是当前值
//index是下标
//oldarr是原数组
let result = arr.reduce((pre,cur,index,oldarr)=>{
  if(pre.includes(cur) == false){
​    pre.push(cur)
  }
  return pre
},[])
```

这里用到了reduce 嵌套 includes来实现数组去重
给一个空数组给reduce为默认值 》每一次执行时判断这个数组里面有没有存在当前值/如果没有就添加/如果有那么就不加/
加和不加 都会return 出去这个数组 作为下一次判断 最后执行完毕以后 接收
**reduce不会修改原数组**

**总结：reduce也可以用来累加数值**


## 全局对象process

node 程序传递参数
比如 node的运行环境,参数信息等

```js
node index.js 这后面可以跟一些参数
```

我们可以打印一下 process

```
里面会有一个属性是argv 数组类型 里面存放了 node.exe的位置 以及我们通过命令行传递的参数
```

之后我们如果要使用的情况下

```
process.argv[下标] //因为是数组 下表对应我们的数据]
```

为什么叫argv
argv是 argument vecto的缩写 传递的具体参数
vector 翻译过来是矢量,在程序中表示的是一种数据结构

console.trace() 可以打印函数的调用栈

## 特殊全局对象

为什么称为特殊全局对象?
1~这些全局对象实际上是模块中的变量,只是每个模块都有,看起来像全局变量
2~**在命令行交互中是不可以使用的**
3~包括:__dirname , __filename , exports , module , require()

```js
// 输出当前绝对路径
console.log(__dirname)
// 输出当前绝对路径 加上当前文件的名称
console.log(__filename)
```

## 常见的全局对象

process对象

console对象 dir log trace等等 很多api可以去官网看特性

定时器函数 在node中有好几种定时器的方式
**计时器全局对象** setTimeout , setInterval , setImmediate , process.nextTick 
**global全局对象** 
类似于浏览器的window对象
process 就属于global的属性 , 所以可以全局获取,process属性其实被做了一层代理
也就是ObjectDefineProperty

## **javascript模块化**

### **CommonJs** 

它是一种规范,最初提出来的是在浏览器以外的地方使用,并且当时被命名为ServerJs
后来为了体现它的广泛性,修改为CommonJs,平时我们简称Cjs

node是CommonJs在服务器端的一个具有代表性的实现
Browserify 是 CommonJs在浏览器中的一种实现
webpack 打包工具具备对CommonJs的支持和转换 但是CommonJs在webpack中是另外一回事 , 它将我们的代码转换成浏览器可识别的代码

### CommonJs中的exports

```js
//CommonJs模块化
//导出数据

const name = '张三';
exports.name = name;
exports.age = 23;

//导入
const result = require('./js文件名') ///导入的时候写路径所在的地址以及自身名称 
//result 就是exports这个对象 所以可以使用这个对象的所属方法
------------------------------------
//知道这些后 我们就可以使用es6的结构赋值
//如果我们暴露的东西太多,在我们使用的时候不想用 对象属性的调用方式
const {name,age} = require('./js文件名')

//exports 最开始是一个全局空对象 
//require所做的事情就是想办法去拿到exports这个对象的内存地址 

```

**require所做的事情就是想办法去拿到exports这个对象的内存地址** 

### CommonJs中的module.exports

exports 和 module.exports 的区别

我们之所以 能够顺利的使用exports进行导入和导出 其实是 node的功劳
为了实现模块导出,Node中使用的是module类,每一个模块都是一个module实例,也就是module
所以在node中真正用于导出的其实根本不是exports,而是module.exports
因为module才是导出的真正实现者

```js
//源码内 module.exports是赋值的exports
负责导出的是 module.exports 
默认情况下 module.exports 是取的exports 的地址
所以我们给exports添加属性来导出时 在底层 module.exports 会对这个地址进行导出

结论:所以可以直接给module.exports = {} 来进行赋值 
如果这么做的 module.exports 和 exports就没有关联了 通过exports导出的数据 在导入时并不会取得 因为 此时module.exports的地址根本没有引用它

```

```js
//案例研究

因为一开始 exports这个对象地址就存在了
moedule.exports 和 exports默认是共用一个地址
如果我们在不修改moedule.exports 直接去更改exports的话
我们最终会取到一个空对象

//一定要注意!!!
//底层最终都是以module.exports为基准 导出
```

**总结:** exports和module.exports共用一个对象 但是底层逻辑在暴露的时候 其实是基于module.exports的值 所以 我们最开始给exports里面添加方式 module.exports也能使用 同时暴露 而且我们可以直接 给module.exports 赋值 来影响导出结果

### require细节

require是一个函数,可以帮助我们引入一个文件(或者模块) 中导入的对象
那么,require的查找规则可以看官网

情况1: require(x)  x是一个模块 比如http path等等 这种没有写地址的都是核心模块
核心模块就是 存放的node_modules里面的模块

情况2: 如果我们引入本地的模块 会写入字符串 添加地址 那么 分别代表的是
'./' 当前目录 ; '../'上层目录 ; '/' 根目录
如果文件没有后缀名的情况下
1>直接查找文件x 2> 直接查找 x.js文件 > 3查找 x.json文件 > 4 x.node文件

require调用以后会执行 对应的文件内的所有代码 它会执行引入模块内的所有代码 再执行自身调用处的文件内的代码

如果模块被多次引入,它会进行一个缓存,所以只会执行一次

```js
//a文件
exports.name = '张三'
console.log('a.js执行了')
//b文件
let result = require('./a')
console.log(result)

//终端
node b.js 
输出:
a.js执行了
{name:'张三'}	


```

## es module(es6模块化)

esmodule加载js文件的过程是编译时加载,并且是异步的
编译时(解析)时加载,意味着import不能和运行时相关的内容放在一起使用
export{里面传入导出的变量名称} 
注意export 不是一个对象 它是一个由js引擎来解析的 声明符 可以理解为 async await 这种字符

重点
我们在export导出的模块中 对值进行修改 , 导入的模块中 也会随之改变
不管是引用类型还是基本类型 只要export对导出的数据修改,那么导入的模块也会被更改

要想在node下 使用 esmodule  有两种办法
第一种是 设置package.json type设置module
第二种是 把使用esmodule的文件后缀名 改为mjs

## path模块

可以获取到文件的绝对路径,之所以要这么做, 主要是因为不同的系统对 '/'斜杠的解析不同
有的系统可能需要转义字符来识别 
使用path就避免了这个问题

path模块的其他方法

路径拼接

```js
const aurl = '/user/aa'
const burl = 'index.txt'

const filepath = path.join(aurl,burl)
console.log(filepath) // /user/aa/index.txt 它自动帮我们拼接了斜杠/

//reolve也可以实现同样效果 resolve要比join灵活一些 具体实现
path.resolve(aurl,burl)
//用的多的是path.resolve webpack很多用的就是path.resolve

```

path也有很多api 可以去官网看



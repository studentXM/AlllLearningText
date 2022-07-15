# webpack

一个构建工具,它可以把前端源代码转换成 在发布以后可以执行的html,css,js代码
代码转换:Ts可以转换成js  scss可以转换为css等
代码分割:提取多个页面的公共代码,提取首屏不需要执行的部分代码让其异步加载
模块合并:在采用模块化的项目里会有很多个模块和文件,需要构建功能把模块分类合并成一个文件
自动刷新:监听本地源代码的变化,自动重新构建,刷新浏览器
代码校验:在代码被提交到仓库前需要校验代码是否符合规范,以及单元测试是否通过
自动发布:更新完代码后,自动构建出线上发布代码并传输给发布系统
文件优化:压缩代码和文件 节约带宽和流量

```
   课程版本
   // "webpack": "^4.39.2",
    // "webpack-cli": "^3.3.7",
    // "webpack-dev-server": "^3.8.0"
    //当前最新版本
        "webpack": "^5.73.0",
    "webpack-cli": "^4.10.0",
    "webpack-dev-server": "^4.9.3"
```

构建其实是工程化,自动化思想在前端开发中的体现,把一系列代码流程用代码去实现,让代码自动化的执行这一列复杂的流程,构建给前端开发注入了更大的活力,就放了生产力

### webpack核心概念

entry:入口 webpack执行构建的第一步将从entry开始,可抽象成输入
module:模块 在webpack里一切皆模块,一个模块对应着一个文件,webpack会从配置的entry开始递归找出所有依赖的模块
chunk:代码块,一个chunk由多个模块组合而成,用于代码合并与分割
Loader:模块转换器,用于把模块原内容按照需求转换成新内容
plugin:扩展插件,在webpack构建流程中的特定时机注入扩展逻辑来改变构建结果 或 做你想要的事情
output:输出结果,在webpck经过一系列处理并得出最终想要的代码后输出结果

流程

```
webpack启动后会从entry 里配置的module 开始递归解析entry依赖的所有module,每找到一个module,就会根据配置的loader去找出对应转换规则,对module进行转换后,再解析出当前module依赖的module
这些模块会以entry为单位进行分组,一个entry和其所有依赖的module被分到一个组也就是一个chunk,最后webpack会把所有chunk转换成文件输出,把整个流程中webpack会在恰当的时机执行plugin定义的逻辑
```

安装

```
npm i webpack webpack-cli -D
```

在目录中新建一个webpack.config.js 配置文件 / 新建一个dist文件夹用来接收打包输出

```js
const path = require('path')
module.exports = {
    // 因为开发环境下和生产环境下的webpack配置有不一样的地方,这里用来区分
    mode:'development',
    entry:'./src/index.js',//入口文件 打包的时候会先从这个文件开始分析它的依赖
    output:{
            path:path.join(__dirname,'dist'), //输出的目录, 这里只能是绝对路径
            filename:'bundle.js' // 输出文件的名字
    }
}
```

在package.json里设置 打包脚本  (以后只需要npm run 脚本名 就可以执行)
这里其实执行的是 node_modules/bin/webpack

```json
{
  "name": "webpack-learn",
  "version": "1.0.0",
  "description": "",
  "main": "webpack.config.js",
  "scripts": {
    //更改了这里
    "build": "webpack" 
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^5.73.0",
    "webpack-cli": "^4.10.0"
  }
}

```

课程内 由于想要 保存以后 自动打包 所以会安装一个模块(其实就是 vue 开发环境下 的 本地服务器 npm run server)


```
npm i webpack-dev-server -D
```

要执行这个模块 需要七档 node_modules/bin/webpack-dev-server
**所以我又需要配置一个脚本**

```json
  "scripts": {
    "build": "webpack",
    "dev":"webpack-dev-server"
  },
```



由于当前使用了 dev脚本后 显示的是一些目录
所以我们再webpack.config.js配置文件中 写一个 devServer对象

```js
    //  和output同级
    devServer:{
        // 内容的基本路径
        contentBase:path.join(__dirname,'dist')//这是产出文件的目录
    }
```

npm run dev 的时候 会启动一个http服务器 我们打开之后 它就默认打开了我们配置的 路径
它默认会打开里面的 index.html

devServer也可以更改的 端口,主机名,compress(布尔值,是否压缩)

完成以上配置以后,我们在开发当中,**修改代码后** **> webpack就会重新打包 > 通知浏览器刷新** 

----

分节
假如我们index.js里面引入了其他js文件

```js
import './hello1'
import './hello2'
```

hello1.js和hello2.js

```js
//hello1
document.write('hello11')
//hello2
document.write('hello2')
```

最后我们在更改 非index.js文件的其他文件时 浏览器也会刷新 
**例子: a.js 和 b.js互不干扰 它们只是被引入到了index.js作展现**
b.js有很多表单数据,那么我们在修改a.js数据时 就会刷新浏览器 那么b.js的表单数据就全部丢失了
**目标:我们想要的就是 谁被修改了 就更新谁 而不是全部更新 这种功能叫做热更新**

----

加载css文件

```js
import './index.css'
```

我们再npm run dev时 就会报错 因为 它把css文件当成js去解析了
这个时候就需要安装 loader来解析了 css 需要css-loader 和 style-loader
这里版本有讲究 我为了和课程走
使用到的是

```json
    "css-loader": "^3.4.2",
    "style-loader": "^1.1.3",
    "webpack": "^4.39.2",
    "webpack-cli": "^3.3.7",
    "webpack-dev-server": "^3.8.0"
```

安装完成后 我们需要配置loader
在webpack配置文件中,写入一个module对象/dules数组/每个loader一个对象
这里的test会使用正则检测 文件的后缀名 如果检测成功那么就会使用 use里面的loader来解析
该类型只有一个loader的情况下 只需要写一个字符串就行了 如果1个以上 的话 需要写到一个数组里
这里的解析过程是从右往左 先解析css-loader,再解析style-loader 所以我们写的顺序要 
这里的顺序 必须按照这么写

```js
module:{
        rules:[
            {
                test:/\.css$/,//如果要加载require或import文件时css文件的话
                // 会从右向左处理css文件,loader时函数
                use:['style-loader','css-loader']
            }
        ]
    }
```


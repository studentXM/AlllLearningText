## 插件-68

规范:在src下写一个plugin.js文件 里面暴露对象 然后在**min.js里面 使用Vue.use来使用暴露的插件对象**,vue会自动帮我们调用这个插件里面的函数 (这个函数的第二个参数是 Vue的构造函数)

## scoped-69

**scoped(局部) 给style标签添加scoped代表局部生效**

**作用:让样式在局部生效,防止冲突**

原理:是给标签动态生成标签属性 style里再通过标签属性获取

注意 : 不要在app.vue里写 scoped 这样它就无法公共样式了(一般来说app.vue的样式是公共的)

## toDoList-70

**组件化编码流程**(通用)
1~实现静态组件:抽取组件,使用组件实现静态页面效果
2~展示动态数据:
					a:数据的类型,名称是什么?
					b:数据保存在哪个组件?
3~交互---从绑定事件监听开始

当我们点击编辑的时候 会出现input输入框,并且要自动聚焦,这里我们使用自定义指令 

directives: {

  focus: {

   // 指令的定义

   inserted: (*el*) => {

​    // 聚焦元素

​    *el*.querySelector('input').focus()

​    *el*.focus()

   }

  }

 },
注意,它会在元素产生的时候就执行 所以如果使用了 v-show 要换成v-if

**todolist保存勾选值**
有两种方式 :
1,使用element-ui 自带的保存方式 暂时没有学会
2,直接深度监听对象里面的布尔值,然后在选项表格里 使用template模板标签覆盖element选项表格
template 使用slot-scope='scope' 标签内写入checkbox 双向绑定scope.row.对应的布尔值
这个scope代表的是这一行的数据

## 组件自定义事件-80

props 也可以通过传递函数 来实现 子组件传递数据给父组件

**$emit** 方式
组件上声明一个事件名称 v-on:事件名(@事件名) 在子组件中 通过this.$emit('事件名',传递值) 这种方式也可以实现子传父

**ref** 方式
组件上声明ref 然后 通过this.$refs.名称.$on('事件名',回调函数)绑定事件名
**v-on和$refs.名称.$on都可以绑定事件 这两种方式都是通过子组件emit发射(个人理解是调用)**

**解绑自定义事件**
this.$off('事件名称') 解绑一个自定义事件
this.$off()解绑所有自定义事件
this.$destroy() 通过销毁当前组件实例,也会销毁到自定义事件(因为绑定到了组件的实例身上)

**组件原生事件** 
组件标签 使用原生事件 比如@click 等事件 会被当成自定义事件来使用 如果要在组件标签上使用原生事件,那么则需要在事件后面加上.native(@click.native)

## 全局事件总线-84

父传子用到了自定义事件传输  父组件内 给子组件绑定自定义事件名称 子组件内 调用事件 传递参数
父组件内就可以触发事件回调 得到值
如果兄弟组件或者其他关系的组件们进行传输 得可以用事件总线
直接给main.js 里面的 Vue构造函数中配置对象添加 
beforeCreate(){

  // 安装全局事件总线

  Vue.prototype.$bus = *this*

 }
**原理:还是用到的自定义事件 只不过我们给Vue原型上绑定了 vue实例 之后我们每一个组件都能拿到这个组件进行绑定自定义事件 也可以进行调用自定义事件发送数据**

## $nextTick-90

this.$nextTick(function) 回调会在下一次dom节点更新完毕之后执行 
**这也是一个特殊的生命周期函数**

## 动画效果

**原生css3 的一些动画**
@keyframes 动画名{
from{//这里面写开始的样式} 
to{这里写结束的样式} 
}
在一个样式里面写入 animation:动画名, 时间 ,顺序 来开始动画样式

写一个transition标签需要过渡动画的标签 transition标签可以给一个name值来代表局部生效
这是给了name值transiiton标签的 样式  注意:要在需要动画的标签 写过渡样式(transition)

## slot-102

### 1~默认插槽

想要在一个组件内插入一个标签 需要在这个组件内部用一个slot插槽标签来占位
这样父组件就能给子组件添加标签
slot标签和其他标签一样使用 如果在使用组件的时候 传入了标签或者其他组件,那么slot会被覆盖,他相当于就是对自己组件标签的一个占位标签,但是当自己组件在外部被使用并且传入了标签(或组件时)slot自己会被覆盖

**总结:**
组件标签在被使用时 又想给组件内部添加标签 那么在组件使用之前给内部 写一个slot 外部在使用时就可以给组件标签内写标签了

### 2~具名插槽

slot标签给一个name属性 然后给一个值
外部在使用组件标签时 插入的元素 需要给一个slot="slot的name值" 这样就代表往对应name值的插槽插入
**template插入**
template标签在渲染后会舍弃自身保留内部的元素 它就是一个用来包裹的外壳,template可以用来插入slot这样也不会生成多的标签
**template v-slot**: slot名 这是一个新的写法 和 slot="名称" 一样是vue2.6更新的写法

### 3~作用域插槽

**我们给组件插入元素时 如果要用到这个插槽组件内的数据** 可以用这种方式

<slot :数据名称="变量"></slot>
**这个插槽传递的数据就给到了 插槽的使用者 外部在插入插槽的时候就可以用了**

a组件内使用template标签的scope来接收值 scope="自定义一个名称"
那么这个templat内的标签就能用到传递过来的数据了 这种方式的插槽是必须要template标签的

知识插入:

```jade
function test([a,b]){
	console.log(a,b)
}
function test2(a,b){
	console.log(a,b)
}
test(['key','value'],'done')//key,value
test2(['key','value'],'done')//['key','value'],'done'
形参内写方括号 优先用来结构实参里的数组
```

## vuex-105

vuex 专门在vue中实现**集中式状态(数据)管理**的一个vue**插件** 对vue应用中多个组件的共享状态进行集中式管理(读/写),也是一种组件间通信方式,且适应与任意组件间通信

**vuex有三个重要的组成部分(  Actions(动作)/ Muations(修改)/ Sate(状态) )**
state是一个对象

**第一步调用 dispatch('动作',值)**  >  Actions(这个对象会以key value的形式接受dispatch的参数)
**actions对象内 的一个函数属性的key若是和dispatch的** 
第一个参数相同 那么则会调用 然后这个函数会有一个值 这个值 就是dispatch的第二个参数  
**这个函数通过调用 context(第一个形参).commit('下一步的key',值) 进入 Mutations**
Mutations写一个函数接收上一步的key 就可以调用了  
**这个函数内就可以对state内的数据进行修改**  

如果我们要修改一个值 同时需要调用后台接口发送请求时 就可以在actions里面做
所以它也可以不经过actions 直接调用Mutations 来进行修改

<img src="./vuex.png" style="zoom: 50%;" />

**actions的作用在于 在修改数据之前 做一些处理 比如需要判断 最重要的是 向后台发请求**
**若我们不需要做一些判断 也不需要对这个数据发请求 那么可以直接通过 Mutations进行修改**

actions > mutations > state 这三个都是对象 它们都得被store管理

## vuex环境

1~npm i vuex 安装
2~Vue.use(Vuex)
3~store
4~需要让所有组件都能使用

**注意**: vue默认版本为3 那么安装vuex 默认版本就为4 vuex4 只能在vue3中使用	
		vue2中 需要使用vuex3版本

**当我们安装vuex 并且 引入后 还需要 Vue.use 这个vuex**
**然后我们就能在vm的配置对象上写一个store对象**

**store配置**:

在src下新建一个vuex 写一个 store.js
**官方建议:在src下新建一个store文件夹 / index.js**

#### 问题!!:

import 会有位置提升的问题 它优先级高于其他代码 所有import语句会按编写顺序执行 但是它的顺序高于非import语句
比如 

```js
vue.use(vuex)
import vuex from "vuex"  //这一句先执行


```

### 正确使用

**在store/index.js里面 引入Vue,引入Vuex, 再使用Vue.use使用vuex**

```js
import Vuex from 'vuex'
import Vue from 'vue'
Vue.use(Vuex)
// 该文件用与创建vuex中最核心的store
// store用来管理 vuex的三个组成部分(actions/mutations/state)
// actions 用来响应组件中的动作
const actions = {
    add(context,value){
        // value是传递的值 context是上下文对象
        // 上下文对象可以理解为 vue开发者把 使用者可能用到的一些api封装成了一个对象给我们使用
        // 它内部包含了 commit
        console.log(value)
        context.commit('ADD',value)
    }
}
// mutations 用于操作数据(state)
const mutations = {
    ADD(state,value){
        console.log(state.num,value)
        state.num += value
    }
}
// state 用于存储数据
const state = {
    num:0
}
// new Vuex身上的Store 创建一个实例
const store = new Vuex.Store({
    actions,
    mutations,
    state
})
// 暴露store
export default store
```

有业务逻辑的时候 可以通过*this*.$store.dispatch() 调用 进入 vuex的actions 里操作
如果不需要进入actions 那么可以直接调用*this*.$store.commit() 进行修改
在actions内的上下文对象中还保留着dispatch 这是为了处理业务逻辑复杂的时候 进行步骤操作
比如actions里面的a对象 处理完了 再调用dispatch 使用actions里面的b函数 以此内推 直到调用commit

### getters  也是Vuex.Store的配置项

getters里面写入函数 函数内部可以拿到 state中的值 这个函数可以对state的数据加工(相当于computed) 可以return出去

### vuex开发者工具的使用-**110**

#### mapState 和 mapGetters 在computed里生成函数 

之所以使用展开运算符是因为 它是一个对象  里面包含了函数

mapState映射state的数据 mapGetters映射getters的数据

**当我们的vue中 写了很多 重复的 this.$store.state时 我们可以使用mapState来进行使用**
map就是来帮我们解决重复代码的

在需要mapState的组件内 引入 import {mapState} from  'vuex'
mapState({ key名:' state里的数据' })

当我们使用时 只需要在需要的地方 ...mapState({ key名:' 函数名' }) 展开运算符
**mapState数组写法**
...mapState([ 'state里的数据' ]) 这种写法必须 这个函数名和state里的数据名是一致的

**mapGetters和mapState的写法一致** 只不过它映射的是 getters里的数据

#### mapMutations 和 mapActions 可以在methods里生成函数

其实这两个对象就是帮我们调用 dispatch 和 commit 

```js
    // 组件内
    ...mapMutations({
      changemessage: "change",
    }),
    在调用changemessage函数的时候可以通过函数调用传值
    // index.js内
    const mutations = {
    change(state,value){
        state.message = value
    }
}
    
```

### vuex模块化

当很多数据都需要使用到vuex的时候 Store配置对象庞大不易维护
所以把不同业务逻辑或者板块 区分成模块 方便维护

每一个模块当中都是一个对象
例如:
这里面包含了 vuex的所有配置对象

```js
const countOptions = {
    //代表可以通过名称进行声明查找 声明后方便使用   详情看组件内使用
    namespaced:true,
    actions: {
        add(context, value) {
            // value是传递的值 context是上下文对象
            // 上下文对象可以理解为 vue开发者把 使用者可能用到的一些api封装成了一个对象给我们使用
            // 它内部包含了 commit
            console.log(value)
            context.commit('ADD', value)
        }
    },
    mutations: {
        ADD(state, value) {
            // console.log(state.num,value)
            state.num += value
        },
        change(state, value) {
            state.message = value
        },
    },
    state: {
        num: 0,
        message: '测试文本',
    },
    getters: {},
}
```

然后在插入配置对象当中的modules中:

```js
const store = new Vuex.Store({
    modules:{
        //countAbout用来在外部使用时 声明范围 前提是 namespaced = true
        countAbout:countOptions,
    }
})
```

组件内使用时:

```js
  computed: {
    ...mapState('countAbout',["num", "message"]),
  },
  //也可以使用对象模式,也是在第一个参数声明 模块对象的名称 countAbout代表 Store配置对象中modules里的对象
  // 前提是 namespaced = true 而且modules里存在这个对象
```

**特殊简写方式**(非map方式 的 模块简写)

```js
        addlist(){
            const personObj = {id:nanoid(),name:this.pname};
		   //因为这里的persnObj 需要用到一些特殊的值 不能通过模板语法中 函数调用传值
		   // 这种方式也方便了使用 
            this.$store.commit('personAbout/AddPerson',personObj)
        },
```

## 路由-117

路由属于vue的插件库,专门实现spa(单页面)应用
**安装**
vue-router默认版本为4(只能在vue3中使用)
所以在vue2中 我们要安装router4以下版本 

```js
//vue2下
npm i vue-router@3
```

在src下 新建一个router文件夹/index.js
path代表之后浏览器的路径指向
componen代表对应路径指向显示的组件

```js
import VueRouter from 'vue-router'
import About from '../components/MyAbout'
import Home from '../components/MyHome'
const router = new VueRouter({
    routes:[
        {
            path:'/about',
            component:About
        },
        {
            path:'/home',
            component:Home
        },
    ]
}) 
export default router
```

组件中

**router-link**

router-link是锚点标签  最终编译到html上时其实也是**a标签**   **点击之后** 就会切换当前的路由到 '/about' 
**然后显示对应路由的组件**

active-calss 当指定路由被激活时 它就会添加一个class名
例如: active-class="active" 当这个router-link标签对应的路由被激活时,那么就会添加active样式反之删除

### **router-link**  中 replace属性

作用:控制路由跳转时操作浏览器**历史记录的模式**
浏览器的历史记录 两种写入方式:**push,replace** 
push是追加历史记录,replace是**替换当前记录**,路由跳转时默认为push
如果在router-link标签 添加了 replace(布尔值为**true**开启) 属性 那么 这个路由在跳转时 会**替换掉当前路由** 

### **router-view**

显示组件的位置 就在router-view里  **router-view决定了路由显示的位置**

```vue
<router-link to="/about">about</router-link>
<router-view></router-view>
```

**路由组件和一般组件的区分文件夹**

pages用来存放路由会使用的组件
components用来存放一般组件(不被路由直接显示的组件)

当我们切换路由时,上一个路由的组件会被销毁(可以使用组件的销毁生命周期函数进行验证(beforeDestroy))

$route 是当前路由的 上下文配置对象 比如当前是home 那么它就是 home路由的上下文配置对象(里面还有更多的api 所以为上下文配置对象)

$router 整个路由对象 它包含了 所有配置对象-
每个组件都有自己的$route属性,里面存储着自己的路由信息,整个应用只有一个router

### 嵌套路由

在一个需要子路由的配置对象中写入一个children数组

```js
    routes:[
        {
            path:'/about',
            component:About
        },
        {
            path:'/home',
            component:Home,
            children:[
                {
                    //这里不需要写/
                    path:'show',
                    component:()=> import('../components/MyChildren.vue')
                },
            ]
        },
    ]
```

然后在这个父路由组件里 写 router-link(锚点) 以及router-view

router-view 的显示是由 router配置对象里面的结构决定的

比如a组件下 锚点点击了a/xx路由  那么a的router-view就会显示xx组件
如果a组件锚点点击了 a/yy/yychild 那么a组件则会显示yy组件 yy组件由于感应到了路由也会显示yychild路由  **需要注意的是 yy一定要有router-view 否则不会显示yychild**

```vue
<router-link to="/home/show">show</router-link>
```

```vue
//home 组件要用router-view占位
<router-view></router-view> 
```

**路由嵌套时 嵌套路由内的router-link标签需要写完整路径 从跟路径开始 /**

## 路由的query参数

query参数: 浏览器url拼接的参数 就是query参数

例如:url?id=1&name:张三
我们在对应路由的组件内 通过 this.$route.query可以拿到这些参数(对象形式)
this.route还有其他对象

```vue
<router-link :to="`/home/show/showchildt?id=${oid}&title=${showanme}`">显示detail</router-link>
```

router-link to的对象写法 (参数比较多的时候 可以以这种标准方式)

```vue
    <router-link
      :to="{
        path: '/home/show/showchildt',
        query: {
          id: oid,
          title: showanme,
        },
      }"
    >
      显示detail</router-link>
```

#### 命名路由

当路由层级过多 比如三级路由 就需要这种方式
**给配置对象一个name属性**
这样的好处是 把原来的**path换成**了 **name属性内的值** 原本的path路径由于需要写成完整路径 后期可能会很长 

```js
//路由配置 js文件内
children:[
                        {
                            name:'showchildt',
                            path:'showchildt',
                            component:()=> import('../components/MyTest.vue')
                        }
                    ]

```

```vue
//组件内
    <router-link
      :to="{
        name: 'showchildt',
        query: {
          id: oid,
          title: showanme,
        },
      }"
    >
      显示detail</router-link>
```

## 路由的params参数

在路由的index.js 路由配置对象path中拼接占位符 

原本的query换成params

我们在取得这些value的时候 也需要使用 this.$route.params

#### !!!!!使用parms参数时,若使用to的对象写法,必须使用name 来代替path!!!!

```js
children:[
                        {
                            name:'showchildt',
                            //路径后面用斜杠分割 保留一个key用来占位 key前面带有冒号
                            path:'showchildt/:id/:title',
                            component:()=> import('../components/MyTest.vue')
                        }
                    ]
```

```vue
//组件内   
<router-link
      :to="{
        name: 'showchildt',
        params: {
          id: oid,
          title: showanme,
        },
      }"
    >显示detail</router-link>
```

## 路由的props配置

当我们使用 路由query 或者 params参数时 会使用 $route.query/$route.params 这样会很繁琐

### 第一种方式(普通对象)

#### 路由配置中

```js
                    children:[
                        {
                            name:'showchildt',
                            path:'showchildt/:id/:title',
                            component:()=> import('../components/MyTest.vue'),
                            // 第一种写法,值为对象,该对象的所有key/value都会以props的形式传给showchildt组件
                            props:{a:'你好威整天',b:'你好我是大黄蜂'}
                        }
                    ]
```

#### 组件内 常规使用props接收参数

```vue
<template>
  <div>
        <h2>路由prop{{a}}</h2> <h2>路由prop{{b}}</h2>
  </div>
</template>
<script>
export default {
    props:['a','b'],
}
```

### 第二种方式(params)

在需要传值的路由配置对象中 写入props:true  之后对应的路由组件就可以以props的方式 接收对应key的params 数据(这种方式仅限params)

```js
                    children:[
                        {
                            name:'showchildt',
                            path:'showchildt/:id/:title',
                            component:()=> import('../components/MyTest.vue'),
//第二种写法,值为布尔值,为true时,就会把该路由的组件的params参数以props的形式给当前组件
                             props:true,
                        }
                    ]
```

```vue
<template>
  <div>
        <h2>路由prop{{a}}</h2> <h2>路由prop{{b}}</h2>
  </div>
</template>
<script>
export default {
    props:['id','title'],
}
```

### 第三种方式(query)

```js
                    children:[
                        {
                            name:'showchildt',
                            path:'showchildt/:id/:title',
                            component:()=> import('../components/MyTest.vue'),
//第三种写法 函数 这是一个回调函数 我们会接受到$route对象 可以把 query里的数据获取到然后返回给对应组件 
                            props($route){
                                return {id:$route.query.id,title:$route.query.title}
                            }
                        }
                    ]
```





## 编程式路由导航

this.**$router**.push({name:'showchildt' }) push里面的对象 就是router-link to属性中的对象
this.$router.replace() 用法一致 但是具有replace特性 

$router.forward() 前进
$router.back() 后退
$router.go(数值) 传递数值 根据正负 来进行前进或后退

### 缓存组件

给需要缓存的组件 包裹一个 keep-alive标签
如果 keep-alive包裹的是 一个动态的路由组件(router-view)
那么 keep-alive 可以给一个include="组件名" 属性 如果指定缓存的组件数量 >1 那么可以给include一个数组	

## 特殊生命周期(路由)

这是写在组件内的 不是路由守卫
**路由激活对应组件**时 该路由的**activated(){}**函数触发
**当前路由离开**时 调用该路由的**deactivated(){}**函数
还有一个特殊的生命周期函数是 nextTick

# **路由守卫**

 可以**简单的理解**为一座房子的门口的保安 判断成功那么就允许跳转路由

## 全局路由守卫

#### 初始化的时候被调用,每次路由切换之前被调用

#### **前置路由守卫 befforeEach** VueRouter实例.befforeEach( ( to,from,next )=>{   } )

// 给一个回调函数 回调函数中有三个参数 to , from , next
to 表示跳转到的目的 路径
from 触发路径 
next 是一个函数 调用后 才可以成功跳转到目的路径 如果不调用是 不能跳转到目的路径的

因为这是全局路由守卫 所以它会守卫所有路由的跳转
我们可以给一个路由一个**验证来判断是否需要**进入守卫判断
但是不能在路由配置对象里随便写数据 它只会保留预设配置项 所以我们给他**写到meta对象中**

路由配置对象meta对象中可以写入我们自定义的一些数据

####  **后置路由守卫 afterEach**

#### 切换成功后会调用 

to , from 两个参数 没有next 

## 独享路由守卫(某一个路由独享)

**单独**一个路由的守卫 独享路由守卫只有**一个 beforeEnter 守卫**

**beforeEnter:function**   和beforeEach一样也是三个参数 to from next
但是它需要写在一个单独**路由配置对象**当中
以key是beforeEnter 值是function 函数内的参数和beforeEach 一致

## 组件内路由守卫(只在组件内部调用)

注意不是router 是route

  进入某个路由之前调用它内部的 **beforeRouteEnter** 函数
组件内的路由守卫只有**激活路由之前** 和 **失效路由时** 
  **beforeRouteEnter**:function
  参数:还是三个 to from next 

 离开当前路由时 **beforeRouterLeave()** 

## 路由模式

hash 和 history 
**hash** 
这里的路由hash值 指的是 浏览器url #后面的值 #后面的值是不会被服务器解析的
比如 https://www.baidu.com/#/123 #后面的值就不会被解析 无论多长

**history** 它是以历史记录为原理 
两种路由模式的问题
推荐使用hash 
history 模式 打包上线后 运行时 会出现url出错
服务器是返回的一个html给用户的浏览器 然后呈现我们的项目
然后它 点击了项目当中的一些路由 显示了一些组件 这个时候 浏览器的url是会发生改变的
然后当用户一刷新 浏览器的url就会去请求当前的地址 但是当前的url地址是加了很多路由路径的 会错把路由路径当作 资源路径去请求

**总结:**
**history 模式 项目打包上线以后 浏览器会错把路由路径当作请求路径去发请求 会报错**

**hash模式 若以后将地址通过第三方手机app分享,若app校验严格,则会被标记不合法地址**(因为#)

解决模式:
使用hash 因为浏览器hash地址的特性 它并不会被解析成资源路径 
配合后端,或者使用nodejs 的一些库
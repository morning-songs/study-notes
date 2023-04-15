### MVC 

> 全名 model view controler  模型  视图  控制器 

模型 : 用于处理数据 存取数据

视图: 依据模型数据创建的视图

控制器: 应用程序中处理交互的部分 负责从视图中读取数据,向模型发送数据 也负责把模型数据渲染到视图 

### MVVM

> 全名 model view  modelView 模型 视图 vm实例

vm负责 模型数据渲染到视图 视图转化为模型数据 达到数据的双向绑定 ( 实现的方式是 dom事件监听 )

MVVM的思想是 视图和数据完全剥离,低耦合模式可以提高代码的复用性,全权由vm进行操作.

但vue没有完全遵循 MVVM,是因为MVVM中 view和model不能直接通信,但vue中可以通过 ref 控制dom ,违反了这一规定.



### data是一个函数的原因

当一个组件被复用时,内部return option配置项,如果data是一个对象,那么则指向同一个内存地址, 这样会导致修改data数据牵一发而动全身. 使用函数return 每次都会得到一个全新的对象,给每个组件实例创建一个私有的数据空间,让各个组件实例维护各自的数据 

### Vue通信方式有哪些

- props 父 传给 子
- emit 子 传给 父
- provide inject 爷传给孙
- eventBus 全局事件总线  任意组件互传
- vuex 任意组件 
- ref 父获取子
- ...... 

### vue2响应式数据的原理

整体思路是	`数据劫持 + 观察者模式 `	

对象内部通过 `defineReactive`方法,使用OBject.defineProperty将属性进行劫持,数组则通过重写数组来实现

get里面会做依赖搜集（dep[watcher, watcher]） set里面会做数据更新（notify，通知watcher更新）

- Object.defineProperty 数据劫持
- 使用getter收集依赖,setter通知 watcher派发更新
- watcher 发布订阅模式

### vue3响应式原理

Vue3.x改用 proxy 替代Object.defineProperty. proxy可以监听对象和数组的变化,并且多达13种拦截方法 

### Proxy 与 Object.defineProperty 优劣对比

**Proxy 的优势如下:**

- Proxy 可以直接监听对象而非属性；

- Proxy 可以直接监听数组的变化；

- Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的；

- Proxy 返回的是一个新对象,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改；

- Proxy 作为新标准将受到浏览器厂商重点持续的性能优化，也就是传说中的新标准的性能红利；

**Object.defineProperty 的优势如下:**

- 兼容性好，支持 IE9，而 Proxy 的存在浏览器兼容性问题,而且无法用 polyfill 磨平，因此 Vue 的作者才声明需要等到下个大版本( 3.0 )才能用 Proxy 重写。
- defineProperty API 的局限性最大原因是**它只能针对单例属性做监听**



### Vue 怎么用 vm.$set() 解决对象新增属性不能响应的问题 

```js
//源码 
export function set (target: Array<any> | Object, key: any, val: any): any {
  // target 为数组  
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 修改数组的长度, 避免索引>数组长度导致splcie()执行有误
    target.length = Math.max(target.length, key)
    // 利用数组的splice变异方法触发响应式  
    target.splice(key, 1, val)
    return val
  }
  // key 已经存在，直接修改属性值  
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  const ob = (target: any).__ob__
  // target 本身就不是响应式数据, 直接赋值
  if (!ob) {
    target[key] = val
    return val
  }
  // 对属性进行响应式处理
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}

```

- 如果目标是数组，直接使用数组的 splice 方法触发相应式；

- 如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用   defineReactive 方法进行响应式处理（ defineReactive 方法就是  Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）

### 单向数据绑定

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/12/7/16ede2ff5a75589d~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

### 双向数据绑定

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/12/7/16ede3f4bc83638a~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

往底层了挖请看这 : [你能写一个Vue的双向数据绑定吗](https://juejin.cn/post/6844903589278646285#comment)  或者 [0 到 1掌握: Vue核心之数据双向绑定 ](https://juejin.cn/post/6844903903822086151)



### Vue的父子组件生命周期钩子函数执行顺序

- **加载渲染过程**

​		父beforeCreate => 父created => 父deforeMounted => 子beforeCreate=> 子created=>子beforeMounted => 子mounted => 父mounted

- **子组件更新过程**

​		父beforeUpdate=> 子deforeUpdate=>子updated=>父updated

- **父组件更新过程**

​		父beforeUpdate=>父updated

- **销毁过程**

​		父beforeUnMount=> 子beforeUnMount=>子unMounted=>父unMounted

### 虚拟DOM 

- 虚拟dom就是用js对象来描述真实Dom，是对真实Dom的抽象
- 由于直接操作Dom性能低，但是js层的操作效率高，可以将Dom操作转化成对象操作。最终通过diff算法比对差异进行更新Dom
- 虚拟Dom不依赖真实平台环境，可以实现跨平台

**实现原理**

- 用 JavaScript 对象模拟真实 DOM 树，对真实 DOM 进行抽象；
- diff 算法 — 比较两棵虚拟 DOM 树的差异；
- pach 算法 — 将两个虚拟 DOM 对象的差异应用到真正的 DOM 树。

### diff 算法了解吗 

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e3c68d1b0884d9ca0f8ffc5ee64a28e~tplv-k3u1fbpfcp-watermark.image)





### v-for为什么要加key

如果不使用key，Vue会使用一种最大限度减少动态元素并且尽可能的尝试`就地修改/复用`相同类型元素的算法( 说白了就是相同节点复用 更新节点修改 )。key 是为Vue中Vnode的唯一标识，通过这个key，我们的diff操作可以更准确、更快速。

### Vue-router路由钩子函数是什么,执行顺序是什么?

> 钩子函数种类: 全局守卫, 路由守卫, 组件守卫

```
导航被触发。
在失活的组件里调用 beforeRouteLeave 守卫。
调用全局的 beforeEach 守卫。
在重用的组件里调用 beforeRouteUpdate 守卫。
在路由配置里调用 beforeEnter。
解析异步路由组件。
在被激活的组件里调用 beforeRouteEnter。
调用全局的 beforeResolve 守卫。
导航被确认。
调用全局的 afterEach 钩子。
触发 DOM 更新。
调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6f72fd5c28a54767b1892ebf9c307653~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

### 谈一谈vuex

vuex 是专门为 vue 提供的全局状态管理系统，用于多个组件中数据共享、数据缓存等。（无法持久化、内部内心原理是通过创造一个全局实例）

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cb545e2edc0a4dcb94a412db0625799c~tplv-k3u1fbpfcp-watermark.image)

主要包含以下模块

```
State:定义了应用状态的数据结构，可以在这里设置默认的初始化状态。
Getter:允许组件从Store中获取数据
Mutation:是唯一更改 store 中状态的方法，且必须是同步函数。
Action:用于提交 mutation，而不是直接变更状态，可以包含任意异步请求。
Module:允许将单一的 Store 拆分更多个 store 且同时保存在单一的状态树中
```

#### Vuex 页面刷新数据丢失怎么解决？

需要做 vuex 数据持久化，一般使用本地储存的方案来保存数据，可以自己设计存储方案，也可以使用第三方插件。

#### Vuex 为什么要分模块并且加命名空间？

**模块**： 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能会变得相当臃肿。为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块。

**命名空间**： 默认情况下，模块内部的 action、mutation、getter是注册在全局命名空间的 --- 这样使得多个模块能够对同一 mutation 或 action 做出响应。如果希望你的模块具有更高的封装度和复用性，你可以通过添加 namespaced:true 的方式使其成为带命名的模块。当模块被注册后，他所有 getter、action、及 mutation 都会自动根据模块注册的路径调整命名。

### Vue中的性能优化

- 对象层级不要过深
- 不需要响应的数据可以不放在data中
- v-if v-show区别场景使用
- computed watch区别场景使用
- v-for 遍历必须加 key，key最好是id值，且避免同时使用 v-if
- 大数据列表和表格性能优化  [解决方案参考](https://blog.csdn.net/weixin_39727976/article/details/111860887)

- 防止内部泄露，组件销毁后把全局变量和时间销毁

- 图片懒加载

- 路由懒加载

- 异步路由

- 第三方插件的按需加载

- 适当采用 keep-alive 缓存组件

- 防抖、节流的运用

  

### nextTick 使用场景和原理

[Vue系列之面试官请问NextTick是想考察什么](https://juejin.cn/post/7069702323173326885#comment)

### keep-alive 使用场景和原理

keep-alive 是 Vue 内置的一个组件，可以实现组件缓存，当组件切换时不会对当前组件进行卸载。

- 常用的两个属性 include/exclude，允许组件有条件的进行缓存。
- 两个生命周期 activated/deactivated，用来得知当前组件是否处理活跃状态。
- keep-alive 运用了 LRU 算法，选择最近最久未使用的组件予以淘汰。

扩展补充：LRU 算法是什么？

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa4a49bd77234467a53cd3b62c5bd135~tplv-k3u1fbpfcp-watermark.image)



### Vue 修饰符有哪些？

**事件修饰符**

- .stop 阻止事件继续传播
- .prevent 阻止标签默认行为
- .capture 使用事件捕获模式，即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理
- .self 只当在 event.target 是当前元素自身时触发处理函数
- .once 事件只会触发一次
- .passive 告诉浏览器你不想阻止事件的默认行为

**v-model 的修饰符**

- .lazy 通过这个修饰符，转变为在 change 事件再同步
- .number 自动将用户输入值转化为数值类型
- .trim 自动过滤用户输入的收尾空格

**键盘事件修饰符**

- .enter
- .tab
- .delete (捕获“删除”和“退格”键)
- .esc
- .space
- .up
- .down
- .left
- .right

**系统修饰符**

- .ctrl
- .alt
- .shift
- .meta

**鼠标按钮修饰符**

- .left
- .right
- .middle

### Vue 模板编译原理

Vue 的编译过程就是将 template 转化为 render 函数的过程，分为以下三步：
第一步是将 模板字符串转换成 element ASTs（解析器）
第二步是对 AST 进行静态节点标记，主要用来做虚拟 DOM 的渲染优化（优化器）
第三步是 使用element ASTs 生成 render 函数代码字符串（代码生成器）

### 谈谈你对 Vue 生命周期的理解？

| 生命周期                    | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| beforeCreate                | 组件实例被创建之初，组件的属性生效之前                       |
| created                     | 组件实例已经完全创建，属性也绑定，但真实 dom 还没有生成，$el 还不可用 |
| beforeMount                 | 在挂载开始之前被调用：相关的 render 函数首次被调用           |
| mounted                     | el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子    |
| beforeUpdate                | 组件数据更新之前调用，发生在虚拟 DOM 打补丁之前              |
| update                      | 组件数据更新之后                                             |
| activited                   | keep-alive 专属，组件被激活时调用                            |
| deactivated                 | keep-alive 专属，组件被销毁时调用                            |
| beforeDestory/beforeUnmount | 组件销毁前调用                                               |
| destoryed/unmounted         | 组件销毁后调用                                               |




### 生命周期钩子实现原理

Vue 的生命周期钩子核心实现是利用发布订阅模式先把用户传入的生命周期钩子订阅好（内部采用数组的方法存储）然后在创建组件实例的过程中会一次执行对应的钩子方法（发布）

### 谈谈对组件的理解

- 组件化开发能大幅提高应用开发效率、测试性、复用性
- 常用的组件化技术：属性、自定义事件、插槽
- 降低更新范围，值重新渲染变化的组件
- 高内聚、低耦合、单向数据流

### keep-alive平时在哪里使用？原理是什么？

- 使用keep-alive包裹动态组件时，会对组件进行缓存，避免组件重新创建

​		使用有两个场景，一个是动态组件，一个是router-view

如果不需要缓存，直接返回虚拟节点。

如果需要缓存，就用组件的id和标签名，生成一个key，把当前vnode的instance作为value，存成一个对象。这就是缓存列表

如果设置了最大的缓存数，就删除第0个缓存。新增最新的缓存。



### vue-router的两种模式的区别

- vue-router中有三种模式，分别是hash、history、abstract
- abstract在不支持浏览器的API换景使用
- hash模式兼容性好，但是不美观，不利于SEO
- history美观，historyAPI+popState，但是刷新会出现404




### 说说你对 SPA 单页面的理解，它的优缺点分别是什么？

SPA（ single-page application ）仅在 Web 页面初始化时加载相应的 HTML、JavaScript 和 CSS。一旦页面加载完成，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是利用路由机制实现 HTML 内容的变换，UI 与用户的交互，避免页面的重新加载。

**优点：**

- 用户体验好、快，内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
- 基于上面一点，SPA 相对对服务器压力小；
- 前后端职责分离，架构清晰，前端进行交互逻辑，后端负责数据处理；

**缺点：**

- 初次加载耗时多：为实现单页 Web 应用功能及显示效果，需要在加载页面的时候将 JavaScript、CSS 统一加载，部分页面按需加载；
- 前进后退路由管理：由于单页应用在一个页面中显示所有的内容，所以不能使用浏览器的前进后退功能，所有的页面切换需要自己建立堆栈管理；
- SEO 难度较大：由于所有的内容都在一个页面中动态替换显示，所以在 SEO 上其有着天然的弱势。

### computed 和 watch 的区别和运用的场景？

**computed：** 是计算属性，依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值时才会重新计算 computed  的值；

**watch：** 更多的是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；

**运用场景：**

- 当我们需要进行数值计算，并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算；
- 当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作 ( 访问一个 API )，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。



### Vue3.0 里为什么要用 Proxy API 替代 defineProperty API

**监听对象上的差异 :** 

defineProperty API 的局限性最大原因是**它只能针对单例属性做监听**

- Vue2.x中的响应式实现正是基于defineProperty中的descriptor，对 data 中的属性做了遍历 + 递归，为每个属性设置了 getter、setter。这也就是为什么 Vue 只能对 data 中预定义过的属性做出响应的原因，在Vue中使用下标的方式直接修改属性的值或者添加一个预先不存在的对象属性是无法做到setter监听的，这是defineProperty的局限性。

Proxy API的监听是针对一个对象的，那么对这个对象的所有操作会进入监听操作， 这就完全可以代理所有属性，将会带来很大的性能提升和更优的代码

**性能的差异:**

在 Vue.js 2.x 中，对于一个深层属性嵌套的对象，要劫持它内部深层次的变化，就需要递归遍历这个对象，执行 Object.defineProperty 把每一层对象数据都变成响应式的，这无疑会有很大的性能消耗。

在 Vue.js 3.0 中，使用 Proxy API 并不能监听到对象内部深层次的属性变化，因此它的处理方式是在 getter 中去递归响应式，这样的好处是真正访问到的内部属性才会变成响应式，简单的可以说是按需实现响应式，减少性能消耗。

### Vue3.0 编译做了哪些优化(底层,源码)

**生成 Block tree**

- Vue.js 2.x 的数据更新并触发重新渲染的粒度是组件级的，单个组件内部 需要遍历该组件的整个 vnode 树。**在2.0里，渲染效率的快慢与组件大小成正相关：组件越大，渲染效率越慢。**并且，对于一些静态节点，又无数据更新，这些遍历都是性能浪费。
- Vue.js 3.0 做到了通过编译阶段对静态模板的分析，编译生成了 Block tree。Block tree 是一个将模版基于动态节点指令切割的嵌套区块，每个 区块内部的节点结构是固定的，每个区块只需要追踪自身包含的动态节点。所以，**在3.0里，渲染效率不再与模板大小成正相关，而是与模板中动态节点的数量成正相关。**



**slot 编译优化**

- Vue.js 2.x 中，如果有一个组件传入了slot，那么每次父组件更新的时候，会强制使子组件update，造成性能的浪费 
- Vue.js 3.0 优化了slot的生成，使得非动态slot中属性的更新只会触发子组件的更新。动态slot指的是在slot上面使用v-if，v-for，动态slot名字等会导致slot产生运行时动态变化但是又无法被子组件 track 的操作

**diff方法优化**

- Vue2.x 中的虚拟dom是进行全量的对比。
- Vue3.0 中新增了静态标记（PatchFlag）：在与上次虚拟结点进行对比的时候，值对比带有patch flag的节点，并且可以通过flag 的信息得知当前节点要对比的具体内容化。

**hoistStatic 静态提升**

- Vue2.x : 无论元素是否参与更新，每次都会重新创建。
- Vue3.0 : 对不参与更新的元素，只会被创建一次，之后会在每次渲染时候被不停的复用。

**SSR优化**

- 当静态内容大到一定量级时候，会用createStaticVNode方法在客户端去生成一个static node，这些静态node，会被直接innerHtml，就不需要创建对象，然后根据对象渲染

**cacheHandlers 事件侦听器缓存**

默认情况下onClick会被视为动态绑定，所以每次都会去追踪它的变化,但是因为是同一个函数，所以没有追踪变化，直接缓存起来复用即可。



### vue3有了解过吗？能说说跟vue2的区别吗？

关于vue3的重构背景  简要就是

- 利用新的语言特性(es6)
- 解决架构问题

设计目标: 

- 随着功能的增长，复杂组件的代码变得越来越难以维护
- 缺少一种比较「干净」的在多个组件之间提取和复用逻辑的机制
- 类型推断不够友好
- bundle 的时间太久了

哪些变化:

- 速度更快
  - 重写了虚拟Dom实现
  - 编译模板的优化
  - 更高效的组件初始化
  - update性能提高1.3~2倍
  - SSR速度提高了2~3倍

- 体积减少
  - 通过webpack的tree-shaking功能，可以将无用模块“剪辑”，仅打包需要的
    - 对开发人员，能够对vue实现更多其他的功能，而不必担忧整体体积过大
    - 对使用者，打包出来的包体积变小了
- 更易维护
  - compositon Api
    - 可与现有的Options API一起使用
    - 灵活的逻辑组合与复用
    - Vue3模块可以和其他框架搭配使用
  - 更好的Typescript支持
- 更接近原生
  - 可以自定义渲染 API
- 更易使用
  - 响应式 Api 暴露出来
  - 轻松识别组件重新渲染原因

#####  Vue3新增特性

- framents
- Teleport
- composition Api
- createRenderer (我们能够构建自定义渲染器，我们能够将 vue 的开发模型扩展到其他平台 S)



### 说说Vue 3.0中Treeshaking特性？举例说明一下？

1. 是什么

> Tree shaking 是一种通过清除多余代码方式来优化项目打包体积的技术，专业术语叫 Dead code elimination

简单来讲，就是在保持代码运行结果不变的前提下，去除无用的代码

在Vue2中，无论我们使用什么功能，它们最终都会出现在生产代码中。主要原因是Vue实例在项目中是单例的，捆绑程序无法检测到该对象的哪些属性在代码中被使用到

而Vue3源码引入tree shaking特性，将全局 API 进行分块。如果您不使用其某些功能，它们将不会包含在您的基础包中

2. 如何做

> Tree shaking是基于ES6模板语法（import与exports），主要是借助ES6模块的静态编译思想，在编译时就能确定模块的依赖关系，以及输入和输出的变量

Tree shaking无非就是做了两件事：

- 编译阶段利用ES6 Module判断哪些模块已经加载
- 判断那些模块和变量未被使用或者引用，进而删除对应代码

3. 作用

通过Tree shaking，Vue3给我们带来的好处是：

- 减少程序体积（更小）
- 减少程序执行时间（更快）
- 便于将来对程序架构进行优化（更友好）
  

### setup函数调用时机

从生命周期的角度来看，它会在`beforeCreate`之前执行。也就是创建组件先执行`setup`、`beforeCreate`、`create`。










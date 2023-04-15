# `Vue3`

##### 升级`vue-cli`

开发`vue2`的项目时，建议使用`4.5`等稍低的`cli`版本。

开发`vue3`的项目，则必须升级`vue-cli`到`5`的高版本。如仍有开发`vue2`项目的需要，可选择局部安装低版本。

**全局升级**：输入`npm update -g @vue/cli`指令，更新安装到全局的`vue/cli`的版本。

**配置文件**：升级到`vue/cli`的5版本后，会在项目中自动生成一个`jsconfig.json`文件，对打包处理`js`文件的配置。



##### `vue3`优势

`vue3`完全不兼容低版本的浏览器，因此代码体积小了很多。

**性能优化**：与`vue2`相比

- 运行速度：提升了1.5倍左右
- 渲染速度：初始渲染速度提升了55%，更新渲染速度提升了133%
- 占用内存：项目的整体占用内存减少了54%

- 项目体积：项目被编译打包后减少41%，删除从没被使用过的**死代码**。
- 源码优化：`vue3`使用`proxy`代理对象全面替换`vue2`的`defineProperty`，并重写了虚拟`DOM`。
- 其他新增：完全拥抱`TS`，新增了一些更方便的`API`（如：组合式`API`）及内置组件。

<hr>

### `Vite`开发工具

`vite`是由`vue`官方开发的另一款开发与构建工具，与`vue-cli`的功能和作用是一样的，但更丰富和轻量。

**优点**：

- 按需引入：默认安装的项目插件很少，遵循按需引入的原则。
- 默认使用组合式`API`替换选项式`API`

##### 创建项目

输入`npm create vite@latest` + 项目名称，表示使用最新版的`vite`工具创建一个项目。

##### 安装依赖

定义好项目名称之后，首先`cd`切换至项目根目录，然后输入`npm i`指令安装项目所需的依赖包。

默认安装的只有`vue，vite`和`@vitejs/plugin-vue`这三个依赖包，要使用`less`等其他工具需手动安装。

##### 运行项目

安装完项目的第三方依赖包之后，就可以使用`npm run dev`指令来运行`vue`项目了。

<hr>

### `Vue3`项目

`vue3`一律通过导入相应的`create`方法来创建实例对象，以取代之前使用`new`关键字的创建方式。



##### 应用实例

`vue3`直接在`main.js`文件中导入`vue`的`createApp`方法，通过该方法创建一个`app`应用实例。

精确挂载：类似避免造成全局污染

将与该应用相关的所有插件直接挂载到这个`app`实例上，而不是像`vue2`那样将插件挂载到`Vue`全局对象上。

```js
// 在main.js文件中
import {createApp} from "vue"
import App from "./App.vue"
import router from "./router"
import store from "./store"

// 创建应用实例，并挂载对应插件
createApp(App).use(store).use(router).mount("#app")
```



##### 全局状态

`vue3`通过从`vuex`中导入`createStore`方法，来创建一个全局状态管理的实例对象。

```js
// 在store的index文件中
import {createStore} from "vuex"

export default createStore({
    state: {},
    getters: {},
    mutations: {},
    actions: {},
    modules: {}
})
```



##### 路由实例

`vue3`通过从`vue-router`中导入`createRouter`方法，来创建一个全局状态管理的实例对象。

通过导入使用`createWebHistory`方法，为路由实例指定路由模式：`history`模式和`hash`模式（在端口后加一个`#`标记）。

```js
// 在router的index文件中
import {createRouter, createWebHistory, createWebHashHistory} from "vue-router"

const routes = [];

const router = createRouter({
    // history: createWebHashHistory(), // 指定为哈希模式
    history: createWebHistory(), // 指定为历史模式
    routes
})

export default router
```



##### 模板结构

在`vue3`的`<template>`元素中，不再限制必须有一个根元素，允许有零个或多个子元素。

```html
<template>
	<nav>
    	<router-link to=""></router-link>
        <router-link to=""></router-link>
    </nav>
    <router-view />
</template>
```



##### 组合式`API`

选项式`API`：在对应的选项中，配置对应的数据或方法，每个选项具有特定的含义和用途。

**优点**：数据和方法的代码位置固定，每个选项具有明确的功能，简单易上手。【中小型的、逻辑不太复杂的项目】

**缺点**：当代码逻辑复杂或量很大时，阅读性很差；相似的逻辑不易复用；组织性很差。

```js
export default {
    data() {
        return {
            // 在data选项中定义组件的数据
        };
    },
    methods: {
        // 在methods中定义组件的方法
    },
    ......
}
```

组合式`API`：

- 选项式：将组件的所有逻辑统一到`setup`选项中管理，`setup`是一个方法形式的选项。
- 语法糖：在一个`<script>`标签上添加`setup`属性，表示该元素完全使用组合式`API`的写法，方法和数据被自动暴露出去【推荐】

优点：类似于原生的`js`代码写法；易于将代码划分为功能块；阅读性和维护性较好。【大型的、逻辑复杂的项目】

```js
// 选项式：必须使用return将数据和方法返回出去。
// 注释：通过return返回的对象，可在页面中直接使用其根属性。
export default {
    setup() {
        // 数据
        let name = "丸子",
            age = 14;
        
        // 方法
        function handleName () {
            console.log(this); // this指向该setup选项的配置对象（一个Proxy代理对象）
            console.log(name);
        }
        
        // 通过return一个对象将数据或方法暴露给组件实例，未返回出去的数据对组件不可见。
        return {
            name,
            age,
            handleName
        };
    }
}
```

```html
<script setup>
	// 通过ref()将数据自动返回出去，将指定数据包装成一个具有响应式功能的数据。
    import {ref} from "vue"
    const count = ref(10);
</script>
```


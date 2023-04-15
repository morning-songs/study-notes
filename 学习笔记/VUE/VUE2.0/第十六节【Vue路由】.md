# `Vue`路由

现代的路由分为后端路由和前端路由，比如：`Vue`路由属于前端路由，`node`路由属于后端路由。

<hr>

### 了解`Vue Router`

`Vue Router`是`Vue`框架的核心插件之一，`v3.x`对应`vue2`的版本，`v4.x`对应`vue3`的版本。



#### 安装

##### `npm`安装

输入`npm i vue-router`指令安装。

##### `Vue CLI`安装

输入`vue add router`指令，在当前`Vue CLI`项目中，以插件的形式添加`Vue Router`。

注意：如果这么做，请提前备份好一份`App.vue`文件和`main.js`文件，因为`CLI`会覆盖重置项目中的这两个文件。

##### 创建时安装

在创建`Vue`项目时，可以在配置项目的时候勾选`Router`工具，使创建项目时就直接安装到当前项目中。【推荐】



#### 新增目录

安装好`Vue Router`后，在项目的`src`目录下会新增两个子目录：`router`和`views`。

##### `router`目录

`router`目录下默认会有一个`index.js`文件，用来管理和处理当前项目中的路由内容。

##### `index.js`文件

`router`目录下的`index.js`文件，会默认导出一个`router`的配置对象，以供`main.js`使用。

`main.js`文件导入该`router`配置对象后，会将其挂载到`Vue`根组件实例上。

```js
// index.js中
export default router
// main.js中
import router from "./router"
new Vue({
	router,
	render: h => h(App)
}).$mount('#app')
```

##### `views`目录

`views`目录则主要用来管理每个页面组件【可复用的子组件应放在`components`目录中】



#### 对原文件的影响

##### `App.vue`文件

添加了`Vue Router`后，`CLI`会重置`App.vue`文件，并去掉`script`元素。

##### `main.js`文件

在`main.js`文件中，添加导入和挂载`router`的内容。

<hr>

### 使用`Vue Router`

##### 配置组件

先在各个页面组件中配置好对应的页面内容，然后导入到`App.vue`中使用。

如果某个组件不需要传递数据，可以简写为空元素的形式。如：`<Home />`

```html
<template>
	<div id="app">
        <div id="top-nav">导航</div>
        <div id="page-views">
            <Home />	<!--空元素形式-->
        </div>
    </div>
</template>
```

##### 清除样式

建议直接在`App.vue`根组件的`style`中清除默认样式，优雅且不失效率。

##### 页面数据

一般每个页面上的主要数据都由后端提供，应事先将这些数据保存到对应页面组件的`script`元素中，方便在组件结构中使用数据。



#### 路由切换

要实现通过切换路由来切换组件，必须做到以下两点：

- 路由发生变化
- 路由对应的组件要渲染到`html`的页面上

##### 导入路由模块

首先要在`index.js`文件中，导入路由模块。如：`import VueRouter from 'vue-router'`

##### 添加路由插件

导入路由模块后，通过`Vue.use()` 方法，添加`vue router`路由插件。如：`Vue.use(VueRouter)`

##### 配置路由对象

建议先在外面配置好各个路由对象，然后挂载到路由实例上【路由对象应按照一定顺序放在一个数组中】

必要属性：`path`，`component`

- `path`：自定义的路由值，切换时使用的特定片段。
- `component`：路由对应的组件。

##### 创建路由实例

通过`new VueRouter()`的方式，创建一个路由实例对象。

##### 配置路由实例

在路由实例对象中，有两个属性是必须的：`mode`和`routes`

- `mode`：用来指定路由模式，常见的有`history`和`hash`两种模式。
- `routes`：用来指定路由的配置对象。

##### 命名路由

在对每个路由对象进行配置时，可以为`path`的值指定一个`name`名称，以便直接使用该名称来代替路由值。

注意：在任何使用`path`的地方，都可以用其名称来代替，必须是对象形式。如：`{name: "路由名称"}`

##### 路由懒加载

某些组件并不是一开始就需要被渲染的，最好的方式是：需要渲染该组件时再加载/导入它。

可以将`component`的值设置为异步加载的方法，如：`() => import('@/views/Article')`

注意：

- 当第一次访问这个路由时，会去异步加载对应的组件。
- 被加过的组件会存到缓存中，以便下次直接取出使用。

##### 动态路由

当页面的结构相同，只有数据内容不同时，可以使用动态路由。即：多个路由对应一个页面组件，只有数据内容不同。

设置动态参数：在`path`值的任何位置，都可以以冒号开头，插入一个或多个变量【这些变量可匹配任意的字符片段】

获取动态参数：在对应的组件中，可使用`this.$route`获取当前组件的路由对象，其`params`属性保存着动态参数。

```vue
// 路由配置对象
{
    path: '/article/:id/:num', // id和num变量用来接收这些路由片段
    component: () => import('@/views/Article') // 在对应的组件中可以获取到id和num这两个动态路由参数
}
// 对应的组件中
<script>
	export default {
        name : 'Article',
        created () {
            // 获取该组件对应的动态路由参数：id
            console.log(this.$route.params.id);
            // 通过该id，发起Ajax请求，请求id号对应的文章数据（需要前后端联动）
        }
    }
</script>
```

##### 匹配任意路由

可以将`path`的值设为`'*'`，表示任意的路由都可以加载并渲染它所对应的组件。一般只用在数组最后，作为默认项来使用。

```js
const routes = [
    {
        path: ,
        component:
    },
    {
        path: ,
        component:
    },
    {
        path: '*', // 只作为最后一项使用，当前面所有的路由都匹配不上时，使用这段路由对应的组件
        component: () => import('@/views/NotFound')
    }
]
```

案例：

```js
// 在router目录下的index.js中
// 导入路由模块
import VueRouter from 'vue-router'
// 添加路由插件
Vue.use(VueRouter);
// 导入组件，一般只导入首页组件
import Home from '@views/Home'
// 配置路由对象，数组形式
const routes = [
    {
        path: '/', // 自定义路由值，必须有效。
        name: 'Home', // 为该路由指定一个名称（命名路由），方便直接使用名称来切换。
        component: Home, // 该路由对应的组件：当切换到该路由时，在页面渲染的组件。
    },
    {
        path: '/article',
        name: 'Article',
        component: () => import('@/views/Article') // 懒加载
    },
    {
        path: '/about',
        name: 'About'
        component: () => import('@/views/About')
    }
]
const router = new VueRouter({
    mode: "history", // 指定为history路由模式
    routes // 挂载路由配置对象到路由实例上
})
// 导出路由实例对象
export default router

// 在main.js文件中
// 导入路由实例对象
import router from '@/router'
// 挂载到根组件的实例上
new Vue({
	router, // 挂载路由实例对象到根组件实例上
	render: h => h(App)
}).$mount('#app')
```

##### 组件渲染出口

当配置好如上内容后，切换到特定路由时，其实已经加载出了对应的组件，但无法被渲染出来。

此时，需要使用`Vue Router`提供的`<router-view>`组件标签来为路由组件占位/提供渲染出口。

```html
<!-- 根组件中 -->
<template>
	<div id="#app">
        <div id="page-view">
            <router-view></router-view><!--只有路由匹配到的组件，才会渲染到该标签中-->
        </div>
    </div>
</template>
```

注意：当路由或路由对应的组件不存在时，页面会显示空白。不过，通常的做法是提醒用户：“404，页面不见了”。

##### 路由导航组件

使用`<router-view>`来提供渲染出口，每次切换都需要手动输入特定的路由才能切换，用户体验不好。

因此，可以使用`<router-link>`路由导航组件，来实现点击切换路由【该组件最终会被转换为`a`元素】

注释：路由导航组件实现通过点击切换到指定的路由，解决了用户必须通过手动输入才能切换路由的问题。

属性：

- 路由导航组件有一个`to`属性，与`a`元素的`href`属性类似，用来指定一段特定的路由。

注意：

- 虽然直接使用`a`元素代替`router-link`组件，也可以实现类似的功能，但不建议这么做。
- 因为`a`标签的默认行为会发生跳转，导致页面被刷新。
- 而经过处理的`router-link`虽然最终也会转换为`a`元素，但不会刷新页面。
- 若要为每个路由导航组件添加样式，应使用`a`元素，而不是`router-link`标签。

`router-link`转换为`a`元素后，当前渲染的组件默认会添加上三个属性：`href`，`aria-current`，`class`

- `href`：指向当前组件的路由，替代`to`属性。
- `aria-current`：当前渲染的组件才具有这个属性，且同组中为同一值，如：`page`。
- `class`：自动添加两个类名：`router-link-exact-active`和`router-link-active`
  - `router-link-exact-active`：指向当前路由渲染的组件，只作用在当前路由对应的组件上【`exact`：精确的】
  - `router-link-active`：点击子路由时，父子路由都会被作用该类名【这在需要给父子路由都设置样式时很重要】

```html
<div id="app">
    <div id="topnav">
        <!--直接使用路由值：path-->
        <ul>
            <li><router-link to="/">首页</router-link></li>
            <li><router-link to="/article">文章</router-link></li>
            <li><router-link to="/about">关于</router-link></li>
        </ul>
        <!--使用路由名称：name-->
        <ul><!-- 传入一个配置对象，通过name指定对应的路由名称 -->
            <li><router-link :to="{name: 'Home'}">首页</router-link></li>
            <li><router-link :to="{name: 'Article'}">文章</router-link></li>
            <li><router-link :to="{name: 'About'}">关于</router-link></li>
        </ul>
    </div>
</div>
<style scoped>
    /* 为当前渲染的组件添加选中样式 */
    a.router-link-exact-active {
        color : yellow;
    }
</style>
```


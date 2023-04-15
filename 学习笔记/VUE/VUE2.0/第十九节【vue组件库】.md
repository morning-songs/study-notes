# `vue`组件库

`vue`组件库，由官方提供一些`UI`组件，另外一些是由组件爱好者或大型团队开发出来的。

常用`UI`组件库：（基于`vue`）

`PC`端：`element-ui`（基于`vue2`），`element-plus`（基于`vue3`），`iView`

移动端：`Vant`

<hr>

### 使用`Element-ui`

`Element-ui`组件库是基于`vue2`的，由饿了么团队提供与维护，采用`Sass`语法编写。

##### `npm`安装

输入`npm i element-ui -S`指令，将`element-ui`组件库安装到当前项目的生产环境中。

#### 引入组件

引入组件分为：完整引入和按需引入。完整引入将所有`ui`组件都引入到项目中，非常占用资源。

##### 完整引入

```js
// 在main.js入口js文件中，引入所有ui组件，及其样式文件
import ElementUI from "element-ui" // 引入所有的element-ui组件
import 'element-ui/lib/theme-chalk/index.css' // 引入element-ui组件的样式文件
// 注册为Vue的插件，将所有组件添加到Vue项目中，所有vue组件都可以直接使用
Vue.use(ElementUI);
```

##### 按需引入

借助`babel-plugin-component`工具，按需引入`ui`组件，以减小项目体积。

安装：输入`npm i babel-plugin-component -D`指令，安装工具。

配置工具：

- 目前使用的脚手架是`vue/cli`，而之前使用的是`vue-cli`，两者不一样。
- `vue/cli`脚手架对应的配置文件是`babel.config.js`，需要在配置对象中配置`plugins`选项。

```js
// 配置babel.config.js中的plugins选项
module.exports = {
    presets: ['@vue/cli-plugin-babel/preset'],
    "plugins" : [
        // 对babel-plugin-component插件的配置
        [
            "component",
            {
                "libraryName": "element-ui", // 对ui组件的配置
                "styleLibraryName": "theme-chalk" // 对样式的配置
            }
        ]
    ]
}
```

注意：修改了配置文件，需要重启服务，否则没有效果。

按需引入：在`main.js`文件中，导入所需的`ui`组件

```js
import {Button, Icon} from "element-ui" // 导入button和Icon两个ui组件
// 注册到全局使用
Vue.use(Button);
Vue.use(Icon);
// 相当于调用了Vue.component全局注册方法
Vue.component(Button.name, Button); // element-ui的组件名称采用大驼峰式，以El开头，如：ElButton
```

注意：完整的`ui`组件列表，以官网的`components.json`为准。

#### 使用组件

在`vue`组件中，直接复制`element-ui`官网提供的相关`ui`组件代码即可使用该`ui`组件。

```html
<!-- 在根组件中 -->
<template>
	<div>
    	<el-button>默认按钮</el-button><!-- 在使用element-ui组件时，采用小写形式，短线连接 -->
        <el-button type="primary">主要按钮</el-button>
        <el-button type="success">成功按钮</el-button>
        <el-button type="info">信息按钮</el-button>
        <el-button type="warning">警告按钮</el-button>
	</div>
</template>
```

附：`Button`组件：使用`type`，`plain`，`round`和`circle`属性来定义`button`的样式，`icon`属性用来指定按钮的图标。

##### 定义样式

当需要自定义样式，修改`ui`组件样式时，可以通过类名覆盖。若覆盖失败，可以添加穿透覆盖（`Sass`语法）【穿透：`/deep/`】

`element-ui`也提供了一些高级`sass`的`css`变量，来覆盖样式。

查看`ui`组件的初始定义：

- 可以在`node_modules/elememt-ui/package`目录下，查看各`ui`组件初始定义。
- 在`node_modules/elememt-ui/lib/theme-chalk`目录下，查看各组件的初始样式。

注意：不推荐修改初始的样式文件，要覆盖样式，应在引用的`vue`组件中自定义类名来覆盖【推荐使用`Sass`语法】

<hr>

### 使用`axios`

##### 安装

输入`npm i axios`指令，安装到当前项目中。

##### 导入

与`element-ui`一样，使用`axios`需要到`main.js`入口文件中导入，如：`import axios from "axios"`

##### 配置全局路径

当向同一个服务器发起请求时，每一段`url`的域名和主机都是相同的，此时可以将相同的片段配置为一段全局路径。

使用方法：

- 先通过`axios.defaults.baseUrl = 相同片段`，将该片段配置为全局路径。
- 然后，可以在发起请求时，直接使用各自独特的路由片段。`axios`会将它们直接拼接到全局路径的后面。
- 但要注意的是，全局路径必须在使用前定义好，顺序不能错。

```js
// 在main.js文件中，使用axios
import axios from "axios"
// 配置axios请求地址的前缀
axios.defaults.baseUrl = "http://rap2api.taobao.org/app/mock/301569";
// 直接使用特定片段发起请求。该片段先会被直接拼接到baseUrl后面。
axios.get("/example/1456789457864");
```

##### 设置全局`axios`

为了方便能够在所有的组件实例中都可以使用`axios`发起请求，而不仅仅是在`main.js`中使用，可以将其定义到`Vue`的原型上。

```js
// 在main.js文件中，将axios定义到Vue原型上
Vue.prototype.$http = axios; // 共享引用，二者皆可发起axios请求
// 此时，可以任何组件实例中通过this.$http使用axios方法。
```

#### 发起请求

使用`axios`发起请求，返回的值是一个`promise`的对象。因此，可以使用`then`方法继续监听。

`then`的参数：`then`的参数是回调函数，第一个形参是成功的回调，第二个形参是失败的回调。

使用`async`和`await`

- 使用`async`和`await`关键字，可以设置必须等待请求（异步的）完毕后，才能执行下一步的同步代码。
- `async`可以设置在任何方法上；`await`则设置在异步请求的语句前。

##### 发起`get`请求

`get`请求，携带的参数一般是直接在`url`中。因此，可以在发起请求时，通过`params`属性提交数据。

```js
created() {
    this.$http.get("/api/getData", {
    	params: {
            id: 212
        },    
    }).then((res) => {
        // 成功的回调，返回成功的结果
    }, (err) => {
        // 失败的回调，返回失败的信息
    })
}
```

查看响应：在`Network`中找到响应文件，在`Headers`的`Request URL`中可以查看响应的`get`数据。

##### 发起`post`请求

`post`请求，提交的参数是`data`属性的值。

```js
async mounted() {
    const {data} = await this.$http.post("/api/postData", { // 先执行异步的请求，再执行同步的赋值
    	data: {
            id: 520
        },    
    });
    console.log(data); // 直接解构出结果对象的data属性
}
```

查看响应：在`Network`中找到响应文件，在`Payload`中可以查看响应的`post`数据。

#### 模块化请求

##### 管理请求

直接在组件实例中发起`axios`请求，仅适用于小型项目。中大型项目应使用模块化来管理请求。

模块管理：可以在`src`下新建一个`utils`目录，再建立一个`axios.js`来管理项目的`axios`请求。

注意：使用模块管理，在`main.js`中就不必再导入`axios`来挂载使用了，直接在`axios.js`中统一配置全局。

```js
// 在axios.js文件中
// 导入aioxs模块
import axios from "axios"
// 创建并导出axios实例对象/方法，使用axios的create方法创建一个axios方法
export default axios.create({ // 配置axios对象/方法
    baseURL: "http://rap2api.taobao.org/app/mock/301569", // 配置全局路径
    timeout: 5000, // 设置5s的超时请求
    headers: {} // 定义全局的请求头
})
```

##### 封装请求

相关的请求方法，也可以在`src`下新建一个`req`目录来统一管理。如：将请求与用户相关的方法，存放在`user.js`中。

```js
// 在user.js中，封装与用户相关的请求方法
// 导入axios实例对象
import request from "@/untils/axios"
// 定义请求用户头像的方法
function reqUserAvatar(params) {
    return request({ // 将执行后的对象返回出去
        url: "/api/getData", // 请求的路由
        params, // 该请求携带的参数
        methods: "get"
    })
}
// 导出与用户相关的请求方法
export { // 此处导出，不可加default，否则会导致出错
    reqUserAvatar, // 请求用户头像的方法
}
```

##### 使用方法

封装好请求方法后，只需要在组件中导入方法，即可使用它们发起请求了。

```html
// 在组件中使用封装方法，请求用户头像
<script>
	import {reqUserAvatar} from "@/req/user"
    export default {
        async created() {
            const userAvatar =  await reqUserAvatar({tag: "今天星期三"}); // 接收请求结果
        }
    }
</script>
```

#### 配置拦截器

可以在`axios.js`文件中，配置请求的拦截器和响应的拦截器，使每一次的请求和响应都要在中途经拦截器的拦截处理。

拦截器：通常用来在接收到请求或响应之前，验证用户身份，或者进行特殊的过滤处理。

注意：在请求拦截器中设置的弹框等，至少要在响应拦截器中关闭它们。

```js
// 在axios.js文件中
import axios from "axios"
const service = axios.create({
    baseURL: "http://rap2api.taobao.org/app/mock/301569",
    timeout: 5000
});
// 配置请求的拦截器：axios.interceptors.request.use()方法
service.interceptors.request.use(
	config => { // 拦截成功时
        // config是拦截到的请求对象，经过处理之后，必须将它返回出去。
        // 比如：在此设置一个loading加载框，在响应拦截器中关闭。
        config.headers = {
            "Authorization": "Token-Ok", // 在请求头中添加该属性
        }
        return config; // 将经过处理的请求对象返回出去
    },
    error => { // 拦截失败时
        // 对拦截失败的错误对象进行处理
        return Promise.reject(error); // 返回带有错误信息的promise对象
        // return error; // 或者直接返回错误信息。
    }
)
// 配置响应的拦截器：axios.interceptors.response.use()方法
service.interceptors.response.use(
	response => { // 拦截成功时，200以内的状态码都会触发该函数。
        // response是拦截到的响应对象，经过处理之后，也必须将它返回出去。
        // 在此关闭loading加载框
        return response; // 将经过处理的响应对象返回出去
    },
    error => { // 拦截失败时，超出200的状态码都会触发该函数。
        // 对拦截失败的错误对象进行处理
        return Promise.reject(error); // 返回带有错误信息的promise对象
        // return error; // 或者直接返回错误信息。
    }
)

// 导出axios方法实例
export default service
```


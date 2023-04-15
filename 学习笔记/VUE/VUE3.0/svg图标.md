# 使用`svg`图标

在`vue3`中批量使用`svg`，可首先在`src`下创建`svg`目录，其下新建`index.js`文件以及`icons`文件夹。

注释：`index`文件用于批量处理`svg`图标文件，`icons`文件夹用于存储各图标的`svg`代码。

提示：将`index`文件导入到`main.js`中执行，如：`import "@/svg/index.js"`。

注释：`svg`是一种图片，修改大小使用`width`和`height`。

设置`svg`颜色：通过`CSS`的`fill`属性，将其设置为`currentColor`，可使用`color`来控制`svg`图标的颜色。

```css
svg {
    fill: currentColor;
}
```



##### 获取文件相对路径

通过`require`的`context`方法，来获取一个能够取出所有`svg`文件的相对路径的方法。

`context`参数：（文件夹路径，`false`，匹配文件的正则）

通过`context`返回方法上的`keys`方法，可以遍历得到指定目录下所有`svg`文件的相对路径。

```js
// 在svg下的index中

// 匹配icons目录下的所有svg文件
const request = require.context("./icons", false, /\.svg$/);

// 遍历得到每个svg文件的相对路径，相对于icons文件夹
let srcArr = request.keys(); // 数组

// 使用request方法，可将指定的文件导入到项目打包处。如：request("./vip.svg"); -- 可遍历批量导入
srcArr.forEach(src => {
    request(src); // 遍历批量导入
})
```



##### 配置`config`文件

在`vue.config.js`文件中，配置第三方插件，来将`svg`图标文件打包到项目中使用。

**提示**：`Vue-CLI`与`webpack`对于配置文件的写法不同，可在`Vue/CLI`官网查看标准。

```js
// 在vue.config.js中
const { defineConfig } = require('@vue/cli-service'),
      path = require("path");

module.exports = defineConfig({
  transpileDependencies: true,
  // 配置对svg文件的打包处理
  chainWebpack(config) {
      // 配置处理svg的loader
      
      // 清除对svg的默认配置处理
      config.module.rules.delete("svg");
      
      // 配置自定义的处理规则
      config.module
      .rule('svg') // 定义解析svg的规则
      .exclude.add(path.join(__dirname, './src/svg')) // 文件路径
      .end()
      
      config.module
      .rule('icons') // 定义处理icons图标的规则
      .test(/\.svg$/) // 匹配svg文件
      .include.add(path.join(__dirname, './src/svg')) // 文件路径
      .end()
      .use('svg-sprite-loader') // 使用处理工具
      .loader('svg-sprite-loader')
      .options({
          symbolId: 'icon-[name]' // 设置引用图标的类名，以图标类名icon-开头。
      })
      .end()
  }
})
```



##### 使用`svg`元素

安装完插件，定义好配置之后，可在页面上使用`svg`及其`use`子元素，来引用`svg`图标文件。

```html
<svg>
	<use xlink:href="#icon-vip"></use> <!-- 必须带#前缀 -->
</svg>
```



##### 封装为组件

由于`svg`图标在全局都可能需要被引用，因此可以将其调用封装在一个组件中，需要时引用组件并传参即可。

```html
<!-- 在components目录下创建SvgIcon.vue -->
<template>
	<svg>
    	<use :xlink:href="'#icon-' + iconFileName"></use>
    </svg>
</template>

<script setup>
	import {defineProps} from 'vue'
    
    // 接收参数
    const props = defineProps({
        // 图标名称
        iconFileName: {
            required: true,
            type: String
        }
    })
</script>
```

在`main.js`中，将该组件注册到全局使用。

```js
// 在main.js中
import "@/svg/index.js" // 导入svg文件执行
import SvgIcon from "@/components/SvgIcon.vue"

const app = createApp(App);

// 注册全局组件
app.component("SvgIcon", SvgIcon);

app.use(store).use(router).mount('#app')
```

页面使用：通过组件传参，获取指定的图标

```html
<SvgIcon iconFileName="music" /> <!-- 为iconFileName指定传递值 -->
```


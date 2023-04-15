# `BOM`操作

##### 页面偏移量 / 滚动条滚动距离

- 标准方法：`IE9`以上及其他浏览器【offset：页面偏移量】

  - 左偏移量：`window.pageXOffset`

  - 上偏移量：`window.pageYOffset`

- 不兼容处理：`IE8`及以下【滚动条滚动距离】

  - 横向滚动条滚动的距离：`document.body.scrollLeft / document.documentElement.scrollLeft`
  - 纵向滚动条滚动的距离：`document.body.scrollTop / document.documentElement.scrollTop`
  - `IE8`及以下的浏览器，在此方面自身互不兼容，诞生如上的两种方法【基于`body`或基于`html`】

- 封装获取滚动条滚动距离的方法：

```js
function getScrollOffset () {
    if (window.pageXOffset) { // 支持标准方法时
        return {
            x : window.pageXOffset,
            y : window.pageYOffset
        }
    }else { // 不支持时
        return { // IE8及以下浏览器的这两个方法：一个存在值，另一个就为0.
            x : document.body.scrollLeft + document.documentElement.scrollLeft,
            y : document.body.scrollTop + document.documentElement.scrollTop
        }
    }
}
```

##### 页面尺寸 / 视口尺寸

可视区指使用`HTML`编写，呈现给用户看的文档区域，不包括浏览器外壳部分。

视口的尺寸会根据页面的缩放而变化，浏览器标准视口宽度：`1440px`。

渲染模式：

- 每一个浏览器都具有两种渲染模式：标准模式和混杂 / 怪异模式。
- 因为在浏览器升级后，使用新的语法去解析渲染旧语法写的页面就会出现很多不兼容的问题。
- 标准模式：通过`<!DOCTYPE html>`开启，支持最新的语法，通过最新的语法来解析渲染页面。
- 混杂模式：去除`<!DOCTYPE html>`，识别之前的语法，通过旧的语法去解析渲染旧的页面。

##### 查看兼容模式【`compatMode`】

使用`document.compatMode`属性，可以获取当前页面的兼容模式。【`compat`：兼容性】

- 标准模式：`"CSS1Compat"`；	怪异模式：`"BackCompat" // 向后兼容`

获取视口的当前尺寸：

- 标准方法：
  - 视口宽度：`window.innerWidth`
  - 视口高度：`window.innerHeight`
- `IE`方法：`IE8`及以下
  - 标准模式：【client：客户端】
    - 视口宽度：`document.documentElement.clientWidth`
    - 视口高度：`document.documentElement.clientHeight`
  - 怪异模式：
    - 视口宽度：`document.body.clientWidth`
    - 视口高度：`document.body.clientHeight`

封装方法：获取视口的当前尺寸

```js
function getViewportSize () {
	if (window.innerWidth) {
        return {
            w : window.innerWidth,
            h : window.innerHeight
        }
    }else if (document.compatMode === "BackCompat") {
        return {
            w : document.documentElement.clientWidth,
            h : document.documentElement.clientHeight
        }
    }else {
        return {
            w : document.body.clientWidth,
            h : document.body.clientHeight
        }
    }
}
```

##### 查看元素的几何尺寸

元素通过调用`getBoundingClientRect()`方法，可以获取该元素在页面中的几何尺寸。

几何尺寸：

- 宽高：`width` -- 元素宽度，`height` -- 元素高度
- 坐标：`(left, top, right, bottom)`，分别指元素的各边界到视口左、上边界的距离。

注意：

- 获取宽高在低版本`IE`中并未实现，兼容：`width = right - left; height = bottom - top;`
- 返回的结果并不是实时的，通过`js`修改元素尺寸后，其状态信息仍保持原有不变。

##### 查看元素的尺寸

元素的尺寸指：元素的宽度和高度。

查看元素宽度：`offsetWidth` ； 查看元素高度：`offsetHeight`

##### 查看元素的位置

返回该元素相对于最近的有定位父级的位置距离，`body`作为最终的有定位父级。

查看左距离：`offsetLeft` ； 查看上距离：`offsetTop`

##### 查看有定位的父级【offsetParent】

元素通过使用`offsetParent`属性，可以查看最近的有定位的父级元素，`body`是顶端元素。

##### 控制滚动条

`window`上定义了三个方法，可以让滚动条滚动指定的距离或到指定的位置。

滚动到指定位置：`scroll(x, y)` 和 `scrollTo(x, y)`，同一个位置不会滚动。

滚动指定的距离：`scrollBy(x, y)`，每一次滚动多少距离。

案例：设置自动阅读功能

```html
<div class = "start">start</div>
<div class = "stop">stop</div>
<script>
    let start = document.getElementsByClassName("start")[0],
        stop = document.getElementsByClassName("stop")[0],
        timer = 0, // 设置全局定时器
        flag = true; // 设置一个创建定时器的开关
    // 开启自动阅读
    start.onclick = function () {
        if (flag) { // 为true时，打开定时器创建开关
            timer = setInterval(() => { // 创建一个定时器
                window.scrollBy(0, 10); // 每次滚动的距离
            }, 100); // 每100ms执行一次
            flag = false; // 锁定当前定时器，关闭定时器创建开关
        }
    }
    // 关闭自动阅读
    stop.onclick = function () {
        clearInterval(timer); // 清除指定定时器
        flag = true; // 打开定时器的创建开关
    }
</script>
```

注意：为定时器设置一个开关是必要的，因为每一次的点击都会产生一个新的定时器。这些新定时器与未清除的旧定时器，叠加使用就会导致效果叠加，如：速度叠加。并且新定时器不断覆盖旧定时器，也会导致旧定时器无法得到正常的关闭与清除。而使用一个开关来控制新定时器的开闭，则很好地解决了这两个问题。【锁定当前定时器，只当该定时器被清除后，才开启创建新定时器的开关】

### 作业

求元素相对于文档 / `body`元素的坐标


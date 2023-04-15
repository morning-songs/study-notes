# 脚本化`CSS`

##### `CSS`行间样式声明表

每一个`DOM`元素都具有一个`style`属性，它的值是一个类数组`CSSStyleDeclaration`，里面存放着该元素所有能使用的`CSS`行间样式。

换句话说，它无法访问页面级`CSS`以及外部文件的`CSS`样式。【只能读写元素的行间`CSS`样式】

##### 书写规范

- 关键字 / 保留字：遇到`CSS`样式属性名与`JS`系统的关键字、保留字冲突时，以`"css"`为前缀小驼峰式命名。
  - 如：`float ==> cssFloat`
- `CSS`复合属性名：复合属性必须拆解，重新组合单词以小驼峰式命名。
  - 如：`font-size ==> fontSize`
- 写入的值必须是字符串格式

##### 获取`CSS`计算样式表

通过`style`获取到的仅仅是元素的行间样式，而由于权重的不同，页面最终的显示样式，不一定如行间样式如愿。

页面最终展示的样式，是通过计算权重后整合的样式。而这些样式存在元素的计算样式表中【只读，不可写】

通过`window.getComputedStyle()`方法，可以获取指定元素的计算样式表，它也是一个`CSSStyleDeclaration`类数组。

使用方法：`window.getComputedStyle(元素，null)[prop] // 如：window.getComputedStyle(div, null).width`

注意：

- 计算样式只读
- 返回的计算样式的值是经过转换处理的：
  - 相对单位的值计算为对应的绝对单位的值，如：`10em ==> 160px`
  - 颜色值计算为对应的`rgb`颜色模式的值，如：`red ==> rbg(255, 0, 0)`
- `IE8`及以下浏览器不兼容，但`DOM`元素可使用`currentStyle`属性来获取一样的计算样式表。
  - `IE`独有的属性
  - 最终的计算样式的值以原样返回，不经过转换处理。

封装兼容性方法：获取元素的计算样式`getStyle`

```js
// 获取DOM元素指定的计算样式
function getStyle (elem, prop) {
    if (window.getComputedStyle) {
        return window.getComputedStyle(elem, null)[prop];
    }else {
        return elem.currentStyle[prop];
    }
}
getStyle(div, "left");
```

##### 获取伪元素计算样式表

`window.getComputedStyle()`方法的第二个参数用来指定元素的伪元素，从而来获取伪元素的计算样式。

使用方法：`window.getComputedStyle(elem, "after")[prop] // 查询elem的after伪元素的prop样式`

##### 间接修改伪元素样式

虽然能够使用`window.getComputedStyle()`方法，获取伪元素的计算样式，但无法修改样式。

间接修改：先将伪元素的样式在`CSS`文件中定义好，通过`JS`切换元素类名的方式来修改伪元素的样式呈现。

案例：更换伪元素的呈现样式

```html
<style>
    div {
        float : left;
        width : 10em;
        height : 100px;
        background-color : red;
    }
    .green::after {
        content : "";
        width : 100px;
        height : 50px;
        background-color : green;
    }
    .yellow::after {
        content : "";
        width : 100px;
        height : 50px;
        background-color : yellow;
    }
</style>
<div class = "green"></div>
<script>
	let div = document.getElementsByTagName("div")[0];
    div.onclick = function () {
        this.className = "yellow";
    }
</script>
```

注意：事先声明好待用样式，然后通过`JS`切换类名来改变页面呈现的方式，比一句一句地使用`JS`代码来修改样式更加实用。

- 节约性能：每一次调用`style`修改样式，都要进行一次通信。而一次性修改节约了相当多的性能。
- 便于维护：当需要修改待用样式时，直接在`CSS`文件中修改即可，且符合结构、样式、行为相分离的开发规范。

### 练习

练习：让小方块运动起来

注意：`HTML`元素的定位偏移量默认值为`auto`，如：`left`，`top`，需要手动设置运动的起始位置。

```html
<div style = "width : 50px; height : 50px; position : absolute; left : 0px; top : 0px; background-color : red;"></div>
<script>
	let div = document.getElementsByTagName("div")[0];
    function getStyle (elem, prop) {
        if (window.getComputedStyle) {
            return window.getComputedStyle(elem, null)[prop];
        }else {
            return elem.currentStyle[prop];
        }
    }
    let flag = true;
    div.onclick = function () {
        if (flag) {
            flag = false;
            let speed = 3,
            	timer = setInterval(function () {
                    // speed += speed / 1; 自定义速度
                    div.style.left = parseInt(getStyle(div, "left")) + speed + "px";
                    if (parseInt(div.style.left) > 500) {
                        clearInterval(timer);
                        flag = true;
                        // div.style.left = "0px"; 回到初始状态。
                	}
            	}, 10);
        	// div.style.left = "0px"; 同步执行代码，下一次点击回到初始状态后执行。
        }
    }
</script>
```




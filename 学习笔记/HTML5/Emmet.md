# `Emmet`

### 前言

​		`Emmet` 是一款文本编辑器 `IDE` 的插件，用来快速生成复杂的 `HTML` 代码，只要掌握一些常用的语法（类似于 `CSS` 选择器），就可以减少重复编码的工作，真的提升开发效率之利器。

​		所有的操作都是按下 `tab` 键即可瞬间完成。



### 相关语法

##### 添加类名

​		用 `.` 来添加类名：

```html
div.aaa
```

​		按 `tab` 后生成如下：

```html
<div class="aaa"></div>
```

​		输入

```html
p.class1.class2.class3
```

​		输出

```html
<p class="class1 class2 class3"></p>
```

##### 添加 `id`

​	用 `#` 来添加 `id`

```html
div#aaa
```

​		按 `tab`

```html
<div id="aaa"></div>
```

##### 添加属性

​		用 `[]` 来添加属性

```html
div[title='hello' colspan=3]
```

​		生成：

```html
<div title="hello" colspan="3"></div>
```

##### 批量生成

用 `*n` 可以一次性生成多个相同元素

```html
div.item*5
```

​		生成

```html
<div class="item"></div>
<div class="item"></div>
<div class="item"></div>
<div class="item"></div>
<div class="item"></div>
```

##### 快速编号

​		用 `$` 来生成编号（主要为批量生成的元素编号），实际上就是个占位符 （很实用，写几个 `$` 默认会有几位的数字）。

```html
li.aaa$*3
```

​		结果

```html
<li class="aaa1"></li>
<li class="aaa2"></li>
<li class="aaa3"></li>
```

​		输入

```html
h$[title=item$]{Header $}*3
```

​		生成

```html
<h1 title="item1">Header 1</h1>
<h2 title="item2">Header 2</h2>
<h3 title="item3">Header 3</h3>
```

​		输入

```html
ul>li.item$$$*5
```

​		生成

```html
<ul>
        <li class="item001"></li>
        <li class="item002"></li>
        <li class="item003"></li>
        <li class="item004"></li>
        <li class="item005"></li>
</ul>
```

##### 改起始值

​		用 `@n` 来修改编号的起始值（负值代表数字倒序，仅写 `-` 代表 `-1`，其他值要写出对应的 `-2`、`-3` 等，负几无论生成的数量是几，最后一个元素的数字一定是几，比如 `-3` ，则最后一个元素的数字是 `3`）。

​		正序

```html
li.aaa$@3*3
```

​		生成：

```html
<li class="aaa3"></li>
<li class="aaa4"></li>
<li class="aaa5"></li>
```

​		倒序

```html
li.aaa$@-3*3
```

​		生成

```html
<li class="aaa5"></li>
<li class="aaa4"></li>
<li class="aaa3"></li>
```

#####  添加文本

​		`{}` 可以添加文本

​		输入：

```html
div.container>div.item.item${测试 $}*8
```

​		生成：

```html
<div class="container">
      <div class="item item1">测试 1</div>
      <div class="item item2">测试 2</div>
      <div class="item item3">测试 3</div>
      <div class="item item4">测试 4</div>
      <div class="item item5">测试 5</div>
      <div class="item item6">测试 6</div>
      <div class="item item7">测试 7</div>
      <div class="item item8">测试 8</div>
</div> 
```

​		输入

```html
p>{Click }+a{here}+{ to continue}
```

​		生成

```html
<p>Click <a href="">here</a> to continue</p>
```

##### 添加子元素

​		用 `>` 生成子元素

```html
div>ul>li
```

​		结果

```html
<div>
     <ul>
         <li></li>
     </ul>
</div>
```

##### 添加兄弟元素

​		用 `+` 生成兄弟元素

```html
div.box-left+div.box-right
```

​		生成

```html
<div class="box-left"></div>
<div class="box-right"></div>
```

##### 返回上一级

用 `^` 向上返回一级，回到父元素中。比如写了 `>` 进入了子级，输入这个 `^` 就可以退出到上一级继续输入。

```html
div+div>p>span+em^bq
```

​		生成

```html
<div></div>
<div>
	<p><span></span><em></em></p>
	<blockquote></blockquote>
</div>
```

##### 元素分组

用 `()` 将元素分组 （主要作用是省去使用 `^` 去到上一层级的符号，用括号直接代替）

​		输入

```html
div>(header>ul>li*2>a)+footer>p
```

​		生成

```html
<div>
	<header>
        <ul>
            <li><a href=""></a></li>
            <li><a href=""></a></li>
        </ul>
    </header>
    <footer>
        <p></p>
    </footer>
</div>
```

​		输入

```html
(div>dl>(dt+dd)*3)+footer>p
```

​		生成

```html
<div>
    <dl>
        <dt></dt>
        <dd></dd>
        <dt></dt>
        <dd></dd>
        <dt></dt>
        <dd></dd>
    </dl>
</div>
<footer>
    <p></p>
</footer>
```

 

### 隐式标签

​		输入

```html
.item
```

​		生成

```html
<div class="item"></div>
```

​		输入

```html
em>.item
```

​		生成

```html
<em><span class="item"></span></em>
```

​		输入

```html
ul>.item
```

​		生成

```html
<ul>
	<li class="item"></li>
</ul>
```

​		输入

```html
table>.row>.col
```

​		生成

```html
<table>
    <tr class="row">
        <td class="col"></td>
    </tr>
</table>
```



### 未知标签

​		所有未知的缩写都会转换成标签，例如 `foo` → `<foo></foo>`



### 基础结构

输入 **`!`** 

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
    </head>
    <body></body>
</html>
```

 

### 参考链接

- https://www.jianshu.com/p/9352a0411fcb


- http://t.zoukankan.com/haoqirui-p-12257215.html




### 官方文档

- https://docs.emmet.io/cheat-sheet/


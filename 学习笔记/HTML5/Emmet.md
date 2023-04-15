# [Emmet快速生成HTML代码的常用语法总结](https://www.cnblogs.com/beileixinqing/p/16383085.html)

### 前言

Emmet是一款文本编辑器/IDE的插件，用来快速生成复杂的HTML代码，只要掌握一些常用的语法（类似于CSS选择器），就可以减少重复编码的工作，真的提升开发效率之利器。

所有的操作都是按下tab键即可瞬间完成。

### 一、相关语法

**1. 用.来生成类名**

```
div.aaa
```

按tab后生成如下：

```
<div class="aaa"></div>
```

输入

```
p.class1.class2.class3
```

输出

```
<p class="class1 class2 class3"></p>
```

**2. id用#**

```
div#aaa
```

按tab

```
<div id="aaa"></div>
```

**3. 属性用 []**

```
div[title='hello' colspan=3]
```

生成：

```
<div title="hello" colspan="3"></div>
```

**4. 用$来实现编号,实际上就是个占位符 （很实用, 写几个$ 默认会有几位的数字）**

```
li.aaa$*3
```

结果

```
<li class="aaa1"></li>
<li class="aaa2"></li>
<li class="aaa3"></li>
```

输入

```
h$[title=item$]{Header $}*3
```

生成

```
<h1 title="item1">Header 1</h1>
<h2 title="item2">Header 2</h2>
<h3 title="item3">Header 3</h3>
```

输入

```
ul>li.item$$$*5
```

生成

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

```
<ul>
        <li class="item001"></li>
        <li class="item002"></li>
        <li class="item003"></li>
        <li class="item004"></li>
        <li class="item005"></li>
</ul>
```

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

 

**5. 用@n来修改起始值（赋值代表数字倒序，仅写 - 代表 -1，其他值 要写出对应的 -1,-2,-3 等，负几无论生成的数量是几，最后一个元素的数字就是几，比如 -3 ，则最后一个元素的 数字是3 ）**

```
li.aaa$@3*3
```

生成：

```
<li class="aaa3"></li>
<li class="aaa4"></li>
<li class="aaa5"></li>
```

 3可以用-3，就会变成 5，4，3

输入

```
li.aaa$@-3*3
```

生成

```
<li class="aaa5"></li>
<li class="aaa4"></li>
<li class="aaa3"></li>
```

 

**6. {} 可以添加文本 (这个非常实用！！！ 在想要写一些测试demo 写多个元素 分别为1,2,3,4 之类的 可以瞬间生成 !)**

输入：

 div.container>div.item.item${测试 $}*8

生成：

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

```
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

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

输入

```
p>{Click }+a{here}+{ to continue}
```

生成

```
<p>Click <a href="">here</a> to continue</p>
```

**7. > 生成子元素**

```
div>ul>li
```

结果

```
<div>
     <ul>
         <li></li>
     </ul>
</div>
```

**8. + 生成兄弟元素**

输入

```
div.box-left+div.box-right
```

生成

```
<div class="box-left"></div>
<div class="box-right"></div>
```

**9. ^ 向上一级，比如写了> 进入了子级，输入这个 ^ 就可以退出到上一级继续输入**

```
div+div>p>span+em^bq
```

生成

```xml
<div></div>
    <div>
        <p><span></span><em></em></p>
        <blockquote></blockquote>
</div>
```

**10. \*n 生成多个相同元素**

```
div.item*5
```

生成

```
<div class="item"></div>
<div class="item"></div>
<div class="item"></div>
<div class="item"></div>
<div class="item"></div>
```

**11. () 将元素分组 （主要作用是省去使用 ^ 去到上一层级的符号，括号直接代替）**

输入

```
div>(header>ul>li*2>a)+footer>p
```

生成

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

```
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

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

输入

```
(div>dl>(dt+dd)*3)+footer>p
```

生成

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

```
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

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

 

### 二、隐式标签

输入

```
  .item
```

生成

```
<div class="item"></div>
```

输入

```
em>.item
```

生成

```
<em><span class="item"></span></em>
```

输入

```
ul>.item
```

生成

```
<ul>
      <li class="item"></li>
</ul>
```

输入

```
table>.row>.col
```

生成

```
 <table>
      <tr class="row">
        <td class="col"></td>
      </tr>
</table>
```

### 三、html标签

所有未知的缩写都会转换成标签，例如，foo → <foo></foo>

### 四、生成html页面基础结构【非常重要，非常好用】

输入 **!** 

生成

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

```
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

[![复制代码](images/Emmet/copycode.gif)](javascript:void(0);)

 

### 参考链接：

https://www.jianshu.com/p/9352a0411fcb

http://t.zoukankan.com/haoqirui-p-12257215.html

### 官方文档

https://docs.emmet.io/cheat-sheet/
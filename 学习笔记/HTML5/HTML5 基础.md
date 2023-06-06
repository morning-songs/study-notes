# `HTML5`

### 初识		

​		超文本标记语言（英语：`HyperText Markup Language`，简称：`HTML`）是一种用于创建网页的标准标记语言。您可以使用 `HTML` 来建立自己的 `WEB` 站点，`HTML` 运行在浏览器上，由浏览器来解析。

#### 实例

​		以下是一个 `HTML` 实例：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>网页标题</title>
</head>
<body>
    <h1>第一个文章标题</h1>
    <p>第一个文章段落。</p>
</body>
</html>
```

​		实例解析：<img src="images/HTML5%20%E5%9F%BA%E7%A1%80/02A7DD95-22B4-4FB9-B994-DDB5393F7F03.jpg" alt="img" style="zoom: 67%;" /> 

- `<!DOCTYPE html>` 用于将此文档声明为 `HTML5` 文档
- `<html>` 元素是 `HTML` 页面的根元素
- `<head>` 元素包含了文档的元（`meta`）数据，如 `<meta charset="utf-8">` 定义网页编码格式为 **`utf-8`**。
- `<title>` 元素描述了文档的标题
- `<body>` 元素包含了可见的页面内容
- `<h1>` 元素定义一个大标题
- `<p>` 元素定义一个段落

**注意：**对于中文网页需要使用 `<meta charset="utf-8">` 声明编码，否则会出现乱码。有些浏览器（如 360 浏览器）会设置 `GBK` 为默认编码，则你需要设置为 `<meta charset="gbk">`。换句话说，**文档的编码和 `meta` 标签设置的编码一定要相同，只要不相同，最终浏览器显示时就一定会出现乱码！**

#### 构成

​		一个 `HTML` 文档，由两个标签组成。一个声明文档类型（`<!DOCTYPE>`），一个定义文档内容（`<html>`）。声明见下节。

​		一个文档的内容，包括元数据和主体两个部分。于是，`<html>` 标签有两个子标签 `<head>` 和 `<body>`。其中，`<head>` 用于定义文档的元数据，`<body>` 用于定义文档的主体。

##### `<html>` 根元素

​		`<html>` 是文档的根元素，囊括了一个文档的所有内容。`<html>` 元素用于告诉告诉浏览器其自身是一个 `HTML` 文档。

**定义和用法**

​		`<html>` 标签告知浏览器这是一个 `HTML` 文档。

​		`<html>` 标签是 `HTML` 文档中最外层的元素。

​		`<html>` 标签是所有其他 `HTML` 元素（除了 `<!DOCTYPE>` 标签）的容器。

​		它具有以下属性。

|    属性    |               值               | 描述                                                         |
| :--------: | :----------------------------: | ------------------------------------------------------------ |
|   `lang`   |     `en`、`zh-CN`、`fr` 等     | 定义此文档所使用的语言                                       |
| `manifest` |             `URL`              | 定义一个 `URL`，在这个 `URL` 上描述文档的缓存信息。          |
|  `xmlns`   | `http://www.w3.org/1999/xhtml` | 规定 `XML` 的 `namespace` 属性，即指派文档的 `XML` 命名空间（如果您需要您的内容符合 `XHTML`，则使用这个属性）。`HTML` 不支持。只有 `XHTML` 支持。 |

注释：`<html>` 标签支持 `HTML` 的全局属性，支持全局事件属性。

​		`HTML5` 中，增加了两个新属性：`manifest` 和 `xmlns`。`xmlns` 属性在 `XHTML` 中是必需的，但在 `HTML`中不是。然而，即使 `XHTML` 文档中的 `<html>` 没有使用 `xmlns` 属性，`W3C` 上的 `HTML` 验证器也不会报错。这是因为 `"xmlns=http://www.w3.org/1999/xhtml"` 是一个固定值，即使您没有包含它，此值也会被添加到 `<html>` 标签中。

```css
html[Attributes Style] {
    -webkit-locale: "en";
}
html {
    display: block;
}
```

**扩展**

​		`<html>` 元素 表示一个 `HTML` 文档的根（顶级元素），所以它也被称为根元素，所有其他元素必须是此元素的后代。

​		`lang` 属性主要用于提高文档的可访问性。在 `html` 元素上提供具有有效 `IETF` 标识语言标记的 `lang` 属性，将有助于屏幕阅读技术确定要陈述的正确语言。标识语言标签应描述页面大部分内容使用的语言。没有它，屏幕阅读器通常会默认使用操作系统的设置语言，这可能会导致错误陈述。

​		尽管在 `HTML` 里 `<html>` 元素不是必需的，可以是隐含的，但是在 `XHTML` 里必须明确给出它的开标签和闭标签。

​		严格意义上，标签是指开始标签（例如 `<p>` 标签）或结束标签（例如 `</p>` 标签）；元素（例如 `p` 元素或者称为 `<p>` 元素）则包括开始标签（自然也包括标签中定义的属性）、结束标签以及标签中间的内容（`Content`）。

**总结**

​		1、`html` 元素用于告诉告诉浏览器其自身是一个 `HTML` 文档。

​        2、`<html>` 与 `</html>` 标签限定了文档的开始点和结束点，在它们之间是文档的头部和主体。

​        3、`<html>` 标签是所有其他 `HTML` 元素（除了 `<!DOCTYPE>` 标签）的容器，即使 `html` 元素是文档的根元素，它也不包含 `doctype` 元素。`doctype` 元素必须位于 `html` 元素之前。

​        4、`xmlns` 属性在 `XHTML` 中是必需的，但在 `HTML`中不是。

​        5、拥有 3 个属性：`manifest`、`lang`、`xmlns`。

##### `<head>` 文档头部

**定义和用法**

​		`<head>` 标签用于定义文档的头部，它是所有头部元素的容器。

​		`<head>`中的元素可以引用脚本、指示浏览器在哪里找到样式表、提供元信息等等。 

​		文档的头部描述了文档的各种属性和信息，包括文档的标题、在 `Web` 中的位置以及和其他文档的关系等。

​		绝大多数文档头部包含的数据都不会真正作为内容显示给读者。

​		子元素：`<base>, <link>, <meta>, <script>, <style>` 以及 `<title>`。

​		`<title>`定义文档的标题，它是 `head` 部分中唯一必需的元素。

**可选的属性**

|   属性    |   值    | 描述                                                         |
| :-------: | :-----: | :----------------------------------------------------------- |
| `profile` | *`URL`* | 一个由空格分隔的 `URL` 列表，这些 `URL` 包含着有关页面的元数据信息。 |

注释：`<head>` 标签支持 `HTML` 的全局属性，但不支持事件属性。

**`profile` 属性的更多信息**

​		文档的头部经常会包含一些 `<meta>` 标签，用来告诉浏览器关于文档的附加信息。

​		在将来，创作者可能会利用预先定义好的标准文档的元数据配置文件（`metadata profile`），以便更好地描述它们的文档。

​		`profile` 属性提供了与当前文档相关联的配置文件的 `URL`。

**提示**：配置文件的格式以及浏览器使用它们的方式都还没有进行定义，这个属性主要是为将来的开发而保留的占位符。

```css
head {
    display: none;
}
```

##### `<body>` 文档主体

**定义和用法**

​		`body` 元素定义文档的主体。

​		`body` 元素包含文档的所有内容（比如文本、超链接、图像、表格和列表等等）。

​		在 `HTML 5` 中，删除了所有 `body` 元素的 "呈现属性"。

注释：`<body>` 标签支持 `HTML` 的全局属性，支持事件属性（包括全局的和特有的）。

```css
body {
    display: block;
    margin: 8px;
}
```

**扩展**

​		`body` 元素表示文档的内容。`document.body` 属性提供了可以轻松访问文档的 `body` 元素的脚本。

​		此元素包含全局属性（`HTML`），且支持事件属性。

​		呈现属性（`CSS`）如下

| 呈现属性（`CSS`） | 描述                                                         |
| :---------------: | :----------------------------------------------------------- |
|      `alink`      | 超链接选中之后的文本颜色。此方法不符合规范，请使用 `CSS` 的 `color` 属性和 `:active` 伪类替代。 |
|   `background`    | 将 `URI` 所指向的图片作为背景。此方法不符合规范，请使用 `CSS` 的 `background` 属性替代。 |
|     `bgcolor`     | 文档的背景颜色。此方法不符合规范，请使用 `CSS` 的 `background-color` 属性替代。 |
|  `bottommargin`   | `body` 的底外边距。此方法不符合规范，请使用 `CSS` 的 `margin-bottom` 属性替代。 |
|   `leftmargin`    | `body` 的左外边距。此方法不符合规范，请使用 `CSS` 的 `margin-left` 属性替代。 |
|      `link`       | 未访问过的超链接文本颜色。此方法不符合规范，请使用 `CSS` 的 `color` 属性和 `:link` 伪类替代。 |
|   `rightmargin`   | `body` 的右外边距。此方法不符合规范，请使用 `CSS` 的 `margin-right` 属性替代。 |
|      `text`       | 文本颜色。此方法不符合规范，请使用 `CSS` 的 `color` 属性替代。 |
|    `topmargin`    | `body` 的上外边距。此方法不符合规范，请使用 `CSS` 的 `margin-top` 属性替代。 |
|      `vlink`      | 访问过的超链接的文本颜色。 此方法不符合规范，请使用 `CSS` 的 `color` 属性和 `:visited` 伪类替代。 |

​		事件属性如下

|  事件属性（`JS`）  | 描述                                                         |
| :----------------: | ------------------------------------------------------------ |
|   `onafterprint`   | 用户完成文档打印之后调用的函数。                             |
|  `onbeforeprint`   | 用户要求打印文档之前调用的函数。                             |
|  `onbeforeunload`  | 文档即将被关闭之前调用的函数。                               |
|      `onblur`      | 文档失去焦点时调用的函数。                                   |
|     `onerror`      | 文档加载失败时调用的函数。                                   |
|     `onfocus`      | 文档获得焦点时调用的函数。                                   |
|   `onhashchange`   | 文档当前地址的片段标识部分（以 `'#'` 开始的部分）发生改变时调用的函数。 |
| `onlanguagechange` | 用户选择的语言发生改变时调用的函数。                         |
|      `onload`      | 文档完成加载时调用的函数。                                   |
|    `onmessage`     | 文档接收到消息时调用的函数。                                 |
|    `onoffline`     | 网络连接失败时调用的函数。                                   |
|     `ononline`     | 网络连接恢复时调用的函数。                                   |
|    `onpopstate`    | 用户回退历史记录时调用的函数。                               |
|      `onredo`      | 用户重做操作时调用的函数。                                   |
|     `onresize`     | 文档尺寸发生改变时调用的函数。                               |
|    `onstorage`     | 存储内容（`localStorage` / `sessionStorage`）发生改变时调用的函数。 |
|      `onundo`      | 用户撤销操作时调用的函数。                                   |
|     `onunload`     | 文档关闭时调用的函数。                                       |

总结

​        1、`<body>` 标签定义文档的主体。

​        2、`<body>` 元素包含文档的所有内容（比如文本、超链接、图像、表格和列表等等）。

​        3、在 `HTML 5` 中，删除了所有 `body` 元素的 "呈现属性"。

​        4、`document.body` 属性提供了可以轻松访问文档的 `body` 元素的脚本。

​        5、此元素包含全局属性，支持事件属性。

#### 后缀

​		`HTML` 文档的后缀名：

- **`.html`**
- **`.htm`**

​		以上两种后缀名没有区别，都可以使用。



### 简介

#### 语言

​		`HTML` 是用来描述网页的一种语言。

- `HTML` 指的是超文本标记语言：`Hyper Text Markup Language`
- `HTML` 不是一种编程语言，而是一种 **标记** 语言
- 标记语言是一套 **标记标签**（`markup tag`）
- `HTML` 使用标记标签来 **描述** 网页
- `HTML` 文档包含了`HTML` **标签** 及 **文本** 内容
- `HTML` 文档也叫做 **`web` 页面**

#### 标签

​		`HTML` 标记标签通常被称为 `HTML` 标签（`HTML tag`）。

- `HTML` 标签是由一对尖括号包围的关键词，比如 `<html>`
- `HTML` 标签通常是成对出现的，比如 `<b>` 和 `</b>`
- 标签对中的第一个标签是开始标签，第二个标签是结束标签
- 开始和结束标签也被称为开放标签和闭合标签。
- 在开始标签和结束标签之间的是该标签的内容。

```html
<标签>该标签的内容</标签>
```

​		下表列出了 `HTML` 标签简写及全称：

|     标签      |          英文全称           | 中文说明                       |
| :-----------: | :-------------------------: | :----------------------------- |
|      `a`      |          `Anchor`           | 锚                             |
|    `abbr`     |       `Abbreviation`        | 缩写词                         |
|   `acronym`   |          `Acronym`          | 取首字母的缩写词               |
|   `address`   |          `Address`          | 地址                           |
|     `alt`     |           `alter`           | 替用(一般是图片显示不出的提示) |
|      `b`      |           `Bold`            | 粗体（文本）                   |
|     `bdo`     |  `Bi-Directional Override`  | 文本显示方向                   |
|     `big`     |            `Big`            | 变大（文本）                   |
| `blockquote`  |      `Block Quotation`      | 区块引用语                     |
|     `br`      |           `Break`           | 换行                           |
|    `cell`     |           `cell`            | 巢                             |
| `cellpadding` |        `cellpadding`        | 巢补白                         |
| `cellspacing` |        `cellspacing`        | 巢空间                         |
|   `center`    |         `Centered`          | 居中（文本）                   |
|    `cite`     |         `Citation`          | 引用                           |
|    `code`     |           `Code`            | 源代码（文本）                 |
|     `dd`      |  `Definition Description`   | 定义描述                       |
|     `del`     |          `Deleted`          | 删除（的文本）                 |
|     `dfn`     | `Defines a Definition Term` | 定义定义条目                   |
|     `div`     |         `Division`          | 分隔                           |
|     `dl`      |      `Definition List`      | 定义列表                       |
|     `dt`      |      `Definition Term`      | 定义术语                       |
|     `em`      |        `Emphasized`         | 加重（文本）                   |
|    `font`     |           `Font`            | 字体                           |
|   `h1 ~ h6`   |   `Header 1 to Header 6`    | 标题 1 到标题 6                |
|     `hr`      |      `Horizontal Rule`      | 水平尺                         |
|    `href`     |    `hypertext reference`    | 超文本引用                     |
|      `i`      |          `Italic`           | 斜体（文本）                   |
|   `iframe`    |       `Inline frame`        | 定义内联框架                   |
|     `ins`     |         `Inserted`          | 插入（的文本）                 |
|     `kbd`     |         `Keyboard`          | 键盘（文本）                   |
|     `li`      |         `List Item`         | 列表项目                       |
|     `nl`      |     `navigation lists`      | 导航列表                       |
|     `ol`      |       `Ordered List`        | 排序列表                       |
|  `optgroup`   |       `Option group`        | 定义选项组                     |
|      `p`      |         `Paragraph`         | 段落                           |
|     `pre`     |       `Preformatted`        | 预定义格式（文本 ）            |
|      `q`      |         `Quotation`         | 引用语                         |
|     `rel`     |          `Reload`           | 加载                           |
| `s / strike`  |       `Strikethrough`       | 删除线                         |
|    `samp`     |          `Sample`           | 示例（文本                     |
|    `small`    |           `Small`           | 变小（文本）                   |
|    `span`     |           `Span`            | 范围                           |
|     `src`     |          `Source`           | 源文件链接                     |
|   `strong`    |          `Strong`           | 加重（文本）                   |
|     `sub`     |        `Subscripted`        | 下标（文本）                   |
|     `sup`     |       `Superscripted`       | 上标（文本）                   |
|     `td`      |      `table data cell`      | 表格中的一个单元格             |
|     `th`      |     `table header cell`     | 表格中的表头                   |
|     `tr`      |         `table row`         | 表格中的一行                   |
|     `tt`      |         `Teletype`          | 打印机（文本）                 |
|      `u`      |        `Underlined`         | 下划线（文本）                 |
|     `ul`      |      `Unordered List`       | 不排序列表                     |
|     `var`     |         `Variable`          | 变量（文本）                   |

##### 分类

​		`HTML` 标签根据 `CSS` 的 `display` 表现可以分为三类：

- 块级标签（`block`）：标签作为一个块级区域存在，表现为独占一行（强行将内容断开），可设宽高。
- 行内标签（`inline`）：标签作为一个行内区域存在，表现为嵌入行中，不可设宽高（宽高由内容撑开）。
- 行内块标签（`inline-block`）：标签作为一个行内块区域存在，表现为嵌入行中，但可设宽高。

##### 关系

​		`HTML` 标签有两种关系：嵌套关系和并列关系。

​		标签之间可以并列存在，也可以互相嵌套使用。并列是最基础的关系，而嵌套需要遵守一些规则：

- 块级标签：可以放置所有行内标签和行内块标签，但对于块级标签情况不同：
  - 文本类：不可放置任何块级标签（因为强制断开内容，元素会意外闭合）。
  - 容器类：可以放置任何块级标签（容器用于容纳内容，以方便页面布局）。
- 行内标签：只能放置行内标签和行内块标签。
- 行内块标签：兼具行内标签和块级标签特性。

#### 元素

​		"`HTML` 标签" 和 "`HTML` 元素" 通常被描述为同样的意思。但严格来讲, 一个 `HTML` 元素包含了开始标签、结束标签以及标签的内容。

```html
<h1>文章标题</h1>
```

​		上述代码中：`<h1>` 是开始标签，`</h1>` 是结束标签，“文章标题” 是标签内容，而 `<h1>文章标题</h1>` 才是一个 `HTML` 元素。

##### 空格解析

​		为精简文档，文档中元素与元素或元素与内容之间的所有空格（`space`）都会被解析为一个空格。

#### 浏览器

​		`Web` 浏览器（如谷歌浏览器，`Internet Explorer`，`Firefox`，`Safari`）是用于读取 `HTML` 文件并将其作为网页显示的工具。浏览器并不是直接显示 `HTML` 标签，而是通过解析标签来决定如何展现标签内容（或资源）以及如何构成 `HTML` 页面并将其展示给用户。

#### 网页

​		一个基础的 `HTML` 页面（**不包括 `<!DOCTYPE>`**）结构如下所示：

​		<img src="images/HTML5%20%E5%9F%BA%E7%A1%80/image-20230603110739692.png" alt="image-20230603110739692" style="zoom:80%;" /> 

注释：

- `<html>` 是一个大容器，用于容纳整个文档的内容。
- `<head>` 用于提供该文档的元信息，其内容是不可见的，它相当于一个文档的 **思想**（用于告诉浏览器如何解析该文档）。
- `<body>` 用于提供该文档的主体内容，其内容是可见的，它相当于一个文档的 **身体**（用于告诉浏览器该文档外显内容）。

> 元信息：
>
> ​		元信息（`meta data`），也称元数据。元是本原、体系的意思，元数据则被定义为 “描述数据的数据”。例如，一本书籍是一条数据，那么描述该书籍的数据就是元数据（如：书名、作者、出版社、出版日期、字数、页数、简介等等）。

#### 版本

​		从初期的网络诞生后，已经出现了许多 `HTML` 版本：

|    版本     | 发布时间 |
| :---------: | :------: |
|   `HTML`    |   1991   |
|   `HTML+`   |   1993   |
| `HTML 2.0`  |   1995   |
| `HTML 3.2`  |   1997   |
| `HTML 4.01` |   1999   |
| `XHTML 1.0` |   2000   |
|   `HTML5`   |   2012   |
|  `XHTML5`   |   2013   |

#### 声明

​		所有主流浏览器都支持 `<!DOCTYPE>` 声明，用于声明当前文档的类型，有助于浏览器正确显示该网页，相当于一个文档的序言。

##### 说明

​		`<!DOCTYPE>` 声明位于文档中的最前面的位置，处于 `<html>` 标签之前。

​		`<!DOCTYPE>` 声明不是一个 `HTML` 标签；它是用来告知 `Web` 浏览器当前页面使用了哪个版本的 `HTML` 语法。

​		在 `HTML 4.01` 中，`<!DOCTYPE>` 声明需引用 `DTD`（文档类型声明），因为 `HTML 4.01` 是基于 `SGML`（`Standard Generalized Markup Language`，标准通用标记语言）的。`DTD` 指定了标记语言的规则，确保了浏览器能够正确的渲染内容。

​		`HTML5` 不是基于 `SGML` 的，因此不要求引用 `DTD`。

​		总是给您的 `HTML` 文档添加 `<!DOCTYPE>` 声明，以确保浏览器能够预先知道此文档的类型。

##### 差异

​		`HTML 4.01` 规定了三种不同的 `<!DOCTYPE>` 声明，分别是：`Strict`、`Transitional` 和 `Frameset`。 `HTML5` 仅规定了一种。

##### 使用

​		`<!DOCTYPE>` 标签没有结束标签。`<!DOCTYPE>` 声明不区分大小写。使用 [`W3C` 的验证](https://validator.w3.org/) 检查您是否编写了一个带有正确 `DTD` 的合法的 `HTML / XHTML` 文档！

​		网络上有很多不同的文件，如果能够正确声明 `HTML` 的版本，浏览器就能正确显示网页内容。`doctype` 声明是不区分大小写的，以下方式均可：

```html
<!DOCTYPE html>

<!DOCTYPE HTML>

<!doctype html>

<!Doctype Html>
```

​		`HTML5`

```html
<!DOCTYPE html>
```

​		`HTML 4.01 Strict`

​		这个 `DTD` 包含所有 `HTML` 元素和属性，但不包括表象或过时的元素（如 `font` ）。框架集是不允许的。

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
```

​		`HTML 4.01 Transitional`

​		这个 `DTD` 包含所有 `HTML` 元素和属性，包括表象或过时的元素（如 `font` ）。框架集是不允许的。

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
```

​		`HTML 4.01 Frameset`

​		这个 `DTD` 与 `HTML 4.01 Transitional` 相同，但是允许使用框架集内容。

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">
```

​		`XHTML 1.0 Strict`

​		这个 `DTD` 包含所有 `HTML` 元素和属性，但不包括表象或过时的元素（如 `font` ）。框架集是不允许的。结构必须按标准格式的 `XML` 进行书写。

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```

​		`XHTML 1.0 Transitional`

​		这个 `DTD` 包含所有 `HTML` 元素和属性，包括表象或过时的元素（如 `font` ）。框架集是不允许的。结构必须按标准格式的 `XML` 进行书写。

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

​		`XHTML 1.0 Frameset`

​		这个 `DTD` 与 `XHTML 1.0 Transitional` 相同，但是允许使用框架集内容。

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
```

​		`XHTML 1.1`

​		这个 `DTD` 与 `XHTML 1.0 Strict` 相同，但是允许您添加模块（例如为东亚语言提供 `ruby` 支持）。

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
```

##### 元素

​		下面的表格列出了所有的 `HTML5 / HTML 4.01 / XHTML` 元素，以及它们会出现在什么文档类型（`DOCTYPE`）中：

|                                                              |         |  `HTML 4.01`   |    和    | `XHTML 1.0` |             |
| :----------------------------------------------------------: | :-----: | :------------: | :------: | :---------: | :---------: |
|                            `Tag`                             | `HTML5` | `Transitional` | `Strict` | `Frameset`  | `XHTML 1.1` |
|       [`<a>`](https://www.runoob.com/tags/tag-a.html)        |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|    [`<abbr>`](https://www.runoob.com/tags/tag-abbr.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<acronym>`](https://www.runoob.com/tags/tag-acronym.html)  | **No**  |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<address>`](https://www.runoob.com/tags/tag-address.html)  |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<applet>`](https://www.runoob.com/tags/tag-applet.html)   | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
|    [`<area>`](https://www.runoob.com/tags/tag-area.html)     |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
| [`<article>`](https://www.runoob.com/tags/tag-article.html)  |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|   [`<aside>`](https://www.runoob.com/tags/tag-aside.html)    |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|   [`<audio>`](https://www.runoob.com/tags/tag-audio.html)    |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|       [`<b>`](https://www.runoob.com/tags/tag-b.html)        |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|    [`<base>`](https://www.runoob.com/tags/tag-base.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<basefont>`](https://www.runoob.com/tags/tag-basefont.html) | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
|     [`<bdi>`](https://www.runoob.com/tags/tag-bdi.html)      |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|     [`<bdo>`](https://www.runoob.com/tags/tag-bdo.html)      |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
|     [`<big>`](https://www.runoob.com/tags/tag-big.html)      | **No**  |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<blockquote>`](https://www.runoob.com/tags/tag-blockquote.html) |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|    [`<body>`](https://www.runoob.com/tags/tag-body.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<br>`](https://www.runoob.com/tags/tag-br.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<button>`](https://www.runoob.com/tags/tag-button.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<canvas>`](https://www.runoob.com/tags/tag-canvas.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
| [`<caption>`](https://www.runoob.com/tags/tag-caption.html)  |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<center>`](https://www.runoob.com/tags/tag-center.html)   | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
|    [`<cite>`](https://www.runoob.com/tags/tag-cite.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|    [`<code>`](https://www.runoob.com/tags/tag-code.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<col>`](https://www.runoob.com/tags/tag-col.html)      |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
| [`<colgroup>`](https://www.runoob.com/tags/tag-colgroup.html) |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
| [`<command>`](https://www.runoob.com/tags/tag-command.html)  |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
| [`<datalist>`](https://www.runoob.com/tags/tag-datalist.html) |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|      [`<dd>`](https://www.runoob.com/tags/tag-dd.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<del>`](https://www.runoob.com/tags/tag-del.html)      |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
| [`<details>`](https://www.runoob.com/tags/tag-details.html)  |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|     [`<dfn>`](https://www.runoob.com/tags/tag-dfn.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<dir>`](https://www.runoob.com/tags/tag-dir.html)      | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
|     [`<div>`](https://www.runoob.com/tags/tag-div.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<dl>`](https://www.runoob.com/tags/tag-dl.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<dt>`](https://www.runoob.com/tags/tag-dt.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<em>`](https://www.runoob.com/tags/tag-em.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<embed>`](https://www.runoob.com/tags/tag-embed.html)    |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
| [`<fieldset>`](https://www.runoob.com/tags/tag-fieldset.html) |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<figcaption>`](https://www.runoob.com/tags/tag-figcaption.html) |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|  [`<figure>`](https://www.runoob.com/tags/tag-figure.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|    [`<font>`](https://www.runoob.com/tags/tag-font.html)     | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
|  [`<footer>`](https://www.runoob.com/tags/tag-footer.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|    [`<form>`](https://www.runoob.com/tags/tag-form.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<frame>`](https://www.runoob.com/tags/tag-frame.html)    | **No**  |     **No**     |  **No**  |     Yes     |   **No**    |
| [`<frameset>`](https://www.runoob.com/tags/tag-frameset.html) | **No**  |     **No**     |  **No**  |     Yes     |   **No**    |
|  [`<h1> to <h6>`](https://www.runoob.com/tags/tag-hn.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|    [`<head>`](https://www.runoob.com/tags/tag-head.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<header>`](https://www.runoob.com/tags/tag-header.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|  [`<hgroup>`](https://www.runoob.com/tags/tag-hgroup.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|      [`<hr>`](https://www.runoob.com/tags/tag-hr.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|    [`<html>`](https://www.runoob.com/tags/tag-html.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|       [`<i>`](https://www.runoob.com/tags/tag-i.html)        |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<iframe>`](https://www.runoob.com/tags/tag-iframe.html)   |   Yes   |      Yes       |  **No**  |     Yes     |   **No**    |
|     [`<img>`](https://www.runoob.com/tags/tag-img.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<input>`](https://www.runoob.com/tags/tag-input.html)    |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<ins>`](https://www.runoob.com/tags/tag-ins.html)      |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
|     [`<kbd>`](https://www.runoob.com/tags/tag-kbd.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<keygen>`](https://www.runoob.com/tags/tag-keygen.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|   [`<label>`](https://www.runoob.com/tags/tag-label.html)    |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<legend>`](https://www.runoob.com/tags/tag-legend.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<li>`](https://www.runoob.com/tags/tag-li.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|    [`<link>`](https://www.runoob.com/tags/tag-link.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<map>`](https://www.runoob.com/tags/tag-map.html)      |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
|    [`<mark>`](https://www.runoob.com/tags/tag-mark.html)     |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|    [`<menu>`](https://www.runoob.com/tags/tag-menu.html)     |   Yes   |      Yes       |  **No**  |     Yes     |   **No**    |
|    [`<meta>`](https://www.runoob.com/tags/tag-meta.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<meter>`](https://www.runoob.com/tags/tag-meter.html)    |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|     [`<nav>`](https://www.runoob.com/tags/tag-nav.html)      |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
| [`<noframes>`](https://www.runoob.com/tags/tag-noframes.html) | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
| [`<noscript>`](https://www.runoob.com/tags/tag-noscript.html) |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<object>`](https://www.runoob.com/tags/tag-object.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<ol>`](https://www.runoob.com/tags/tag-ol.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<optgroup>`](https://www.runoob.com/tags/tag-optgroup.html) |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<option>`](https://www.runoob.com/tags/tag-option.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<output>`](https://www.runoob.com/tags/tag-output.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|       [`<p>`](https://www.runoob.com/tags/tag-p.html)        |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<param>`](https://www.runoob.com/tags/tag-param.html)    |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<pre>`](https://www.runoob.com/tags/tag-pre.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<progress>`](https://www.runoob.com/tags/tag-progress.html) |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|       [`<q>`](https://www.runoob.com/tags/tag-q.html)        |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<rp>`](https://www.runoob.com/tags/tag-rp.html)       |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|      [`<rt>`](https://www.runoob.com/tags/tag-rt.html)       |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|    [`<ruby>`](https://www.runoob.com/tags/tag-ruby.html)     |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|       [`<s>`](https://www.runoob.com/tags/tag-s.html)        |   Yes   |      Yes       |  **No**  |     Yes     |   **No**    |
|    [`<samp>`](https://www.runoob.com/tags/tag-samp.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<script>`](https://www.runoob.com/tags/tag-script.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<section>`](https://www.runoob.com/tags/tag-section.html)  |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|  [`<select>`](https://www.runoob.com/tags/tag-select.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<small>`](https://www.runoob.com/tags/tag-small.html)    |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<source>`](https://www.runoob.com/tags/tag-source.html)   |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|    [`<span>`](https://www.runoob.com/tags/tag-span.html)     |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|  [`<strike>`](https://www.runoob.com/tags/tag-strike.html)   | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
|  [`<strong>`](https://www.runoob.com/tags/tag-strong.html)   |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<style>`](https://www.runoob.com/tags/tag-style.html)    |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<sub>`](https://www.runoob.com/tags/tag-sub.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<summary>`](https://www.runoob.com/tags/tag-summary.html)  |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|     [`<sup>`](https://www.runoob.com/tags/tag-sup.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<table>`](https://www.runoob.com/tags/tag-table.html)    |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<tbody>`](https://www.runoob.com/tags/tag-tbody.html)    |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
|      [`<td>`](https://www.runoob.com/tags/tag-td.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
| [`<textarea>`](https://www.runoob.com/tags/tag-textarea.html) |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<tfoot>`](https://www.runoob.com/tags/tag-tfoot.html)    |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
|      [`<th>`](https://www.runoob.com/tags/tag-th.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<thead>`](https://www.runoob.com/tags/tag-thead.html)    |   Yes   |      Yes       |   Yes    |     Yes     |   **No**    |
|    [`<time>`](https://www.runoob.com/tags/tag-time.html)     |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|   [`<title>`](https://www.runoob.com/tags/tag-title.html)    |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|      [`<tr>`](https://www.runoob.com/tags/tag-tr.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<track>`](https://www.runoob.com/tags/tag-track.html)    |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|      [`<tt>`](https://www.runoob.com/tags/tag-tt.html)       | **No**  |      Yes       |   Yes    |     Yes     |     Yes     |
|       [`<u>`](https://www.runoob.com/tags/tag-u.html)        | **No**  |      Yes       |  **No**  |     Yes     |   **No**    |
|      [`<ul>`](https://www.runoob.com/tags/tag-ul.html)       |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|     [`<var>`](https://www.runoob.com/tags/tag-var.html)      |   Yes   |      Yes       |   Yes    |     Yes     |     Yes     |
|   [`<video>`](https://www.runoob.com/tags/tag-video.html)    |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |
|     [`<wbr>`](https://www.runoob.com/tags/tag-wbr.html)      |   Yes   |     **No**     |  **No**  |   **No**    |   **No**    |


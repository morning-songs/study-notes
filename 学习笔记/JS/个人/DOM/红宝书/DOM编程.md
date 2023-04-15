# DOM 编程

​		很多时候，操作 `DOM` 是很直观的。通过 `HTML` 代码能实现的，也一样能通过 `JavaScript` 实现。但有时候，`DOM` 也没有看起来那么简单。浏览器能力的参差不齐和各种问题，也会导致 `DOM` 的某些方面会复杂一些。



### 动态脚本

​		`<script>` 元素用于向网页中插入 `JavaScript` 代码，可以是 `src` 属性包含的外部文件，也可以是作为该元素内容的源代码。动态脚本就是在页面初始加载时不存在，之后又通过 `DOM` 包含的脚本。与对应的 `HTML` 元素一样，有两种方式通过 `<script>` 动态为网页添加脚本：引入外部文件和直接插入源代码。

​		动态加载外部文件很容易实现，比如下面的 `<script>` 元素：

```html
<script src="foo.js"></script>
```

​		可以像这样通过 `DOM` 编程创建这个节点：

```js
let script = document.createElement("script"); 
script.src = "foo.js"; 
document.body.appendChild(script);
```

​		这里的 `DOM` 代码实际上完全照搬了它要表示的 `HTML` 代码。注意，在上面最后一行把 `<script>` 元素添加到页面之前，是不会开始下载外部文件的。当然也可以把它添加到 `<head>` 元素，同样可以实现动态脚本加载。这个过程可以抽象为一个函数，比如：

```js
function loadScript(url) { 
 	let script = document.createElement("script"); 
 	script.src = url; 
 	document.body.appendChild(script); 
}
```

​		然后，就可以像下面这样加载外部 `JavaScript` 文件了：

```js
loadScript("client.js");
```

​		加载之后，这个脚本就可以对页面执行操作了。这里有个问题：如何知道脚本什么时候加载完？这个问题并没有标准答案。《事件》章节中会讨论一些与加载相关的事件，具体情况取决于使用的浏览器。注意：脚本通常会在下载完成之后立即执行，除非 `defer` 执行。

​		另一个动态插入 `JavaScript` 的方式是嵌入源代码，如下例所示：

```html
<script> 
	function sayHi() { 
 		console.log("hi");
 	} 
</script>
```

​		`<script>` 元素能够将插入其中的外来字符串代码识别为 `JS` 代码 —— 这很强大，也很危险。

```html
<!-- 不会识别script元素内部原有的字符串 -->
<script>
	"function logAge(){console.log('age');}"
    
    logAge(); // Uncaught ReferenceError: logAge is not defined
</script>

<!-- 如果是外部插入的字符串，则会被识别 -->
<script id="test"></script>
<script>
	let test = document.getElementById("test");

    test.append("function logAge(){console.log('age');}");

    logAge(); // "age"
</script>

<!-- 如果在自己内部通过这种方式插入字符串，也会被识别为JS代码，但这样的代码不会被执行且始终位于脚本底部 -->
<script id="ts">
	let ts = document.getElementById("ts");

    ts.append("(function () {console.log('age');})()");

    // 会在脚本底部生成这段代码，但它不会被执行。
    (function() {console.log('age');})();
</script>
```

​		因此，使用 `DOM`，可以实现以下逻辑：

```js
let script = document.createElement("script"); // 创建脚本
script.appendChild(document.createTextNode("function sayHi(){console.log('hi');}")); // 嵌入代码
document.body.appendChild(script); // 插入页面

sayHi(); // "hi"
```

​		以上代码可以在 `Firefox`、`Safari`、`Chrome` 和 `Opera` 中运行。不过在旧版本的 `IE` 中可能会导致问题。这是因为 `IE` 对 `<script>` 元素做了特殊处理，不允许常规 `DOM` 访问其子节点。但 `<script>` 元素上有一个 `text` 属性，可以用来添加 `JavaScript` 代码，如下所示：

```js
var script = document.createElement("script"); 
script.text = "function sayHi(){alert('hi');}"; 
document.body.appendChild(script);
```

​		这样修改后，上面的代码可以在 `IE`、`Firefox`、`Opera` 和 `Safari 3` 及更高版本中运行。`Safari 3` 之前的版本不能正确支持这个 `text` 属性，但这些版本却支持文本节点赋值。对于早期的 `Safari` 版本，需要使用以下代码：

```js
var script = document.createElement("script"); 
var code = "function sayHi(){console.log('hi');}"; 
try { 
 	script.appendChild(document.createTextNode(code)); // 标准方式
} catch (e){ 
 	script.text = code; // IE方式
} 
document.body.appendChild(script);
```

​		这里先尝试使用标准的 `DOM` 文本节点插入方式，因为除 `IE` 之外的浏览器都支持这种方式。`IE` 此时会抛出错误，那么可以在捕获错误之后再使用 `text` 属性来插入 `JavaScript` 代码。于是，我们就可以抽象出一个跨浏览器的函数：

```js
function loadScriptString(code){ 
 	var script = document.createElement("script"); 
 	script.type = "text/javascript"; 
 	try { 
 		script.appendChild(document.createTextNode(code)); 
 	} catch (e){ 
 		script.text = code; 
 	} 
 	document.body.appendChild(script); 
}
```

​		这个函数可以这样调用：

```js
loadScriptString("function sayHi(){console.log('hi');}");
```

​		以这种方式加载的代码会在全局作用域中执行，并在调用返回后立即生效。基本上，这就相当于是在全局作用域中把源代码传给了  `eval()`方法。

​		过去，通过 `innerHTML` 属性创建的 `<script>` 元素永远不会执行。浏览器会尽责地创建 `<script>` 元素，以及其中的脚本文本，但解析器会给这个 `<script>` 元素打上永不执行的标签。只要是使用 `innerHTML` 创建的 `<script>` 元素，以后也没有办法强制其执行。但现在，不仅可以通过 `innerHTML` 直接插入源代码，也可以通过 `innerText` 插入。

```js
var script = document.createElement("script");
script.innerHTML = "function sayHi(){console.log('hi');}";
document.body.appendChild(script);

sayHi(); // "hi"
```



### 动态样式

​		`CSS` 样式在 `HTML` 页面中可以通过两个元素加载。`<link>` 元素用于包含 `CSS` 外部文件，而 `<style>` 元素用于添加嵌入样式。与动态脚本类似，动态样式也是页面初始加载时并不存在，而是在之后才添加到页面中的。

​		来看下面这个典型的 `<link>` 元素：

```html
<link rel="stylesheet" type="text/css" href="styles.css">
```

​		这个元素很容易使用 `DOM` 编程创建出来：

```js
// 创建元素
let link = document.createElement("link"); 

// 增强元素
link.rel = "stylesheet"; 
link.type = "text/css"; 
link.href = "styles.css"; 

// 插入元素
let head = document.getElementsByTagName("head")[0]; 
head.appendChild(link);
```

​		以上代码在所有主流浏览器中都能正常运行。注意应该把 `<link>` 元素添加到 `<head>` 元素而不是 `<body>` 元素，这样才能保证所有浏览器都能正常运行。这个过程可以抽象为以下通用函数：

```js
function loadStyles(url, head = document.getElementsByTagName("head")[0]){ 
 	let link = document.createElement("link"); 
 	link.rel = "stylesheet"; 
 	link.type = "text/css"; 
 	link.href = url; 
 	head.appendChild(link); 
}
```

​		然后就可以这样调用这个 `loadStyles()` 函数了：

```js
loadStyles("styles.css");
```

​		通过外部文件加载样式是一个异步过程。因此，样式的加载和正执行的 `JavaScript` 代码并没有先后顺序。一般来说，也没有必要知道样式什么时候加载完成。

​		另一种定义样式的方式是使用 `<script>` 元素包含嵌入的 `CSS` 规则，例如：

```html
<style type="text/css"> 
	body { 
 		background-color: red; 
	} 
</style>
```

​		逻辑上，下列 `DOM` 代码会有同样的效果：

```js
let style = document.createElement("style"); 
style.type = "text/css"; 
style.appendChild(document.createTextNode("body{background-color:red}")); 

let head = document.getElementsByTagName("head")[0]; 
head.appendChild(style);
```

​		以上代码在 `Firefox`、`Safari`、`Chrome` 和 `Opera` 中都可以运行，但旧版的 `IE` 除外。旧 `IE` 对 `<style>` 节点会施加限制，不允许访问其子节点，这一点与它对 `<script>` 元素施加的限制一样。事实上，`IE` 在执行到给 `<style>` 添加子节点的代码时，会抛出与给 `<script>` 添加子节点时同样的错误。对于 `IE`，解决方案是访问元素的 `styleSheet` 属性，这个属性又有一个 `cssText` 属性，然后给这个属性添加 `CSS` 代码：

```js
let style = document.createElement("style"); 
style.type = "text/css"; 

try{ 
 	style.appendChild(document.createTextNode("body{background-color:red}")); 
} catch (e){ 
 	style.styleSheet.cssText = "body{background-color:red}"; // 现代Edge已删除styleSheet属性。
} 

let head = document.getElementsByTagName("head")[0]; 
head.appendChild(style);
```

​		与动态添加脚本源代码类似，这里也使用了 `try...catch` 语句捕获 `IE` 抛出的错误，然后再以 `IE` 特有的方式来设置样式。这是最终的通用函数：

```js
function loadStyleString(css){ 
 	let style = document.createElement("style"); 
 	style.type = "text/css"; 
 	try{ 
 		style.appendChild(document.createTextNode(css)); 
 	} catch (e){ 
 		style.styleSheet.cssText = css; // 针对旧IE
 	} 
 	let head = document.getElementsByTagName("head")[0]; 
 	head.appendChild(style); 
}
```

​		可以这样调用这个函数：

```js
loadStyleString("body{background-color:red}");
```

​		这样添加的样式会立即生效，因此所有变化会立即反映出来。

注意：对于旧版 `IE`，要小心使用 `styleSheet.cssText`。如果重用同一个 `<style>` 元素并设置该属性超过一次，则可能导致浏览器崩溃。同样，将 `cssText` 设置为空字符串也可能导致浏览器崩溃。

​		同样的，现代浏览器允许使用 `innerHTML` 和 `innerText` 等方式，向 `<style>` 元素插入 `CSS` 代码。

```js
let style = document.createElement("style");

style.type = "text/css";
style.innerHTML = "body{background-color:red}";

let head = document.getElementsByTagName("head")[0];
head.appendChild(style);
```



### 操作表格

​		表格是 `HTML` 中最复杂的结构之一。通过 `DOM` 编程创建 `<table>` 元素，通常要涉及大量标签，包括表行、表元、表题，等等。因此，通过 `DOM` 编程创建和修改表格时可能要写很多代码。假设要通过 `DOM` 来创建以下 `HTML` 表格：

```html
<table border="1" width="100%"> 
 	<tbody> 
 		<tr> 
			<td>Cell 1,1</td> 
			<td>Cell 2,1</td> 
 		</tr> 
		 <tr> 
			<td>Cell 1,2</td> 
			<td>Cell 2,2</td> 
 		</tr> 
	 </tbody> 
</table>
```

​		下面就是以 `DOM` 编程方式重建这个表格的代码：

```js
// 创建表格
let table = document.createElement("table"); 
table.border = 1; 
table.width = "100%"; 

// 创建表体
let tbody = document.createElement("tbody"); 
table.appendChild(tbody); 

// 创建第一行
let row1 = document.createElement("tr"); 
tbody.appendChild(row1); 
let cell1_1 = document.createElement("td"); 
cell1_1.appendChild(document.createTextNode("Cell 1,1")); 
row1.appendChild(cell1_1); 
let cell2_1 = document.createElement("td"); 
cell2_1.appendChild(document.createTextNode("Cell 2,1")); 
row1.appendChild(cell2_1); 

// 创建第二行
let row2 = document.createElement("tr"); 
tbody.appendChild(row2); 
let cell1_2 = document.createElement("td"); 
cell1_2.appendChild(document.createTextNode("Cell 1,2")); 
row2.appendChild(cell1_2); 
let cell2_2= document.createElement("td"); 
cell2_2.appendChild(document.createTextNode("Cell 2,2")); 
row2.appendChild(cell2_2); 

// 把表格添加到文档主体
document.body.appendChild(table);
```

​		以上代码相当烦琐，也不好理解。为了方便创建表格，`HTML DOM` 给 `<table>`、`<tbody> `和 `<tr>` 元素添加了一些属性和方法。

​		`<table>` 元素添加了以下属性和方法：

- `caption`：指向 `<caption>` 元素的指针（如果存在）；
- `tBodies`：包含 `<tbody>` 元素的 `HTMLCollection`；
- `tFoot`：指向 `<tfoot>` 元素（如果存在）；
-  `tHead`：指向 `<thead>` 元素（如果存在）；
- `rows`：包含表示所有行的 `HTMLCollection`；
- `createTHead()`：创建 `<thead>` 元素（当不存在时），放到表格中，返回引用；
- `createTFoot()`：创建 `<tfoot>` 元素（当不存在时），放到表格中，返回引用；
- `createCaption()`：创建 `<caption>` 元素（当不存在时），放到表格中，返回引用；
- `deleteTHead()`：删除 `<thead>` 元素；
- `deleteTFoot()`：删除 `<tfoot>` 元素；
- `deleteCaption()`：删除 `<caption>` 元素；
- `deleteRow(pos)`：在行集合中删除给定位置的行；
- `insertRow(pos)`：在行集合中给定位置插入一行。

​		`<tbody>` 元素添加了以下属性和方法：

- `rows`：包含 `<tbody>` 元素中所有行的 `HTMLCollection`；
- `deleteRow(pos)`：删除给定位置的行；
- `insertRow(pos)`：在行集合中给定位置插入一行，返回该行的引用。

​		`<tr>` 元素添加了以下属性和方法：

- `cells`：包含 `<tr>` 元素所有表元的 `HTMLCollection`；
- `deleteCell(pos)`：删除给定位置的表元；
- `insertCell(pos)`：在表元集合给定位置插入一个表元，返回该表元的引用。

```html
<table id="t1" border="2">
    <caption>表格</caption>
    <thead>
        <tr>
            <th>A</th>
            <th>B</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>2</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td>3</td>
            <td>4</td>
        </tr>
    </tfoot>
</table>
```

```js
let table = document.getElementById("t1");

console.log(table.caption); // <caption>表格</caption>
console.log(table.tBodies); // HTMLCollection [tbody]
console.log(table.tFoot); 	// <tfoot>...</tfoot>
console.log(table.tHead); 	// <thead>...</thead>
console.log(table.rows); 	// HTMLCollection(3) [tr, tr, tr]

// 为表格删除caption、thead、tfoot元素
table.deleteCaption(); 	// undefined
table.deleteTHead(); 	// undefined
table.deleteTFoot(); 	// undefined

// 为表格创建caption、thead、tfoot元素
table.createCaption();	// <caption></caption>
table.createTHead();	// <thead></thead>
table.createTFoot();	// <tfoot></tfoot>

// 插入与删除行（默认插入到tbody里面）
table.insertRow(0); // <tr></tr>
table.deleteRow(0); // undefined

let tbody = table.tBodies[0];
console.log(tbody.rows); 	// HTMLCollection [tr]
tbody.insertRow(0); 		// <tr></tr>
tbody.deleteRow(0); 		// undefined

let tr = tbody.rows[0];
console.log(tr.cells); 			// HTMLCollection(2) [td, td]
console.log(tr.insertCell(0)); 	// <td></td>
console.log(tr.deleteCell(0)); 	// undefined
```

```html
<!-- 最终效果 -->
<table id="t1" border="2">
    <caption></caption>
    <thead></thead>
    <tbody>
    	<tr>
        	<td>1</td>
            <td>2</td>
        </tr>
    </tbody>
    <tfoot></tfoot>
</table>
```

​		这些属性和方法极大地减少了创建表格所需的代码量。例如，使用这些方法重写前面的代码之后是这样的：

```js
// 创建表格
let table = document.createElement("table"); 
table.border = 1; 
table.width = "100%"; 

// 创建表体
let tbody = document.createElement("tbody"); 
table.appendChild(tbody); 

// 创建第一行
tbody.insertRow(0); 
tbody.rows[0].insertCell(0); 
tbody.rows[0].cells[0].appendChild(document.createTextNode("Cell 1,1")); 
tbody.rows[0].insertCell(1); 
tbody.rows[0].cells[1].appendChild(document.createTextNode("Cell 2,1")); 

// 创建第二行
tbody.insertRow(1); 
tbody.rows[1].insertCell(0); 
tbody.rows[1].cells[0].appendChild(document.createTextNode("Cell 1,2")); 
tbody.rows[1].insertCell(1); 
tbody.rows[1].cells[1].appendChild(document.createTextNode("Cell 2,2")); 

// 把表格添加到文档主体
document.body.appendChild(table);
```

​		这里创建 `<table>` 和 `<tbody>` 元素的代码没有变。变化的是创建两行的部分，这次使用了 `HTML DOM` 表格的属性和方法。创建第一行时，在 `<tbody>` 元素上调用了 `insertRow()` 方法。传入参数 0，表示把这一行放在什么位置。然后，使用 `tbody.rows[0]` 来引用这一行，因为这一行刚刚创建并被添加到了 `<tbody>` 的位置 0。

​		创建表元的方式也与之类似。在 `<tr>` 元素上调用 `insertCell()` 方法，传入参数 0，表示把这个表元放在什么位置上。然后，使用 `tbody.rows[0].cells[0]` 来引用这个表元，因为这个表元刚刚创建并被添加到了 `<tr>` 的位置 0。

​		虽然以上两种代码在技术上都是正确的，但使用这些属性和方法创建表格让代码变得更有逻辑性，也更容易理解。



### `NodeList`

​		理解 `NodeList` 对象和相关的 `NamedNodeMap`、`HTMLCollection`，是理解 `DOM` 编程的关键。这 3 个集合类型都是 “实时的”，意味着文档结构的变化会实时地在它们身上反映出来，因此它们的值始终代表最新的状态。实际上，`NodeList` 就是基于 `DOM` 文档的实时查询。例如，下面的代码会导致无穷循环：

```js
let divs = document.getElementsByTagName("div"); 
for (let i = 0; i < divs.length; ++i){ 
 	let div = document.createElement("div"); 
 	document.body.appendChild(div); 
}
```

​		第一行取得了包含文档中所有 `<div>` 元素的 `HTMLCollection`。因为这个集合是 “实时的”，所以任何时候只要向页面中添加一个新 `<div>` 元素，再查询这个集合就会多一项。因为浏览器不希望保存每次创建的集合，所以就会在每次访问时更新集合。这样就会出现前面使用循环的例子中所演示的问题。每次循环开始，都会求值 `i < divs.length`。这意味着要执行获取所有 `<div>` 元素的查询。因为循环体中会创建并向文档添加一个新 `<div>` 元素，所以每次循环 `divs.length` 的值也会递增。因为两个值都会递增，所以 `i` 将永远不会等于 `divs.length`。

​		使用 `ES6` 迭代器并不会解决这个问题，因为迭代的是一个永远增长的实时集合。以下代码仍然会导致无穷循环：

```js
for (let div of document.getElementsByTagName("div")){ 
 	let newDiv = document.createElement("div"); 
 	document.body.appendChild(newDiv); 
}
```

​		任何时候要迭代 `NodeList`，最好再初始化一个变量保存当时查询时的长度，然后用循环变量与这个变量进行比较，如下所示：

```js
let divs = document.getElementsByTagName("div"); 
for (let i = 0, len = divs.length; i < len; ++i) { 
 	let div = document.createElement("div"); 
 	document.body.appendChild(div); 
}
```

​		在这个例子中，又初始化了一个保存集合长度的变量 `len`。因为 `len` 保存着循环开始时集合的长度，而这个值不会随集合增大动态增长，所以就可以避免前面例子中出现的无穷循环。本章还会使用这种技术来演示迭代 `NodeList` 对象的首选方式。

​		另外，如果不想再初始化一个变量，也可以像下面这样反向迭代集合：

```js
let divs = document.getElementsByTagName("div"); 
for (let i = divs.length - 1; i >= 0; --i) { 
 	let div = document.createElement("div"); 
 	document.body.appendChild(div); 
}
```

​		一般来说，最好限制操作 `NodeList` 的次数。因为每次查询都会搜索整个文档，所以最好把查询到的 `NodeList` 缓存起来。


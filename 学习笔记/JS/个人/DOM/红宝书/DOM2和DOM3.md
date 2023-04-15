# DOM2 和 DOM3

​		`DOM1`（`DOM Level 1`）主要定义了 `HTML` 和 `XML` 文档的底层结构。`DOM2`（`DOM Level 2`）和 `DOM3`（`DOM Level 3`）在这些结构之上加入更多交互能力，提供了更高级的 `XML` 特性。实际上，`DOM2` 和 `DOM3` 是按照模块化的思路来制定标准的，每个模块之间有一定关联，但分别针对某个 `DOM` 子集。这些模式如下所示。

- `DOM Core`：在 `DOM1` 核心部分的基础上，为节点增加方法和属性。
- `DOM Views`：定义基于样式信息的不同视图。
- `DOM Events`：定义通过事件实现 `DOM` 文档交互。
- `DOM Style`：定义以编程方式访问和修改 `CSS` 样式的接口。
- `DOM Traversal and Range`：新增遍历 `DOM` 文档及选择文档内容的接口。
- `DOM HTML`：在 `DOM1 HTML` 部分的基础上，增加属性、方法和新接口。
- `DOM Mutation Observers`：定义基于 `DOM` 变化触发回调的接口。这个模块是 `DOM4` 级模块，用于取代 `Mutation Events`。

​		本章介绍除 `DOM Events` 和 `DOM Mutation Observers` 之外的其他所有模块，第 17 章会专门介绍事件，而 `DOM Mutation Observers` 第 14 章已经介绍过了。`DOM3` 还有 `XPath` 模块和 `Load and Save` 模块，将在第 22 章介绍。

注意：比较老旧的浏览器（如 `IE8`）对本章内容支持有限。如果你的项目要兼容这些低版本浏览器，在使用本章介绍的 `API` 之前先确认浏览器的支持情况。推荐参考 `Can I Use` 网站。



### `DOM` 的推进

​		`DOM2` 和 `DOM3` `Core` 模块的目标是扩展 `DOM API`，满足 `XML` 的所有需求并提供更好的错误处理和特性检测。很大程度上，这意味着支持 `XML` 命名空间的概念。`DOM2 Core` 没有新增任何类型，仅仅在 `DOM1 Core` 基础上增加了一些方法和属性。`DOM3 Core` 则除了增强原有类型，也新增了一些新类型。

​		类似地，`DOM` `View` 和 `HTML` 模块也丰富了 `DOM` 接口，定义了新的属性和方法。这两个模块很小，因此本章将在讨论 `JavaScript` 对象的基本变化时将它们与 `Core` 模块放在一起讨论。

注意：本章只讨论浏览器实现的 `DOM API`，不会提及未被浏览器实现的。



#### `XML` 命名空间

​		`XML` 命名空间可以实现在一个格式规范的文档中混用不同的 `XML` 语言，而不必担心元素命名冲突。严格来讲，`XML` 命名空间在 `XHTML` 中才支持，`HTML` 并不支持。因此，本节的示例使用 `XHTML`。

​		命名空间是使用 `xmlns` 指定的。`XHTML` 的命名空间是 `"http://www.w3.org/1999/xhtml"`，应该包含在任何格式规范的 `XHTML` 页面的 `<html>` 元素中，如下所示：

```xhtml
<html xmlns="http://www.w3.org/1999/xhtml"> 
 	<head> 
 		<title>Example XHTML page</title> 
 	</head> 
 	<body> 
 		Hello world! 
 	</body> 
</html>
```

​		对这个例子来说，所有元素都默认属于 `XHTML` 命名空间。可以使用 `xmlns` 给命名空间创建一个前缀，格式为 **“`xmlns`: 前缀”**，如下面的例子所示：

```xhtml
<xhtml:html xmlns:xhtml="http://www.w3.org/1999/xhtml"> 
 	<xhtml:head> 
 		<xhtml:title>Example XHTML page</xhtml:title> 
 	</xhtml:head> 
 	<xhtml:body> 
 		Hello world! 
 	</xhtml:body> 
</xhtml:html>
```

​		这里为 `XHTML` 命名空间定义了一个前缀 `xhtml`，同时所有 `XHTML` 元素都必须加上这个前缀。为避免混淆，属性也可以加上命名空间前缀，比如：

```xhtml
<xhtml:html xmlns:xhtml="http://www.w3.org/1999/xhtml"> 
 	<xhtml:head> 
 		<xhtml:title>Example XHTML page</xhtml:title> 
 	</xhtml:head> 
 	<xhtml:body xhtml:class="home"> 
 		Hello world! 
 	</xhtml:body> 
</xhtml:html>
```

​		这里的 `class` 属性被加上了 `xhtml` 前缀。如果文档中只使用一种 `XML` 语言，那么命名空间前缀其实是多余的，只有一个文档混合使用多种 `XML` 语言时才有必要。比如下面这个文档就使用了 `XHTML` 和 `SVG` 两种语言：

```xhtml
<html xmlns="http://www.w3.org/1999/xhtml"> 
 	<head> 
 		<title>Example XHTML page</title> 
 	</head> 
 	<body> 
 		<svg xmlns="http://www.w3.org/2000/svg" version="1.1" 
             viewBox="0 0 100 100" style="width:100%; height:100%"> 
 			<rect x="0" y="0" width="100" height="100" style="fill:red" /> 
 		</svg> 
 </body> 
</html>
```

​		在这个例子中，通过给 `<svg>` 元素设置自己的命名空间，将其标识为当前文档的外来元素。这样一来，`<svg>` 元素及其属性，包括它的所有后代都会被认为属于 `"https://www.w3.org/2000/svg"` 命名空间。虽然这个文档从技术角度讲是 `XHTML` 文档，但由于使用了命名空间，其中包含的 `SVG` 代码也是有效的。

​		对于这样的文档，如果调用某个方法与节点交互，就会出现一个问题。比如，创建了一个新元素，那这个元素属于哪个命名空间？查询特定标签名时，结果中应该包含哪个命名空间下的元素？`DOM2 Core` 为解决这些问题，给大部分 `DOM1` 方法提供了特定于命名空间的版本。



##### `Node` 的变化

​		在 `DOM2` 中，`Node` 类型包含以下特定于命名空间的属性：

- `localName`：不包含命名空间前缀的节点名；
- `namespaceURI`：节点的命名空间 `URL`，如果未指定则为 `null`；
- `prefix`：命名空间前缀，如果未指定则为 `null`。

​		在节点使用命名空间前缀的情况下，`nodeName` 等于 `prefix + ":" + localName`。不使用时，等于 `localName` 。如下所示：

```xhtml
<html xmlns="http://www.w3.org/1999/xhtml"> 
	<head> 
 		<title>Example XHTML page</title> 
 	</head> 
 	<body> 
		<s:svg xmlns:s="http://www.w3.org/2000/svg" version="1.1" 
 			   viewBox="0 0 100 100" style="width:100%; height:100%"> 
 			<s:rect x="0" y="0" width="100" height="100" style="fill:red" /> 
 		</s:svg> 
 	</body> 
</html>
```

​		其中的 `<html>` 元素的 `localName` 和 `tagName` 都是 `"html"`，`namespaceURI` 是 `"http://www.w3.org/1999/xhtml"`，而 `prefix` 是 `null`。对于 `<s:svg>` 元素，`localName` 是 `"svg"`，`tagName` 是 `"s:svg"`，`namespaceURI` 是 `"https://www.w3.org`

`/2000/svg"`，而 `prefix` 是 `"s"`。注释：`tagName` 和 `nodeName` 相同。

```js
let html = document.documentElement;

console.log(html.localName); 	// 'html'
console.log(html.tagName); 		// 'html'
console.log(html.nodeName); 	// 'html'
console.log(html.namespaceURI); // 'http://www.w3.org/1999/xhtml'
console.log(html.prefix); 		// null

let svg = document.querySelector("svg");

console.log(svg.localName); 	// 'svg'
console.log(svg.tagName); 		// 's:svg'
console.log(svg.nodeName); 		// 's:svg'
console.log(svg.namespaceURI); 	// 'http://www.w3.org/2000/svg'
console.log(svg.prefix); 		// 's'

svg === document.getElementsByTagName("s:svg")[0]; // true
```

​		`DOM3` 进一步增加了如下与命名空间相关的方法：

- `isDefaultNamespace(namespaceURI)`：返回布尔值，表示 *`namespaceURI`* 是否为节点的默认命名空间；
- `lookupNamespaceURI(prefix)`：返回给定 *`prefix`* 的命名空间 `URI`；
- `lookupPrefix(namespaceURI)`：返回给定 *`namespaceURI`* 的前缀。

​		对前面的例子，可以执行以下代码：

```js
console.log(document.body.isDefaultNamespace("http://www.w3.org/1999/xhtml")); // true 

// 假设 svg 包含对<s:svg>元素的引用
console.log(svg.lookupPrefix("http://www.w3.org/2000/svg")); 	// "s" 
console.log(svg.lookupNamespaceURI("s")); 						// "http://www.w3.org/2000/svg"
```

​		这些方法主要用于通过元素查询前缀和命名空间 `URI`，以确定元素与文档的关系。



##### `Document` 的变化

​		`DOM2` 在 `Document` 类型上新增了如下命名空间特定的方法：

- `createElementNS(namespaceURI, tagName)`：以给定的标签名 *`tagName`* 创建指定命名空间 *`namespaceURI`* 的一个新元素；
- `createAttributeNS(namespaceURI, attributeName)`：以给定的属性名 *`attributeName`* 创建指定命名空间 *`namespaceURI`* 的一个新属性；
- `getElementsByTagNameNS(namespaceURI, localName)`：返回指定命名空间 *`namespaceURI`* 中所有标签名为 *`localName`* 的元素的 `HTMLCollection`。

​		使用这些方法都需要传入相应的命名空间 `URI`（不是命名空间前缀），如下面的例子所示：

```js
// 创建一个新 SVG 元素
document.createElementNS("http://www.w3.org/2000/svg", "svg"); 	// <svg></svg>

// 创建一个新带前缀的 SVG 元素
document.createElementNS("http://www.w3.org/2000/svg", "s:svg"); // <s:svg></s:svg>

// 创建一个指定命名空间的新属性
document.createAttributeNS("http://www.somewhere.com", "random"); // random=""

// 获取指定命名空间中的所有 XHTML 元素
document.getElementsByTagNameNS("http://www.w3.org/1999/xhtml", "*"); 
// HTMLCollection(5) [html, head, title, body, script]
```

​		这些命名空间特定的方法，只在文档中包含两个或两个以上命名空间时才能体现出他们的重要作用。

```xhtml
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>Example XHTML page</title>
</head>
<body>
    <s:svg xmlns:s="http://www.w3.org/2000/svg" version="1.1" 
           viewBox="0 0 100 100" style="width:100%; height:100%">
        <s:rect x="0" y="0" width="100" height="100" style="fill:red" />
    </s:svg>
    <svg></svg>
</body>
<script>
	let HC1 = document.getElementsByTagNameNS("http://www.w3.org/1999/xhtml", "*"),
    	HC2 = document.getElementsByTagNameNS("http://www.w3.org/2000/svg", "*");
    
    // 获取时，不能带前缀
  	let svg1 = document.getElementsByTagNameNS("http://www.w3.org/2000/svg", "svg");
    let svg2 = document.getElementsByTagNameNS("*", "svg");
    
    console.log(HC1); // HTMLCollection(6) [html, head, title, body, svg, script]
    console.log(HC2); // HTMLCollection(2) [s:svg, s:rect]
    
    console.log(svg1); // HTMLCollection [s:svg]
    console.log(svg2); // HTMLCollection(2) [s:svg, svg]
</script>
</html>
```



##### `Element` 的变化

​		`DOM2 Core` 对 `Element` 类型的更新主要集中在对属性的操作上。下面是新增的方法：

- `getAttributeNS(namespaceURI, localName)`：取得指定命名空间 *`namespaceURI`* 中名为 *`localName`* 的属性；
- `getAttributeNodeNS(namespaceURI, localName)`：取得指定命名空间 *`namespaceURI`* 中名为 *`localName`* 的属性节点；
- `getElementsByTagNameNS(namespaceURI, localName)`：取得指定命名空间 *`namespaceURI`* 中标签名为 *`localName`* 的元素的 `HTMLCollection`；
- `hasAttributeNS(namespaceURI, localName)`：返回布尔值，表示元素中是否有命名空间 *`namespaceURI`* 下名为 *`localName`* 的属性（注意，`DOM2 Core` 也添加不带命名空间的 `hasAttribute()` 方法）；
- `removeAttributeNS(namespaceURI, localName)`：删除指定命名空间 *`namespaceURI`* 中名为 *`localName`* 的属性；
- `setAttributeNS(namespaceURI, qualifiedName, value)`：设置指定命名空间 *`namespaceURI`* 中名为 *`qualifiedName`* 的属性为 *`value`*；特别需要注意的是，该方法会在 `NamedNodeMap` 中保存新旧两个版本的属性节点。
- `setAttributeNodeNS(attNode)`：为元素设置（添加）包含命名空间信息的属性节点 *`attNode`*。该节点只能被普通方法删除。

​		这些方法与 `DOM1` 中对应的方法行为相同，除 `setAttributeNodeNS()` 之外都只是多了一个命名空间参数。

```xhtml
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>Example XHTML page</title>
</head>
<body>
    <s:svg xmlns:s="http://www.w3.org/2000/svg" version="1.1">
        <s:rect x="0" y="0" width="100" height="100" style="fill:red" />
    </s:svg>
</body>
<script>
    let ns = "http://www.w3.org/2000/svg";
    let s_svg = document.getElementsByTagNameNS(ns, "svg")[0];
    
    console.log(s_svg.getAttributeNames()); // (2) ['xmlns:s', 'version']
    
    // 初次获取元素上的某个原有属性，会返回null。
    console.log(s_svg.hasAttributeNS(ns, "version")); // false
    console.log(s_svg.getAttributeNS(ns, "version")); // null
    console.log(s_svg.getAttributeNodeNS(ns, "version")); // null
    
    // 通过set API将其重新赋值后，就可以被正常获取了。
    s_svg.setAttributeNS(ns, "version", "1.1"); // 但它会在NamedNodeMap中保存新旧两个version节点。
    console.log(s_svg.hasAttributeNS(ns, "version")); // true
    console.log(s_svg.getAttributeNS(ns, "version")); // "1.1"
    console.log(s_svg.getAttributeNodeNS(ns, "version")); // version="1.1"
    
    // 通过API添加一个属性
    s_svg.setAttributeNS(ns, "custom", "cs"); // 在NamedNodeMap中添加一个custom节点
    console.log(s_svg.hasAttributeNS(ns, "custom")); // true
    console.log(s_svg.getAttributeNS(ns, "custom")); // "cs"
    console.log(s_svg.getAttributeNodeNS(ns, "custom")); // custom="cs"
    
    // 通过API删除一个属性
    s_svg.removeAttributeNS(ns, "custom"); // 在NamedNodeMap中删除一个custom节点
    console.log(s_svg.hasAttributeNS(ns, "custom")); // false
    console.log(s_svg.getAttributeNS(ns, "custom")); // null
    console.log(s_svg.getAttributeNodeNS(ns, "custom")); // null
    
     // 通过API添加属性节点
    let attr = document.createAttributeNS("http://www.somewhere.com", "random");
    s_svg.setAttributeNodeNS(attr); // 在NamedNodeMap中添加一个random节点
    
    console.log(s_svg.hasAttributeNS(ns, "random")); // false
    console.log(s_svg.getAttributeNS(ns, "random")); // null
    console.log(s_svg.getAttributeNodeNS(ns, "random")); // null
    
    s_svg.setAttributeNS(ns, "random", "rd"); // 在NamedNodeMap中再次添加一个random节点
    console.log(s_svg.hasAttributeNS(ns, "random")); // true
    console.log(s_svg.getAttributeNS(ns, "random")); // "rd"
    console.log(s_svg.getAttributeNodeNS(ns, "random")); // random="rd"
    
    console.log(s_svg.attributes);
    /*
    NamedNodeMap {
    	0: xmlns:s, 
    	1: version, 
    	2: version, 
    	3: random, 
    	4: random, 
    	xmlns:s: xmlns:s, 
    	version: version, 
    	random: random, 
    	length: 5
    }
    */
    
    // 由于NamedNodeMap中存在两个同名节点，而removeAttributeNS方法只能删除一个，因此需要删除两次才能彻底删除（见下文）。
</script>
</html>
```

​		`setAttributeNS()` 方法一定会向 `NamedNodeMap` 中添加一个新的属性节点，且添加的新属性节点可以被 `removeAttributeNS()` 方法彻底清除。 因此，一般推荐使用 `setAttributeNS()` 来添加新的属性节点。



##### `NamedNodeMap` 的变化

​		`NamedNodeMap` 也增加了以下处理命名空间的方法。因为 `NamedNodeMap` 主要表示属性，所以这些方法大都适用于属性：

- `getNamedItemNS(namespaceURI, localName)`：取得指定命名空间 *`namespaceURI`* 中名为 *`localName`* 的项；
- `removeNamedItemNS(namespaceURI, localName)`：删除指定命名空间 *`namespaceURI`* 中名为 *`localName`* 的项；
- `setNamedItemNS(node)`：为元素设置（添加）包含命名空间信息的节点。该节点只能被普通方法删除。

​		这些方法很少使用，因为通常都是使用元素来访问属性。

```js
// 元素的attributes属性是一个NamedNodeMap的实例
let nnm = s_svg.attributes;

// 与Element中的方法一样，需要先通过set API重新赋值。
console.log(nnm.getNamedItemNS(ns, "version")); // null
s_svg.setAttributeNS(ns, "version", "1.1"); 	// 但它会在NamedNodeMap中保存新旧两个version节点。
console.log(nnm.getNamedItemNS(ns, "version")); // version="1.1"

// 创建并插入一个新的属性节点
let attr = document.createAttributeNS("http://www.somewhere.com", "random");
attr.nodeValue = "rd";
nnm.setNamedItemNS(attr); // 在NamedNodeMap中添加一个random节点

console.log(nnm.getNamedItemNS(ns, "random")); // null
s_svg.setAttributeNS(ns, attr.nodeName, attr.nodeValue); // 在NamedNodeMap中再次添加一个random节点
console.log(nnm.getNamedItemNS(ns, "random")); // random="rd"

// 删除一个属性节点
nnm.removeNamedItemNS(ns, "random"); // 在NamedNodeMap中删除一个random节点，但还有一个。
console.log(nnm.getNamedItemNS(ns, "random")); 			// null
console.log(s_svg.hasAttributeNS(ns, "random")); 		// false
console.log(s_svg.getAttributeNS(ns, "random")); 		// null
console.log(s_svg.getAttributeNodeNS(ns, "random")); 	// null
```

注意：通过 `setNamedItemNS()` 和 `setAttributeNodeNS()` 方法添加的属性节点，以及元素原有的属性节点，都无法被命名空间的特有方法彻底删除，它们仍会作为元素的属性节点存在，换句话说，它们不仅会被保留在页面上，还会继续存在于 `attributes` 属性的 `NamedNodeMap` 中，只是无法被 `getNamedItemNS` 等 `API` 访问了而已。

```js
// 接上文

// 仍然作为元素属性存在
console.log(nnm); // 注释：NamedNodeMap是一个实时列表
/*
NamedNodeMap {
	0: xmlns:s, 
	1: version, 
	2: version, 
	3: random, 
	xmlns:s: xmlns:s, 
	version: version, 
	random: random, 
	length: 4
}
*/ 

console.log(nnm[3]); // ramdom="rd"

// 试图访问，是无效的
console.log(nnm.getNamedItemNS(ns, "random")); 			// null
console.log(s_svg.hasAttributeNS(ns, "random")); 		// false
console.log(s_svg.getAttributeNS(ns, "random")); 		// null
console.log(s_svg.getAttributeNodeNS(ns, "random")); 	// null

// 因为它实际还存在，因此可以被HTML的普通方法访问
console.log(s_svg.getAttribute("random")); // "rd"

// 再次通过Element的setAttributeNS方法为其重新赋值，会使其恢复。
s_svg.setAttributeNS(ns, attr.nodeName, attr.nodeValue);

// 恢复后能够正常访问
console.log(nnm.getNamedItemNS(ns, "random")); 			// random="rd"
console.log(s_svg.hasAttributeNS(ns, "random")); 		// true
console.log(s_svg.getAttributeNS(ns, "random")); 		// "rd"
console.log(s_svg.getAttributeNodeNS(ns, "random")); 	// random="rd"
```

​		而如果想要被彻底删除，则还需再调用一次 `removeAttribute` 等一般性删除方法。另外，元素原有的元素属性也需要删两次。

```js
// 注意：这里需要连续删除两次（最多同时存在两个同名节点——新旧节点）。
// 至少调用一次普通的removeAttribute删除方法，另外一次可以是removeAttribute、removeNamedItemNS或removeAttributeNS。
s_svg.removeAttribute("random");
s_svg.removeAttribute("random");

// random节点已从指定元素中被彻底删除，删除原有属性节点version同样如此。
console.log(nnm); 
// NamedNodeMap {0: xmlns:s, 1: version, 2: version, xmlns:s: xmlns:s, version: version, length: 3}

console.log(s_svg.getAttribute("random")); // null

console.log(nnm.getNamedItemNS(ns, "random")); 			// null
console.log(s_svg.hasAttributeNS(ns, "random")); 		// false
console.log(s_svg.getAttributeNS(ns, "random")); 		// null
console.log(s_svg.getAttributeNodeNS(ns, "random")); 	// null
```

```js
// 彻底清除指定元素上的某个属性节点
function clearAttribute(elem, attr) {
    let attrs = elem.attributes;
    while (attrs[attr]) {
        elem.removeAttribute(attr);
    }
}
```



#### 其他变化

​		除命名空间相关的变化，`DOM2 Core` 还对 `DOM` 的其他部分做了一些更新。这些变化与 `XML` 命名空间无关，主要关注 `DOM API` 的完整性与可靠性。



##### `DocumentType` 的变化

​		`DocumentType` 新增了 3 个属性：`publicId`、`systemId` 和 `internalSubset`。`publicId`、`systemId` 属性表示文档类型声明中有效但无法使用 `DOM1 API` 访问的数据。比如下面这个 `HTML` 文档类型声明：

```xhtml
<!DOCTYPE HTML PUBLIC "-// W3C// DTD HTML 4.01// EN" "http://www.w3.org/TR/html4/strict.dtd">
```

​		其 `publicId` 是 `"-// W3C// DTD HTML 4.01// EN"`，而 `systemId` 是 `"http://www.w3.org/TR/html4/strict.dtd"`。支持 `DOM2` 的浏览器应该可以运行以下 `JavaScript` 代码：

```js
console.log(document.doctype.publicId); // '-// W3C// DTD HTML 4.01// EN'
console.log(document.doctype.systemId); // 'http://www.w3.org/TR/html4/strict.dtd'
```

​		通常在网页中很少需要访问这些信息。

​		`internalSubset` 用于访问文档类型声明中可能包含的额外定义，如下面的例子所示：

```xhtml
<!DOCTYPE html PUBLIC "-// W3C// DTD XHTML 1.0 Strict// EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd" [<!ELEMENT name (#PCDATA)>] >
```

​		对于以上声明，`document.doctype.internalSubset` 会返回 `"<!ELEMENT name (#PCDATA)>"`。`HTML` 文档中几乎不会涉及文档类型的内部子集，`XML` 文档中稍微常用一些。



##### `Document` 的变化

​		`Document` 类型的更新中唯一跟命名空间无关的方法是 `importNode()`。这个方法的目的是从其他文档获取一个节点并导入到新文档，以便将其插入新文档。每个节点都有一个 `ownerDocument` 属性，表示所属文档。如果调用 `appendChild()` 方法时传入节点的 `ownerDocument` 不是指向当前文档，则会发生错误。而调用 `importNode()` 导入其他文档的节点会返回一个新节点，这个新节点的`ownerDocument` 属性是正确的。

```js
console.log(document.body.ownerDocument === document); // true
```

​		`importNode()` 方法跟 `cloneNode()` 方法类似，同样接收两个参数：要复制的节点和表示是否同时复制子树的布尔值，返回结果是适合在当前文档中使用的新节点。下面看一个例子：

```js
let newNode = document.importNode(oldNode, true); // 导入节点及其子树

console.log(newNode.ownerDocument === document); // true

document.body.appendChild(newNode);
```

​		这个方法在 `HTML` 中使用得并不多，在 `XML` 文档中的使用会更多一些（第 22 章会深入讨论）。

​		`DOM2 View` 给 `Document` 类型增加了新属性 `defaultView`，是一个指向拥有当前文档的窗口（或窗格 `<frame>`）的指针。这个规范中并没有明确视图何时可用，因此这是添加的唯一一个属性。`defaultView` 属性得到了除 `IE8` 及更早版本之外所有浏览器的支持。`IE8` 及更早版本支持等价的 `parentWindow` 属性，`Opera` 也支持这个属性。因此要确定拥有文档的窗口，可以使用以下代码：

```js
let parentWindow = document.defaultView || document.parentWindow;
```

​		除了上面这一个方法和一个属性，`DOM2 Core` 还针对 `document.implementation` 对象增加了两个新方法：`createDocumentType()` 和 `createDocument()`。前者用于创建 `DocumentType` 类型的新节点，接收 3 个参数：文档类型名称、`publicId` 和 `systemId`。比如，以下代码可以创建一个新的 `HTML 4.01` 严格型文档：

```js
let doctype = document.implementation.createDocumentType(
    "html", 
 	"-// W3C// DTD HTML 4.01// EN", 
 	"http://www.w3.org/TR/html4/strict.dtd");

console.log(doctype); 
// <!DOCTYPE html PUBLIC "-// W3C// DTD HTML 4.01// EN" "http://www.w3.org/TR/html4/strict.dtd">
```

​		已有文档的文档类型不可更改，因此 `createDocumentType()` 只在创建新文档时才会用到，而创建新文档要使用 `createDocument` 方法。`createDocument()` 接 收 3 个参数：文档元素的 `namespaceURI`、文档元素的标签名和文档类型。比如，下列代码可以创建一个空的 `XML` 文档：

```js
let doc = document.implementation.createDocument("", "root", null);
/*
▼#document
	<root></root>
*/
```

​		这个空文档没有命名空间和文档类型，只指定了 `<root>` 作为文档元素。要创建一个 `XHTML` 文档，可以使用以下代码：

```js
let doctype = document.implementation.createDocumentType(
    "html", 
 	"-// W3C// DTD XHTML 1.0 Strict// EN", 
 	"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"); 

let doc = document.implementation.createDocument("http://www.w3.org/1999/xhtml", "html", doctype);

console.log(doc);
/*
▼#document
	<!DOCTYPE html PUBLIC "-// W3C// DTD XHTML 1.0 Strict// EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
	<html></html>
*/
```

​		这里使用了适当的命名空间和文档类型创建一个新 `XHTML` 文档。这个文档只有一个文档元素 `<html>`，其他一切都需要另行添加。

​		`DOM2 HTML` 模块也为 `document.implamentation` 对象添加了 `createHTMLDocument()` 方法。使用这个方法可以创建一个完整的 `HTML` 文档，包含 `<html>`、`<head>`、`<title>` 和 `<body>` 元素。这个方法只接收一个参数，即新创建文档的标题（放到 `<title>` 元素中），返回一个新的 `HTML` 文档。比如：

```js
let htmldoc = document.implementation.createHTMLDocument("New Doc"); 
console.log(htmldoc.title); 		// "New Doc" 
console.log(htmldoc);
/*
▼#document
	<html>
		<head>
			<title>New Doc</title>
		</head>
		<body></body>
	</html>
*/
```

​		`createHTMLDocument()` 方法创建的对象是 `HTMLDocument` 类型的实例，因此包括该类型所有相关的方法和属性，包括 `title` 和 `body` 属性。



##### `Node` 的变化

​		`DOM3` 新增了两个用于比较节点的方法：`isSameNode()` 和 `isEqualNode()`。这两个方法都接收一个节点参数，如果这个节点与参考节点相同或相等，则返回 `true`。节点相同，意味着引用同一个对象；节点相等，意味着节点类型相同，拥有相等的属性（`nodeName`、`nodeValue` 等），而且 `attributes` 和 `childNodes` 也相等（即同样的位置上包含相等的值）。来看一个例子：

```js
let div1 = document.createElement("div"); 
div1.setAttribute("class", "box"); 

let div2 = document.createElement("div");
div2.setAttribute("class", "box"); 

console.log(div1.isSameNode(div1)); 	// true 
console.log(div1.isEqualNode(div2)); 	// true 
console.log(div1.isSameNode(div2)); 	// false

div1.classList.add("div1");
console.log(div1.isEqualNode(div2)); 	// false 
console.log(div1.isSameNode(div2)); 	// false
```

​		这里创建了包含相同属性的两个 `<div>` 元素。这两个元素相等，但并不相同。

​		`DOM3` 也增加了给 `DOM` 节点附加额外数据的方法。`setUserData()` 方法接收 3 个参数：键、值、处理函数，用于给节点追加数据。可以像下面这样把数据添加到一个节点：

```js
// 该方法可能已删除，在现代浏览器上不存在。
document.body.setUserData("name", "Nicholas", function() {});
```

​		然后，可以通过相同的键再取得这个信息，比如：

```js
let value = document.body.getUserData("name");
```

​		`setUserData()` 的处理函数会在包含数据的节点被复制、删除、重命名或导入其他文档的时候执行，可以在这时候决定如何处理用户数据。处理函数接收 5 个参数：表示操作类型的数值（1 代表复制，2 代表导入，3 代表删除，4 代表重命名）、数据的键、数据的值、源节点和目标节点。删除节点时，源节点为 `null`；除复制外，目标节点都为 `null`。

```js
let div = document.createElement("div"); 

div.setUserData("name", "Nicholas", function(operation, key, value, src, dest) { 
 	if (operation == 1) { 
 		dest.setUserData(key, value, function() {}); 
    } 
}); 

let newDiv = div.cloneNode(true); 
console.log(newDiv.getUserData("name")); // "Nicholas"
```

​		这里先创建了一个 `<div>` 元素，然后给它添加了一些数据，包含用户的名字。在使用 `cloneNode()` 复制这个元素时，就会调用处理函数，从而将同样的数据再附加给复制得到的目标节点。然后，在副本节点上调用 `getUserData()` 能够取得附加到源节点上的数据。



##### 内嵌窗格的变化

​		`DOM2 HTML` 给 `HTMLIFrameElement`（即 `<iframe>`，内嵌窗格）类型新增了一个属性，叫 `contentDocument`。这个属性包含代表子内嵌窗格中内容的 `document` 对象的指针。下面的例子展示了如何使用这个属性：

```js
let iframe = document.getElementById("myIframe"); 

console.log(iframe.contentDocument); 	// ▶#document
console.log(iframe.contentWindow); 		// Window {...}
```

​		`contentDocument` 属性是 `Document` 的实例，拥有所有文档属性和方法，因此可以像使用其他 `HTML` 文档一样使用它。还有一个属性 `contentWindow`，返回相应窗格的 `window` 对象，这个对象上有一个 `document` 属性。所有现代浏览器都支持 `contentDocument` 和 `contentWindow` 属性。

注意：跨源访问子内嵌窗格的 `document` 对象会受到安全限制。如果内嵌窗格中加载了不同域名（或子域名）的页面，或者该页面使用了不同协议，则访问其 `document` 对象会抛出错误。



### 样式

​		`HTML` 中的样式有 3 种定义方式：外部样式表（通过 `<link>` 元素）、文档样式表（使用 `<style>` 元素）和元素特定（亦称行间）样式（使用 `style` 属性）。`DOM2 Style` 为这 3 种应用样式的机制都提供了 `API`。



#### 存取元素样式

​		任何支持 `style` 属性的 `HTML` 元素在 `JavaScript` 中都会有一个对应的 `style` 属性。这个 `style` 属性是 `CSSStyleDeclaration` 类型的实例，其中包含通过 `HTML style` 属性为元素设置的所有样式信息，但不包含通过层叠机制从文档样式和外部样式中继承来的样式。`HTML style` 属性中的 `CSS` 属性在 `JavaScript style` 对象中都有对应的属性。因为 `CSS` 属性名使用连字符表示法（用连字符分隔两个单词，如 `background-image`），所以在 `JavaScript` 中这些属性必须转换为驼峰大小写形式（如 `backgroundImage`）。下表给出了几个常用的 `CSS` 属性与 `style` 对象中等价属性的对比。

|      CSS属性       |     JavaScript属性      |
| :----------------: | :---------------------: |
| `background-image` | `style.backgroundImage` |
|      `color`       |      `style.color`      |
|     `display`      |     `style.display`     |
|   `font-family`    |   `style.fontFamily`    |

```html
<style>
    div#box {
        font-family: Inter, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen, Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif, arial;
        text-align: center;
    }
</style>

<div id="box" style="float: left; width: 100px; height: 100px; background-color: red;"></div>

<script>
	let div_box = document.getElementById("box");
    
    console.log(div_box.style); // CSSStyleDeclaration {...}
    /*
    CSSStyleDeclaration {
    	0: "float",
    	1: "width",
    	2: "height",
    	3: "background-color",
    	float: "left",
    	width: "100px",
    	height: "100px",
    	backgroundColor: "red",
    	...,
    	cssFloat: "left",
    	cssText: "float: left; width: 100px; height: 100px; background-color: red;",
    	length: 4,
    	parentRule: null,
		[[Prototype]]: CSSStyleDeclaration
    }
    */
</script>
```

​		大多数属性名会这样直接转换过来。过去，有一个 `CSS` 属性名不能直接转换，它就是 `float`。因为 `float` 是 `JavaScript` 的保留字，所以不能用作属性名。`DOM2 Style` 规定它在 `style` 对象中对应的属性应该是 `cssFloat`。

```js
let boxStyle = div_box.style;

// 行间样式
console.log(boxStyle.float); 	// "left"
console.log(boxStyle.width); 	// "100px"
console.log(boxStyle.height); 	// "100px"
console.log(boxStyle.backgroundColor); // "red"

// style属性中并不包含文档样式
console.log(boxStyle.fontFamily); 	// ""
console.log(boxStyle.textAlign); 	// ""
```

​		任何时候，只要获得了有效 `DOM` 元素的引用，就可以通过 `JavaScript` 来设置样式。来看下面的例子：

```js
// 修改背景色
boxStyle.backgroundColor = "#ebe8e8"; 

// 修改大小
boxStyle.width = "300px"; 
boxStyle.height = "200px"; 
```

​		像这样修改样式时，元素的外观会自动更新。

注意：在标准模式下，所有尺寸都必须包含单位。在混杂模式下，可以把 `style.width` 设置为 `"20"`，相当于 `"20px"`。如果是在标准模式下，把 `style.width` 设置为 `"20"` 会被忽略，因为没有单位。实践中，最好一直加上单位。

​		`style` 对象默认包含所有可能的 `CSS` 属性的空值，通过 `style` 对象设置的样式会被添加到元素的行间 `style` 属性中。

```js
// 设置边框
boxStyle.border = "1px solid #ca1f1f";
```

```html
<div id="box" style="
	 float: left; 
     width: 300px; 
	 height: 200px; 
   	 background-color: rgb(235, 232, 232); 
     border: 1px solid rgb(202, 31, 31);
"></div>
```



##### `DOM` 样式属性和方法

​		`DOM2 Style` 规范也在 `style` 对象上定义了一些属性和方法。这些属性和方法提供了元素 `style` 属性的信息并支持修改，如下：

- `cssText`：包含 `style` 属性中的 `CSS` 代码。
- `length`：应用给元素的 `CSS` 属性数量。
- `parentRule`：表示 `CSS` 信息的 `CSSRule` 对象（下一节会讨论 `CSSRule` 类型）。
- `getPropertyCSSValue(propertyName)`：返回包含 `CSS` 属性 *`propertyName`* 值的 `CSSValue` 对象（已废弃）。
- `getPropertyPriority(propertyName)`：如果 `CSS` 属性 *`propertyName`* 使用了 `!important` 则返回 `"important"`，否则返回空字符串。
- `getPropertyValue(propertyName)`：返回属性 *`propertyName`* 的字符串值。
- `item(index)`：返回索引为 *`index`* 的 `CSS` 属性名。
- `removeProperty(propertyName)`：从样式中删除 `CSS` 属性 *`propertyName`*。
- `setProperty(propertyName, value, priority)`：设置 `CSS` 属性 *`propertyName`* 的值为 *`value`*，*`priority`* 是 `"important"` 或空字符串。

​		通过 `cssText` 属性可以存取样式的 `CSS` 代码。在读模式下，`cssText` 返回 `style` 属性 `CSS` 代码在浏览器内部的表示。在写模式下，给 `cssText` 赋值会重写整个 `style` 属性的值，意味着之前通过 `style` 属性设置的属性都会丢失。比如，如果一个元素通过 `style` 属性设置了边框，而赋给 `cssText` 属性的值不包含边框，则元素的边框会消失。下面的例子演示了 `cssText` 的使用：

```js
boxStyle.cssText = "width: 200px; height: 100px; background-color: green"; 

console.log(boxStyle.cssText); // "width: 200px; height: 100px; background-color: green"
```

​		设置 `cssText` 是一次性修改元素多个样式最快捷的方式，因为所有变化会同时生效。

​		`length` 属性是跟 `item()` 方法一起配套迭代 `CSS` 属性用的。此时，`style` 对象实际上变成了一个集合，也可以用中括号代替 `item()` 取得相应位置的 `CSS` 属性名，如下所示：

```js
for (let i = 0, len = boxStyle.length; i < len; i++) { 
 	console.log(boxStyle[i]); // 或者用 boxStyle.item(i) 
}
/*
> "width"
> "height"
> "background-color"
*/
```

​		使用中括号或者 `item()` 都可以取得相应位置的 `CSS` 属性名（`"background-color"`，不是 `"backgroundColor"`）。这个属性名可以传给 `getPropertyValue()` 以取得属性的值，如下面的例子所示：

```js
let prop, value, i, len; 

for (i = 0, len = boxStyle.length; i < len; i++) { 
 	prop = boxStyle.item(i);
 	value = boxStyle.getPropertyValue(prop); 
 	console.log(`${prop}: ${value}`); 
}
/*
> "width: 200px"
> "height: 100px"
> "background-color: green"
*/
```

​		`getPropertyValue()` 方法返回 `CSS` 属性值的字符串表示。如果需要更多信息，则可以通过 `getPropertyCSSValue()` 获取 `CSSValue` 对象。这个对象有两个属性：`cssText` 和 `cssValueType`。前者的值与 `getPropertyValue()` 方法返回的值一样；后者是一个数值常量，表示当前值的类型（0 代表继承的值，1 代表原始值，2 代表列表，3 代表自定义值）。下面的代码演示了如何输出 `CSS` 属性值和值类型：

注释：不过，`getPropertyCSSValue()` 方法已经被废弃，虽然可能有浏览器还支持，但随时有可能被删除。建议开发中使用 `getPropertyValue()`。

```js
let prop, value, i, len; 

for (i = 0, len = boxStyle.length; i < len; i++) { 
 	prop = boxStyle[i]; // 或者：boxStyle.item(i) 
 	value = boxStyle.getPropertyCSSValue(prop); 
 	console.log(`${prop}: ${value.cssText} (${value.cssValueType})`); 
}
```

​		`removeProperty()` 方法用于从元素样式中删除指定的 `CSS` 属性。使用这个方法删除属性意味着会应用该属性的默认（从其他样式表层叠继承的）样式。例如，可以像下面这样删除 `style` 属性中设置的 `background-color` 样式：

```js
// 删除行间样式，意味着将使用继承样式或默认样式。
boxStyle.removeProperty("background-color"); // "green"
```

​		在不确定给定 `CSS` 属性的默认值是什么的时候，可以使用这个方法。只要从 `style` 属性中删除，就可以使用默认值。

​		通过 `setProperty` 方法，可以设置行间 `CSS` 属性的值与权重。使用 `getPropertyPriority` 方法，可以获取指定 `CSS` 属性的优先级（权重）。但权重只有 `"important"` 和空串两个值。

```js
boxStyle.setProperty("border", "1px solid red", "important");

boxStyle.getPropertyValue("border"); // '1px solid red'

boxStyle.getPropertyPriority("border"); // 'important'
```

```html
<div id="box" style="width: 200px; height: 100px; border: 1px solid red !important;"></div>
```



##### 计算样式

​		`style` 对象中包含支持 `style` 属性的元素为这个属性设置的样式信息，但不包含从其他样式表层叠继承的同样影响该元素的样式信息。`DOM2 Style` 在 `document.defaultView` 上增加了 `getComputedStyle()` 方法，它包含行间样式表、文档样式表和外部样式表中的样式。这个方法接收两个参数：要取得计算样式的元素和伪元素字符串（如 `":after"`）。如果不需要查询伪元素，则第二个参数可以传 `null`。`getComputedStyle()` 方法返回一个 `CSSStyleDeclaration` 对象（与 `style` 属性的类型一样），包含元素的计算样式。假设有如下 `HTML` 页面：

```html
<!DOCTYPE html> 
<html> 
<head> 
 	<title>Computed Styles Example</title> 
 	<style type="text/css"> 
 		#myDiv { 
			background-color: blue; 
			width: 100px; 
			height: 200px; 
 		} 
 	</style> 
</head> 
<body> 
 	<div id="myDiv" style="background-color: red; border: 1px solid black"></div> 
</body> 
</html>
```

​		这里的 `<div>`元 素从文档样式表（`<style>` 元素）和自己的 `style` 属性获取了样式。此时，这个元素的 `style` 对象中包含 `backgroundColor` 和 `border` 属性，但不包含（通过样式表规则应用的）`width` 和 `height` 属性。下面的代码从这个元素获取了计算样式：

```js
let myDiv = document.getElementById("myDiv"); 
let computedStyle = document.defaultView.getComputedStyle(myDiv, null); 

// 在Chrome中
console.log(computedStyle.backgroundColor); // "rgb(255, 0, 0)" 
console.log(computedStyle.width); 			// "100px" 
console.log(computedStyle.height); 			// "200px" 
console.log(computedStyle.border); 			// "0.8px solid rgb(0, 0, 0)"（在某些浏览器中）
```

​		在取得这个元素的计算样式时，`border` 属性不一定返回样式表中实际的 `border` 规则（某些浏览器会）。这种不一致性是因浏览器解释简写样式的方式造成的，比如 `border` 实际上会设置一组别的属性。在设置 `border` 时，实际上设置的是 4 条边的线条宽度、颜色和样式（`border-left-width`、`border-top-color`、`border-bottom-style` 等）。因此，即使 `computedStyle.border` 在所有浏览器中都不会返回值，`computedStyle.borderLeftWidth` 也一定会返回值。

注意：浏览器虽然会返回样式值，但返回值的格式不一定相同。比如，`Firefox` 和 `Safari` 会把所有颜色值转换为 `RGB` 格式（如红色会变成 `rgb(255,0,0)`），而 `Opera` 把所有颜色转换为十六进制表示法（如红色会变成 `#ff0000`）。因此在使用 `getComputedStyle()`时一定要多测试几个浏览器。

​		关于计算样式要记住一点，在所有浏览器中计算样式都是只读的，不能修改 `getComputedStyle()` 方法返回的对象。而且，计算样式还包含浏览器内部样式表中的信息。因此有默认值的 `CSS` 属性会出现在计算样式里。例如，`visibility` 属性在所有浏览器中都有默认值，但这个值因实现而不同。有些浏览器会把 `visibility` 的默认值设置为 `"visible"`，而另一些将其设置为 `"inherit"`。不能假设 `CSS` 属性的默认值在所有浏览器中都一样。如果需要元素具有特定的默认值，那么一定要在样式表中手动指定。



##### 计算样式映射

​		每个元素上都有一个 `computedStyleMap()` 方法，该方法返回一个 `StylePropertyMapReadOnly` 实例。实例中只包含一个 `size` 属性，表示有多少种 `CSS` 属性，与 `CSSStyleDeclaration` 对象的 `length` 值相等。在 `StylePropertyMapReadOnly` 的原型上有一些可以方便地访问 `CSS` 属性的方法。

```js
console.log(myDiv.computedStyleMap()); // StylePropertyMapReadOnly {size: 343}
/*
StylePropertyMapReadOnly {
	size: 343,
	[[Prototype]]: StylePropertyMapReadOnly {
		entries: ƒ entries(),
		forEach: ƒ forEach(),
		get: ƒ (),
		getAll: ƒ getAll(),
		has: ƒ has(),
		keys: ƒ keys(),
		size: 343,
		values: ƒ values(),
		constructor: ƒ StylePropertyMapReadOnly(),
		Symbol(Symbol.iterator): ƒ entries(),
		Symbol(Symbol.toStringTag): "StylePropertyMapReadOnly",
		get size: ƒ size(),
		[[Prototype]]: Object
	}
}
*/
```

​		`get` 方法根据不同的 `CSS` 属性返回不同的值，获取只带数值和单位的属性时，返回一个 `CSSUnitValue` 对象。获取其他属性，则会返回一个 `CSSStyleValue` 对象。

```js
let computedStyleMap = myDiv.computedStyleMap();

console.log(computedStyleMap.get('width')); // CSSUnitValue {value: 100, unit: 'px'}

console.log(computedStyleMap.get('border')); // CSSStyleValue {}
console.log(computedStyleMap.get('border').toString()); // '0.8px solid rgb(0, 0, 0)'
```



#### 操作样式表

​		`CSSStyleSheet` 类型表示 `CSS` 样式表，包括使用 `<link>` 元素和通过 `<style>` 元素定义的样式表。值得一提的是，每个 `<link>` 元素和  `<style>` 元素都会生成一个独立的样式表，且只包含书写在它们内部的样式。注意，这两个元素本身分别是 `HTMLLinkElement` 和 `HTMLStyleElement` 的实例。`CSSStyleSheet` 类型是一个通用样式表类型，可以表示以任何方式在 `HTML` 中定义的样式表。另外，元素特定的类型允许修改 `HTML` 属性，而 `CSSStyleSheet` 类型的实例则是一个只读对象（只有一个属性例外）。

​		`CSSStyleSheet` 类型继承 `StyleSheet`，后者可用作非 `CSS` 样式表的基类。以下是 `CSSStyleSheet` 从 `StyleSheet` 继承的属性

- `disabled`：布尔值，表示样式表是否被禁用了（这个属性是可读写的，因此将它设置为 `true` 会禁用样式表）。
- `href`：如果是使用 `<link>` 包含的样式表，则返回样式表的 `URL`，否则返回 `null`。
- `media`：样式表支持的媒体类型集合，这个集合有一个 `length` 属性和一个 `item()` 方法，跟所有 `DOM` 集合一样。同样跟所有 `DOM` 集合一样，也可以使用中括号访问集合中特定的项。如果样式表可用于所有媒体，则返回空列表。
- `ownerNode`：指向拥有当前样式表的节点，在 `HTML` 中要么是 `<link>` 元素要么是 `<style>` 元素（在 `XML` 中可以是处理指令）。如果当前样式表是通过 `@import` 被包含在另一个样式表中，则这个属性值为 `null`。
- `parentStyleSheet`：如果当前样式表是通过 `@import` 被包含在另一个样式表中，则这个属性指向导入它的样式表。
- `title`：`ownerNode` 的 `title` 属性，默认值 `null`。
- `type`：字符串，表示样式表的类型。对 `CSS` 样式表来说，就是 `"text/css"`。

```css
/* 在外部DOM.css文件中 */
#box {
    border: 1px solid black;
}
```

```html
<!DOCTYPE html>
<html>
<head>
    <title>Computed Styles Example</title>
    <link rel="stylesheet" href="DOM.css" title="link--外部样式">
    <style type="text/css"><!-- 给style元素设置title属性，可能会导致其不可用 -->
        #box {
            width: 100px;
            height: 200px;
        }
    </style>
</head>
    
<body>
    <div id="box" style="background-color: red;"></div>
</body>

<script>
    // 查看样式表
    console.log(document.styleSheets); // StyleSheetList {0: CSSStyleSheet, 1: CSSStyleSheet, length: 2}
    /*
    StyleSheetList {
    	0: CSSStyleSheet {
    		cssRules: [Exception: DOMException: Failed to read the 'cssRules' property ...],
			disabled: false,
			href: "file:///C:/Users/MR/Desktop/%E7%BB%83%E4%B9%A0/DOM.css",
			media: MediaList {length: 0, mediaText: ''},
			ownerNode: link,
			ownerRule: null,
			parentStyleSheet: null,
			rules: (...),
			title: "link--外部样式",
			type: "text/css"
			[[Prototype]]: CSSStyleSheet
    	}, 
    	1: CSSStyleSheet {
    		cssRules: CSSRuleList {0: CSSStyleRule, length: 1},
			disabled: false,
			href: null,
			media: MediaList {length: 0, mediaText: ''},
			ownerNode: style,
			ownerRule: null,
			parentStyleSheet: null,
			rules: CSSRuleList {0: CSSStyleRule, length: 1},
			title: null,
			type: "text/css",
			[[Prototype]]: CSSStyleSheet
    	}, 
    	length: 2,
    	[[Prototype]]: StyleSheetList
    }
    */
</script>
</html>
```

​		上述属性里除了 `disabled`，其他属性都是只读的。除了上面继承的属性，`CSSStyleSheet` 类型还支持以下属性和方法。

- `cssRules`：当前样式表包含的样式规则的集合（`<link>` 引入的外部样式表会被跨域阻止，此时需要设置 `crossorigin` 属性）。
- `ownerRule`：如果样式表是使用 `@import` 导入的，则指向导入规则；否则为 `null`。
- `deleteRule(index)`：在指定位置删除 `cssRules` 中的规则。
- `insertRule(rule, index)`：在指定位置向 `cssRules` 中插入规则。

​		`document.styleSheets` 表示文档中可用的样式表集合（只包含被直接引入到文档的样式表，不包括通过 `@import` 导入的）。

```html
<!-- 当读取外部样式表的cssRules被跨域阻止时，需要在link元素上配置crossorigin属性 -->
<!-- 1、提前配置跨域标志 -->
<link rel="stylesheet" href="DOM.css" crossorigin="anonymous">

<!-- 2、后续配置跨域标志 -->
<script>
	function insertCrossOrigin() {
        let head = document.head;
        document.querySelectorAll('link[rel=stylesheet]').forEach(link => {
            // crossorigin标志只有在元素被创建时设置才会生效，后续添加的无效。
            const newLink = link.cloneNode();
            newLink.setAttribute('crossorigin', 'anonymous');
            head.replaceChild(newLink, link);
        });
    }
</script>
```

​		这个集合的 `length` 属性保存着文档中样式表的数量，而每个样式表都可以使用中括号或 `item()` 方法获取。来看这个例子：

```js
let sheet = null; 

for (let i = 0, len = document.styleSheets.length; i < len; i++) { 
 	sheet = document.styleSheets[i]; 
 	console.log(sheet.href); 
}
/*
> "file:///C:/Users/MR/Desktop/%E7%BB%83%E4%B9%A0/DOM.css"
> null
*/
```

​		以上代码输出了文档中每个样式表的 `href` 属性（`<style>` 元素没有这个属性）。

​		`document.styleSheets` 返回的样式表可能会因浏览器而异。所有浏览器都会包含 `<style>` 元素和 `rel` 属性设置为 `"stylesheet"` 的 `<link>` 元素。`IE`、`Opera`、`Chrome` 也包含 `rel` 属性设置为 `"alternate stylesheet"` 的 `<link>` 元素。

​		通过 `<link>` 或 `<style>` 元素也可以直接获取 `CSSStyleSheet` 对象。`DOM` 在这两个元素上暴露了 `sheet` 属性，其中包含对应的 `CSSStyleSheet` 对象。

```js
document.styleSheets[0] === document.styleSheets[0].ownerNode.sheet; 	// true
document.styleSheets[1] === document.styleSheets[1].ownerNode.sheet; 	// true
```



##### `CSS` 规则

​		`CSSRule` 类型表示样式表中的一条规则。这个类型也是一个通用基类，很多类型都继承它，但其中最常用的是表示样式信息的 `CSSStyleRule`（其他 `CSS` 规则还有 `@import`、`@font-face`、`@page` 和 `@charset` 等，不过这些规则很少需要使用脚本来操作）。以下是 `CSSStyleRule` 对象上可用的属性。

- `cssText`：返回整条规则的文本。这里的文本可能与样式表中实际的文本不一样，因为浏览器内部处理样式表的方式也不一样。`Safari` 始终会把所有字母都转换为小写。
- `parentRule`：如果这条规则被其他规则（如 `@media`）包含，则指向包含规则，否则就是 `null`。
- `parentStyleSheet`：包含当前规则的样式表。
- `selectorText`：返回规则的选择符文本。这里的文本可能与样式表中实际的文本不一样，因为浏览器内部处理样式表的方式也不一样。这个属性在 `Firefox`、`Safari`、`Chrome` 和 `IE` 中是只读的，在 `Opera` 中是可以修改的。
- `style`：返回 `CSSStyleDeclaration` 对象，可以设置和获取当前规则中的样式。
- `type`：数值常量，表示规则类型。对于样式规则，它始终为 1。

​		在这些属性中，使用最多的是 `cssText`、`selectorText` 和 `style`。`cssText` 属性与 `style.cssText` 类似，不过并不完全一样。前者包含选择符文本和环绕样式声明的大括号，而后者则只包含样式声明（类似于元素上的 `style.cssText`）。此外，`cssText` 是只读的，而 `style.cssText` 可以被重写。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Computed Styles Example</title>
    <style type="text/css">
        #myDiv {
            width: 100px;
            height: 200px;
        }
    </style>
</head>

<body>
    <div id="myDiv" style="background-color: red;"></div>
</body>

<script>
    let sheet = document.styleSheets[0];
 	let rules = sheet.cssRules || sheet.rules; // 获取规则集合
    
    console.log(rules); // CSSRuleList {0: CSSStyleRule, length: 1}
    /*
    CSSRuleList {
    	0: CSSStyleRule {
            cssText: "#myDiv { width: 100px; height: 200px; }",
            parentRule: null,
            parentStyleSheet: CSSStyleSheet {ownerRule: null, cssRules: CSSRuleList, type: 'text/css', ...},
            selectorText: "#myDiv",
            style: CSSStyleDeclaration {0: 'width', 1: 'height', accentColor: '', additiveSymbols: '', ...},
            styleMap: StylePropertyMap {size: 2},
            type: 1,
            [[Prototype]]: CSSStyleRule
    	}, 
    	length: 1,
    	[[Prototype]]: CSSRuleList
    }
    */
    
    console.log(rules[0].parentStyleSheet === sheet); // true
</script>
</html>
```

​		多数情况下，使用 `style` 属性就可以实现操作样式规则的任务了，这个 `style` 属性只包含书写该 `style` 元素中的文档样式。它可以像每个元素上的 `style` 对象一样，用来读取或修改规则中的样式。

​		`CSS` 样式规则包括 `CSS` 选择器以及为匹配元素设置的样式，一个 `CSS` 样式块就是一条 `CSS` 样式规则，如下所示：

```vue
<style lang="less">
    /* 第一条规则 */
    div {
        /* 第二条规则 */
        .box {
            width: 100px;
        }
    }
    /* 第三条规则 */
    p {
        font-size: 14px;
    }
</style>
```

```html
<head>
    <!-- 第一个样式表 -->
    <style>
    	/* 第一条规则 */
        .myDiv { 
            width: 100px; 
            height: 200px; 
        }

        /* 第二条规则 */
        h1, h2, h3 {
            font-weight: normal;
        }
    </style>
    
    <!-- 第二个样式表 -->
    <style>
        /* 第一条规则 */
    	a {
            text-decoration: none;
        }
    </style>
</head>
```

```js
let sheets = document.styleSheets; 	// 取得所有的样式表
let sheet1 = sheets[0]; 			// 取得第一个样式表
let sheet2 = sheets[1]; 			// 取得第二个样式表

let rules1 = sheet1.cssRules || sheet1.rules; 	// 取得第一个样式表中的所有规则
let rules2 = sheet2.cssRules || sheet2.rules; 	// 取得第二个样式表中的所有规则

console.log(rules1); // CSSRuleList {0: CSSStyleRule, 1: CSSStyleRule, length: 2}
/*
CSSRuleList {
	0: CSSStyleRule {selectorText: '.myDiv', cssText: '.myDiv { width: 100px; height: 200px; }', ...},
	1: CSSStyleRule {selectorText: 'h1, h2, h3', cssText: 'h1, h2, h3 { font-weight: normal; }', ...},
	length: 2,
	[[Prototype]]: CSSRuleList
}
*/

console.log(rules2); // CSSRuleList {0: CSSStyleRule, length: 1}
/*
CSSRuleList {
	0: CSSStyleRule {selectorText: 'a', cssText: 'a { text-decoration: none; }', ...},
	length: 1,
	[[Prototype]]: CSSRuleList
}
*/
```

​		使用样式规则的 `style` 属性提供的接口，可以像确定元素 `style` 对象中包含的样式一样，确定一条样式规则的样式信息。与元素的场景一样，也可以修改规则中的样式，如下所示：

```js
// 修改第一个样式表，第一条规则.myDiv中的样式
rules1[0].style.backgroundColor = 'red';
```

​		注意，这样修改规则会影响到页面上所有应用了该规则的元素。如果页面上有两个元素的类名为 `".myDiv"`，则这两个元素都会受到这个修改的影响。



##### 创建规则

​		`DOM` 规定，可以使用 `insertRule()` 方法向样式表中添加新规则。这个方法接收两个参数：规则的文本和表示插入位置的索引值。

```js
// 使用DOM方法在第一个样式表中插入一条规则，并将其置顶。
sheet1.insertRule("body { background-color: silver }", 0); // 0
```

​		这个例子插入了一条改变文档背景颜色的规则。这条规则是作为样式表的第一条规则（位置 0）插入的，顺序对于规则层叠是很重要的，默认遵循 “后来者居上” 的覆盖原则。

​		虽然可以这样添加规则，但随着要维护的规则增多，很快就会变得非常麻烦。这时候，更好的方式是使用第 14 章介绍的动态样式加载技术。



##### 删除规则

​		支持从样式表中删除规则的 `DOM` 方法是 `deleteRule()`，它接收一个参数：要删除规则的索引。要删除样式表中的第一条规则，可以这样做：

```js
// 使用DOM方法从第一个样式表中删除一条规则。
sheet1.deleteRule(0); // undefined
```

​		与添加规则一样，删除规则并不是 `Web` 开发中常见的做法。考虑到可能影响 `CSS` 层叠的效果，删除规则时要慎重。



#### 元素尺寸

​		本节介绍的属性和方法并不是 `DOM2 Style` 规范中定义的，但与 `HTML` 元素的样式有关。`DOM` 一直缺乏对页面中元素实际尺寸的规定。`IE` 率先增加了一些属性，向开发者暴露元素的尺寸信息。这些属性现在已经得到所有主流浏览器支持。

<img src="images/DOM2%E5%92%8CDOM3/image-20221128235928478.png" alt="image-20221128235928478" style="zoom:80%;" /> 

- `offsetLeft`：元素边框外侧相对于参考容器元素边框内侧的距离，如果参考容器元素是 `<body>` 或 `<html>`，则基于其边框外侧。
- `offsetHeight`：元素总高度，与 `height` 相等。
- `clientLeft`：元素左边框宽度。
- `clientHeight`：元素的可见区高度，即：总高度 - 边框。
- `left`：元素边框外侧相对于窗口边界的距离。



##### 偏移尺寸

​		第一组属性涉及偏移尺寸（`offset dimensions`），包含元素在屏幕上占用的所有视觉空间。元素在页面上的视觉空间由其高度和宽度决定，包括所有内边距、滚动条和边框（但不包含外边距）。以下 4 个属性用于取得元素的偏移尺寸。

- `offsetHeight`：元素在垂直方向上占用的像素尺寸，包括它的高度、水平滚动条高度（如果可见）和上、下边框的高度。
- `offsetLeft`：元素左边框外侧距离包含元素左边框内侧的像素数。默认相对于 `<html>` 的最左边（**外边框**）。
- `offsetTop`：元素上边框外侧距离包含元素上边框内侧的像素数。默认相对于 `<html>` 的最顶边（**外边框**）。
- `offsetWidth`：元素在水平方向上占用的像素尺寸，包括它的宽度、垂直滚动条宽度（如果可见）和左、右边框的宽度。

​		其中，`offsetLeft` 和 `offsetTop` 是相对于包含元素的，包含元素保存在 `offsetParent` 属性中。`offsetParent` 并不一定总是 `parentNode`。比如，`<td>` 元素的 `offsetParent` 是作为其祖先的 `<table>` 元素，因为 `<table>` 是节点层级中第一个提供尺寸的元素。下图展示了这些属性代表的不同尺寸。【`Left` 和 `Top` 偏移量相对于最近的有定位属性的祖先元素】

​                                                 <img src="images/DOM2%E5%92%8CDOM3/image-20221127222839353.png" alt="image-20221127222839353" style="zoom:80%;" /> 

​		要确定一个元素在页面中的偏移量，可以把它的 `offsetLeft` 和 `offsetTop` 属性分别与 `offsetParent` 的相同属性及边框宽度相加，一直加到根元素（虽然 `<body>` 元素的 `offsetParent` 为 `null`，但是，`offsetLeft` 和 `offsetTop` 的顶层参考元素是 `<html>`，而非 `<body>`，并且它们都会将 `<html>` 和 `<body>` 元素的边框宽度，以及 `<body>` 的偏移量算进去）。



注释：视口元素是 `<html>` 元素，如果 `<body>` 元素没有规定宽高，也没有边框和偏移，则它们基本上是相等的。

```js
// 元素左边相对于视口左边的偏移量 = 元素左边相对于有定位容器元素左边的偏移叠加量 + 有定位容器元素左边框的宽度叠加值。
// 元素左外边相对于<html>左外边的距离
function getElementLeftToHtml(element) {
    let actualLeft = element.offsetLeft;
    let current = element.offsetParent;

    while (current !== null) {
        // 如果<body>元素有边框或偏移量的话，也会被算入其中。要想相对于<body>元素，减去其边框与相关偏移量即可。
        actualLeft += current.offsetLeft + current.computedStyleMap().get('border-left-width').value;
        current = current.offsetParent;
    }
	// 减去多余算入的body的左边框宽度。
    return actualLeft - document.body.computedStyleMap().get('border-left-width').value;
}

// 元素上边相对于视口上边的偏移量 = 元素上边相对于有定位容器元素上边的偏移量叠加 + 有定位容器元素上边框的宽度叠加。
// 元素上外边相对于<html>上外边的距离
function getElementTopToHtml(element) {
    let actualTop = element.offsetTop;
    let current = element.offsetParent;

    while (current !== null) {
        // 如果<body>元素有边框或偏移量的话，也会被算入其中。要想相对于<body>元素，减去其边框与相关偏移量即可。
        actualTop += current.offsetTop + current.computedStyleMap().get('border-top-width').value;
        current = current.offsetParent;
    }
	// 减去多余算入的body的上边框宽度。
    return actualTop - document.body.computedStyleMap().get('border-top-width').value;
}

// 元素左外边相对于<body>元素左内边的距离
function getElementLeftToBody(element) {
    let actualLeft = element.offsetLeft;
    let current = element.offsetParent;
    let bodyComputedStyle = document.defaultView.getComputedStyle(document.body, null);
    let bodyOffsetLeft = parseFloat(bodyComputedStyle.marginLeft || bodyComputedStyle.marginInlineStart);
    let bodyBorderLeft = parseFloat(bodyComputedStyle.borderLeftWidth||bodyComputedStyle.borderInlineStart);

    while (current !== null) {
        actualLeft += current.offsetLeft + current.computedStyleMap().get('border-left-width').value;
        current = current.offsetParent;
    }
	// 实际上，offsetLeft不会记录<body>元素的左偏移量，即：body.offsetLeft总是为0。
    return actualLeft - bodyOffsetLeft - bodyBorderLeft * 2;
}

// 元素上外边相对于<body>元素上内边的距离
function getElementTopToBody(element) {
    let actualTop = element.offsetTop;
    let current = element.offsetParent;
	let bodyComputedStyle = document.defaultView.getComputedStyle(document.body, null);
    let bodyOffsetTop = parseFloat(bodyComputedStyle.marginTop || bodyComputedStyle.marginBlockStart);
    let bodyBorderTop = parseFloat(bodyComputedStyle.borderTopWidth || bodyComputedStyle.borderBlockStart);
    
    while (current !== null) {
        actualTop += current.offsetTop + current.computedStyleMap().get('border-top-width').value;
        current = current.offsetParent;
    }
	// 实际上，offsetTop也不会记录<body>元素的上偏移量，即：body.offsetTop总是为0。
    return actualTop - bodyOffsetTop - bodyBorderTop * 2;
}
```

​		这两个函数使用 `offsetParent` 在 `DOM` 树中逐级上溯，将每一级的偏移属性相加，最终得到元素的实际偏移量。对于使用 `CSS` 布局的简单页面，这两个函数是很精确的。而对于使用表格和内嵌窗格的页面布局，它们返回的值会因浏览器不同而有所差异，因为浏览器实现这些元素的方式不同。

​		一般来说，如果容器元素均无定位属性，则包含在其中的所有元素都将以 `<body>` 为其 `offsetParent`，即：相对于 `<body>` 元素而言。因此 `getElementLeftToBody()` 和 `getElementTopToBody()` 返回的值与 `offsetLeft` 和 `offsetTop` 返回的值相同。

```js
// 如果元素的offsetParent是body元素，元素的offsetLeft和offsetTop都是直接相对于body而言的。
getElementLeftToBody(sub1) === sub1.offsetLeft; // true
getElementTopToBody(sub1) === sub1.offsetTop; 	// true

// 如果元素的offsetParent是其父元素，则其offsetLeft和offsetTop是相对于父元素而言的。
sub1.offsetLeft; 	// 35
sub1.offsetTop; 	// 15
```

注意：所有这些偏移尺寸属性都是只读的，每次访问都会重新计算，以获得最新的尺寸信息。为避免影响性能，应该尽量减少查询它们的次数。比如把查询的值保存在局量中。



##### 客户端尺寸

​		元素的客户端尺寸（`client dimensions`）包含元素内容及其内边距所占用的空间。客户端尺寸具有四个相关属性：`clientWidth` 和 `clientHeight`，以及 `clientLeft` 和 `clientTop`。其中，`clientWidth` 是内容区宽度加左、右内边距宽度，`clientHeight` 是内容区高度加上、下内边距高度。下图形象地展示了这两个属性。

​                                                      <img src="images/DOM2%E5%92%8CDOM3/image-20221128103206828.png" alt="image-20221128103206828" style="zoom:80%;" /> 

​		客户端尺寸实际上就是元素内部的空间，因此不包含滚动条占用的空间。这两个属性最常用于确定浏览器视口尺寸，即检测 `document.documentElement` 的 `clientWidth` 和 `clientHeight`。这两个属性表示视口（即：`<html>` 元素）的尺寸。

```html
<div id="box"></div>
<style>
    body {
        width: 1200px;
        height: 800px;
    }
    #box {
        width: 200px;
        height: 100px;
        border: 10px solid red;
        border-top-width: 5px;
    }
</style>
```

```js
// <html>元素的clientWidth和clientHeight与<body>元素的并不一定相等。
document.documentElement.clientWidth; // 1519，减去了滚动条的宽度
document.body.clientWidth; // 1200
document.documentElement.clientHeight; // 486
document.body.clientHeight; // 800

// 元素的clientWidth和clientHeight
let box = document.querySelector("#box");
box.clientWidth; 	// 200
box.clientHeight; 	// 100
```

​		 `clientLeft` 和 `clientTop` 分别表示元素的左边框宽度和上边框宽度。

```js
box.clientLeft; // 10
box.clientTop;  // 5

// 由于浏览器实现的差异，计算样式对于宽高度的计算并不一定准确，以下两等式不总是相等，但近似相等。
box.clientLeft === box.computedStyleMap().get('border-left-width').value;
box.clientTop === box.computedStyleMap().get('border-top-width').value;

box.computedStyleMap().get('border-left-width').value; // 9.600000381469727
box.computedStyleMap().get('border-top-width').value;  // 4.800000190734863
```

注意：与偏移尺寸一样，客户端尺寸也是只读的，而且每次访问都会重新计算。



##### 滚动尺寸

​		最后一组尺寸是滚动尺寸（`scroll dimensions`），提供了元素内容滚动距离的信息。有些元素，比如 `<html>` 无须任何代码就可以自动滚动，而其他元素则需要使用 `CSS` 的 `overflow` 属性令其滚动。与滚动尺寸相关的属性有如下 4 个。

- `scrollHeight`：没有滚动条出现时，元素内容的总高度。
- `scrollLeft`：内容区左侧隐藏的像素数，设置这个属性可以改变元素的滚动位置。
- `scrollTop`：内容区顶部隐藏的像素数，设置这个属性可以改变元素的滚动位置。
- `scrollWidth`：没有滚动条出现时，元素内容的总宽度。

​	                              <img src="images/DOM2%E5%92%8CDOM3/image-20221128103636599.png" alt="image-20221128103636599" style="zoom:80%;" />

​		`scrollWidth` 和 `scrollHeight` 可以用来确定给定元素内容的实际尺寸。例如，`<html>` 元素是浏览器中滚动视口的元素。因此，`document.documentElement.scrollHeight` 就是整个页面垂直方向的总高度。

```html
<style>
    body {
        height: 800px;
    }
</style>
<script>
	// 内容总高度
    console.log(document.documentElement.scrollHeight); // 800
    // 视口高度
    console.log(document.documentElement.clientHeight); // 452
</script>
```

​		`scrollWidth` 和 `scrollHeight` 与 `clientWidth` 和 `clientHeight` 之间的关系在不需要滚动的文档上是分不清的。如果文档尺寸超过视口尺寸，则在所有主流浏览器中这两对属性都不相等，`scrollWidth` 和 `scollHeight` 等于文档内容的宽度，而 `clientWidth` 和 `clientHeight` 等于视口的大小。

​		元素视口会根据滚动条的尺寸自适应元素剩余的可见区。如果元素的内容尺寸小于其视口尺寸，则内容尺寸会与视口尺寸平齐，此时的可滚动的距离为 0。如果内容尺寸大于视口尺寸，则内容会超出可见区，超出部分被遮盖，最大可滚动距离为内容尺寸减去视口尺寸。内边框之外的内容都是不可见的，因此，滚动距离都是内边距外侧相对于容器元素的边框内侧而言的，这两条线就是计算滚长的参考线。

```html
<div id="box">
    <p>第1个段落</p>
    <p>第2个段落</p>
    <p>第3个段落</p>
    <p>第4个段落</p>
    <p>第5个段落</p>
    <p>第6个段落</p>
    <p>第7个段落</p>
    <p>第8个段落</p>
    <p>第9个段落</p>
    <p>第10个段落</p>
</div>
<style>
    * {
        margin: 0;
        padding: 0;
    }
    #box {
        width: 200px;
        height: 100px;
        border: 10px solid red;
        margin: 100px auto;
        overflow: auto;
    }
    #box p {
        height: 21px;
    }
</style>
<script>
    let box = document.querySelector("#box");

    // 视口尺寸
    console.log(box.clientWidth); 	// 183，减去了纵向滚动条的宽度
    console.log(box.clientHeight); 	// 100
    
    // 内容尺寸（内容的宽度不超出，高度超出）
    console.log(box.scrollWidth); 	// 183，内容宽度与视口宽度平齐
    console.log(box.scrollHeight); 	// 210

    // 滚动距离（纵向最大滚动长度 = 内容高度 - 视口高度）
    console.log(box.scrollTop); 	// 0
    console.log(box.scrollLeft); 	// 0

    // 滚动相对于容器的内边框线，使内容在纵向上向上滚动105px。
    box.scrollTop = 105; // 滚动到“第6个段落”。
</script>
```

​		`scrollLeft` 和 `scrollTop` 属性可以用于确定内容已滚动的距离（即：滚动条的当前位置），或者用于设置它们的滚动距离。元素在未滚动时，这两个属性都等于 0。如果元素在垂直方向上滚动，则 `scrollTop` 会大于 0，表示元素内容在顶部不可见区域的高度。如果元素在水平方向上滚动，则 `scrollLeft` 会大于 0，表示元素内容在左侧不可见区域的宽度。这两个属性都是可写的，所以把它们都设置为 0 就可以重置元素内容的滚动距离（即：使滚动条置顶）。

​		下面这个函数检测元素内容（或滚动条）是不是正位于顶部，如果不是则把它滚动回顶部：

```js
function scrollToTop(element) { 
 	if (element.scrollTop !== 0) { 
 		element.scrollTop = 0; 
 	} 
}
```



##### 确定元素尺寸

​		浏览器在每个元素上都暴露了 `getBoundingClientRect()` 方法，返回一个 `DOMRect` 对象，包含 6 个属性：`left`、`top`、`right`、`bottom`、`height` 和 `width`。这些属性给出了元素在页面中相对于窗口的位置。

​                                      <img src="images/DOM2%E5%92%8CDOM3/image-20221128103955544.png" alt="image-20221128103955544" style="zoom:80%;" /> 

```html
<div id="box">
    <p>第1个段落</p>
    <p>第2个段落</p>
    <p>第3个段落</p>
    <p>第4个段落</p>
    <p>第5个段落</p>
    <p>第6个段落</p>
    <p>第7个段落</p>
    <p>第8个段落</p>
    <p>第9个段落</p>
    <p>第10个段落</p>
</div>
<style>
    * {
        margin: 0;
        padding: 0;
    }
    #box {
        width: 200px;
        height: 100px;
        border: 10px solid red;
        margin: 100px auto;
        overflow: auto;
    }
    #box p {
        height: 21px;
    }
</style>
```

```js
let box = document.querySelector("#box");
let position = box.getBoundingClientRect();

console.log(position.x); // DOMRect {x: 658.4000244140625, y: 100, width: 219.1999969482422, ...}
/*
DOMRect {
	bottom: 219.20000457763672, // 等于：top + height（元素总高度）
	height: 119.20000457763672, // 元素总高度
	left: 658.4000244140625, 	// 相对于窗口左边界
	right: 877.6000213623047, 	// 等于：left + width（元素总宽度）
	top: 100, 					// 相对于窗口上边界
	width: 219.1999969482422, 	// 元素总宽度
	x: 658.4000244140625, 		// 等于：left
	y: 100, 					// 等于：top
	[[Prototype]]: DOMRect
}
*/
```



### 遍历

​		`DOM2 Traversal and Range` 模块定义了两个类型用于辅助顺序遍历 `DOM` 结构。这两个类型—— `NodeIterator` 和 `TreeWalker`——从某个起点开始执行对 `DOM` 结构的深度优先遍历。

​		如前所述，`DOM` 遍历是对 `DOM` 结构的深度优先遍历，至少允许朝两个方向移动（取决于类型）。遍历以给定节点为根，不能在 `DOM` 中向上超越这个根节点。来看下面的 `HTML`：

```html
<!DOCTYPE html> 
<html> 
 	<head> 
 		<title>Example</title> 
 	</head> 
 	<body>
      	<p><b>Hello</b> world!</p> 
 	</body> 
</html>
```

​		这段代码构成的 `DOM` 树如下图所示。

​                                                      <img src="images/DOM2%E5%92%8CDOM3/image-20221128223736302.png" alt="image-20221128223736302" style="zoom:80%;" /> 

​		其中的任何节点都可以成为遍历的根节点。比如，假设以 `<body>` 元素作为遍历的根节点，那么接下来是 `<p>` 元素、`<b>` 元素和两个文本节点（都是 `<body>` 元素的后代）。但这个遍历不会到达 `<html>` 元素、`<head>` 元素，或者其他不属于 `<body>` 元素子树的元素。而以 `document` 为根节点的遍历，则可以访问到文档中的所有节点。下图展示了以 `document` 为根节点的深度优先遍历。

​		                                      <img src="images/DOM2%E5%92%8CDOM3/image-20221128224232956.png" alt="image-20221128224232956" style="zoom:80%;" />

​		从 `document` 开始，然后循序移动，第一个节点是 `document`，最后一个节点是包含 `" world!"` 的文本节点。到达文档末尾最后那个文本节点后，遍历会在 `DOM` 树中反向回溯。此时，第一个访问的节点就是包含 `" world!"` 的文本节点，而最后一个是 `document` 节点本身。`NodeIterator` 和 `TreeWalker` 都以这种方式进行遍历。



#### `NodeIterator`

​		`NodeIterator` 类型是两个类型中比较简单的，可以通过 `document.createNodeIterator()` 方法创建其实例（节点迭代器）。这个方法接收以下 4 个参数。

- `root`：作为遍历根节点的节点（迭代的起点）。
- `whatToShow`：数值代码，表示应该访问哪些节点（迭代的节点类型）。
- `filter`：`NodeFilter` 对象或函数，表示是否接收或跳过特定节点（迭代的节点过滤器）。
- `entityReferenceExpansion`：布尔值，表示是否扩展实体引用。这个参数在 `HTML` 文档中没有效果，因为实体引用永远不扩展。

​		`whatToShow` 参数是一个位掩码，通过应用一个或多个过滤器来指定访问哪些节点。这个参数对应的常量是在 `NodeFilter` 类型中定义的。

- `NodeFilter.SHOW_ALL`：所有节点 `(4294967295)`。
- `NodeFilter.SHOW_ELEMENT`：元素节点 `(1)`。
- `NodeFilter.SHOW_ATTRIBUTE`：属性节点 `(2)`。由于 `DOM` 的结构，因此实际上用不上。
- `NodeFilter.SHOW_TEXT`：文本节点 `(4)`。
- `NodeFilter.SHOW_CDATA_SECTION`：`CData` 区块节点 `(8)`。不是在 `HTML` 页面中使用的。
- `NodeFilter.SHOW_ENTITY_REFERENCE`：实体引用节点 `(16)`。不是在 `HTML` 页面中使用的。
- `NodeFilter.SHOW_ENTITY`：实体节点 `(32)`。不是在 `HTML` 页面中使用的。
- `NodeFilter.SHOW_PROCESSING_INSTRUCTION`：处理指令节点 `(64)`。不是在 `HTML` 页面中使用的。
- `NodeFilter.SHOW_COMMENT`：注释节点 `(128)`。
- `NodeFilter.SHOW_DOCUMENT`：文档节点 `(256)`。
- `NodeFilter.SHOW_DOCUMENT_TYPE`：文档类型节点 `(512)`。
- `NodeFilter.SHOW_DOCUMENT_FRAGMENT`：文档片段节点 `(1024)`。不是在 `HTML` 页面中使用的。
- `NodeFilter.SHOW_NOTATION`：记号节点 `(2048)`。不是在 `HTML` 页面中使用的。

​		这些值除了 `NodeFilter.SHOW_ALL` 之外，都可以组合使用。比如，可以像下面这样使用按位或操作组合多个选项：

```js
// 注释：若按位或运算的两个操作数都是2^N，那么按位或运算的结果为：两数之和；意为：包含这两个数。
let whatToShow = NodeFilter.SHOW_ELEMENT | NodeFilter.SHOW_TEXT; // 5
```

​		`createNodeIterator()` 方法的 `filter` 参数表示一个节点过滤器，可以指定为自定义的 `NodeFilter` 对象，或者一个节点过滤器函数。`NodeFilter` 对象只有一个 `acceptNode()` 方法，如果指定的节点应该被接收，那么就返回 `NodeFilter.FILTER_ACCEPT`，否则返回 `NodeFilter.FILTER_SKIP`。因为 `NodeFilter` 是一个抽象类型，所以不可能创建它的实例。只要创建一个包含 `acceptNode()` 的对象，然后把它传给 `createNodeIterator()` 就可以了。以下代码定义了只接收 `<p>` 元素的节点过滤器对象：

```js
let filter = { 
    // 当迭代器调用 nextNode() 或 previousNode() 方法时，会使用该方法迭代过滤。
 	acceptNode(node) {
        // 前两个参数过滤后的结果，默认包含从root节点开始的每个后代节点。
        // 当执行nextNode方法后会开始迭代这个过滤结果，每次迭代都调用会acceptNode方法并将一个节点作为首参传入。
        // 当触发该方法后，它会根据过滤规则去匹配每次传入的node参数节点，寻找符合过滤要求的节点，直至过滤完所有节点。
        // 如果是初次触发该方法，则node从第一个节点开始过滤，找到后立即停止迭代。后续触发则从上次停止的位置继续迭代。
        // 如果没有使用return来指定过滤规则，那么每次触发都会将前两个参数过滤的结果节点从头遍历到尾，不断调用该方法。
 		return node.tagName.toLowerCase() === "p" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP; 
	} 
}; 
// 指定迭代的根节点为root，迭代的节点类型为元素节点，过滤器只接收p元素节点。
let iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false);
```

​		`filter` 参数还可以是一个函数，与 `acceptNode()` 的形式一样，如下面的例子所示：

```js
let filter = function(node) { 
 	return node.tagName.toLowerCase() == "p" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP; 
}; 

let iterator = document.createNodeIterator(root, NodeFilter.SHOW_ELEMENT, filter, false);
```

​		通常，`JavaScript` 会使用这种形式，因为更简单也更像普通 `JavaScript` 代码。如果不需要过滤器，则可以给它传入 `null`。

​		要创建一个简单的遍历所有节点的 `NodeIterator`，可以使用以下代码：

```js
let iterator = document.createNodeIterator(document, NodeFilter.SHOW_ALL, null, false);

// 当然，也可以在过滤器方法中始终返回NodeFilter.FILTER_ACCEPT。但这样不如直接传入一个null方便简洁。
document.createNodeIterator(document, NodeFilter.SHOW_ALL, () => NodeFilter.FILTER_ACCEPT, false);
```

​		深度刨析 `NodeIterator` 节点迭代器的工作原理。

```html
<div id="box">
    <!-- 插入第一个div元素 -->
    <div>1</div>
    <p>第1个段落</p>
    <!-- 插入第二个div元素 -->
    <div>2</div>
    <p>第2个段落</p>
    <p>第3个段落</p>
    <p>第4个段落</p>
    <p>第5个段落</p>
    <!-- 插入第三个div元素 -->
    <div>3</div>
    <p>第6个段落</p>
    <p>第7个段落</p>
    <p>第8个段落</p>
    <!-- 插入第一个span元素 -->
    <span>4</span>
    <p>第9个段落</p>
    <p>第10个段落</p>
</div>
<script>
    let box = document.querySelector("#box");

    let iterator1 = document.createNodeIterator(box, NodeFilter.SHOW_ELEMENT, {
        acceptNode(node) {
            console.log(node, 1); // 每次执行打印node参数。
        }
    }, false);
    
    // 打印迭代器对象。
    iterator1; // NodeIterator {root: div#box, referenceNode: div#box, whatToShow: 1, filter: {…}, ...}
    /*
    NodeIterator {
    	filter: {acceptNode: ƒ},
		pointerBeforeReferenceNode: true,
		referenceNode: div#box, // 该值是随遍历而改变的，指向当前引用的节点，也是使上次迭代暂停的节点。【只读】
		root: div#box, // 该值是固定不变的，是实时反映DOM结构变化的内部指针。
		whatToShow: 1,
		[[Prototype]]: NodeIterator {
			detach: ƒ detach(),
			filter: (...),
			nextNode: ƒ nextNode(),
			pointerBeforeReferenceNode: (...),
			previousNode: ƒ previousNode(),
			referenceNode: (...),
			root: (...),
			whatToShow: (...),
			constructor: ƒ NodeIterator(),
			Symbol(Symbol.toStringTag): "NodeIterator",
			get filter: ƒ filter(),
			get pointerBeforeReferenceNode: ƒ pointerBeforeReferenceNode(),
			get referenceNode: ƒ referenceNode(),
			get root: ƒ root(),
			get whatToShow: ƒ whatToShow(),
			[[Prototype]]: Object
		}
    }
    */
    
    // 寻找第一个符合过滤规则的节点。
    iterator1.nextNode(); // 由于没有指定过滤规则，acceptNode过滤器方法将被陆续调用执行，直至迭代完初步过滤的结果。
    /*
    > <div id="box">...</div> 1 // 第一次调用过滤器方法
    > <div>1</div> 1 			// 第二次调用过滤器方法
    > <p>第1个段落</p> 1
    > <div>2</div> 1
    > <p>第2个段落</p> 1
    > <p>第3个段落</p> 1
    > <p>第4个段落</p> 1
    > <p>第5个段落</p> 1
    > <div>3</div> 1
    > <p>第6个段落</p> 1
    > <p>第7个段落</p> 1
    > <p>第8个段落</p> 1
    > <span>4</span> 1
    > <p>第9个段落</p> 1
    > <p>第10个段落</p> 1 		// 结束调用过滤器方法
    */
    
    let iterator2 = document.createNodeIterator(box, NodeFilter.SHOW_ELEMENT, {
        acceptNode(node) {
            console.log(node, 1); // 每次执行打印node参数。
            // 指定过滤规则，寻找<p>元素节点。
            return node.tagName.toLowerCase() == "p" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP;
        }
    }, false);
    
    // 寻找第一个符合过滤规则的节点。
    iterator2.nextNode(); // 从根节点开始，寻找<p>元素，找到后立即停止调用过滤器方法。
    /*
    > <div id="box">...</div> 1 	// 第一次调用过滤器方法
    > <div>1</div> 1 				// 第二次调用过滤器方法
    > <p>第1个段落</p> 1 			 // 停止调用过滤器方法
    */
    
    // 寻找第二个符合过滤规则的节点。
    iterator2.nextNode(); // 从上次停止的节点继续，寻找<p>元素，找到后立即停止调用过滤器方法。
    /*
    > <div>2</div> 1 	// 第一次重启过滤器方法
    > <p>第2个段落</p> 1 // 停止调用过滤器方法
    */
    
    let iterator3 = document.createNodeIterator(box, NodeFilter.SHOW_ELEMENT, {
        acceptNode(node) {
            // 指定过滤规则，寻找<p>元素节点。
            return node.tagName.toLowerCase() == "p" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP;
        }
    }, false);
    
    // 每次返回找到的符合过滤规则的节点，默认null。
    iterator3.nextNode(); // <p>第1个段落</p>
    iterator3.nextNode(); // <p>第2个段落</p>
    iterator3.nextNode(); // <p>第3个段落</p>
</script>
```

​		`NodeIterator` 的两个主要方法是 `nextNode()` 和 `previousNode()`。`nextNode()` 方法在 `DOM` 子树中以深度优先的方式前进一步，而 `previousNode()` 则是在遍历中后退一步。创建 `NodeIterator` 对象的时候，会有一个内部指针指向根节点，因此第一次调用 `nextNode()` 返回的是根节点。当遍历到达 `DOM` 树最后一个节点时，`nextNode()` 返回 `null`。`previousNode()` 方法也是类似的。当反向遍历到达 `DOM` 树最后一个节点时，调用 `previousNode()` 返回遍历的根节点后，再次调用也会返回 `null`。

​		以下面的 `HTML` 片段为例：

```html
<div id="div1"> 
 	<p><b>Hello</b> world!</p> 
 	<ul> 
		<li>List item 1</li>
		<li>List item 2</li>
		<li>List item 3</li>
 	</ul> 
</div>
```

​		假设想要遍历 `<div>` 元素内部的所有元素，那么可以使用如下代码：

```js
let div = document.getElementById("div1"); 
let iterator = document.createNodeIterator(div, NodeFilter.SHOW_ELEMENT, null, false); 
let node = iterator.nextNode(); 

while (node !== null) { 
 	console.log(node.tagName); // 输出标签名
 	node = iterator.nextNode(); 
}
```

​		这个例子中第一次调用 `nextNode()` 返回 `<div>` 元素。因为 `nextNode()` 在遍历到达 `DOM` 子树末尾时返回 `null`，所以这里通过 `while` 循环检测每次调用 `nextNode()` 的返回值是不是 `null`。以上代码执行后会输出以下标签名：

```js
/*
> "DIV" 
> "P" 
> "B" 
> "UL" 
> "LI" 
> "LI" 
> "LI"
*/
```

​		如果只想遍历 `<li>` 元素，可以传入一个过滤器，比如：

```js
let div = document.getElementById("div1"); 
let filter = function(node) { 
 	return node.tagName.toLowerCase() == "li" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP; 
}; 

let iterator = document.createNodeIterator(div, NodeFilter.SHOW_ELEMENT, filter, false); 
let node = iterator.nextNode(); 

while (node !== null) { 
 	console.log(node.tagName); // 输出标签名
 	node = iterator.nextNode(); 
}
```

​		在这个例子中，遍历只会输出 `<li>` 元素的标签。

​		`nextNode()` 和 `previousNode()` 方法共同维护 `NodeIterator` 对 `DOM` 结构的内部指针，因此修改 `DOM` 结构也会体现在遍历中。



#### `TreeWalker`

​		`TreeWalker` 是 `NodeIterator` 的高级版。除了包含同样的 `nextNode()`、`previousNode()` 方法，`TreeWalker` 还添加了如下在 `DOM` 结构中向不同方向遍历的方法。

- `parentNode()`：遍历到当前节点的父节点。
- `firstChild()`：遍历到当前节点的第一个子节点。
- `lastChild()`：遍历到当前节点的最后一个子节点。
- `nextSibling()`：遍历到当前节点的下一个同胞节点。
- `previousSibling()`：遍历到当前节点的上一个同胞节点。

​		`TreeWalker` 对象要调用 `document.createTreeWalker()` 方法来创建，这个方法接收与 `document.createNodeIterator()` 同样的参数：作为遍历起点的根节点、要查看的节点类型、节点过滤器和一个表示是否扩展实体引用的布尔值。因为两者很类似，所以 `TreeWalker` 通常可以取代 `NodeIterator`，比如：

```js
let div = document.getElementById("div1"); 
let filter = function(node) { 
 	return node.tagName.toLowerCase() == "li" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_SKIP; 
};

let walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT, filter, false); 

walker; // TreeWalker {root: div#div1, whatToShow: 1, currentNode: div#div1, filter: ƒ}
/*
TreeWalker {
	currentNode: div#div1,
	filter: ƒ (node),
	root: div#div1,
	whatToShow: 1,
	[[Prototype]]: TreeWalker {
		currentNode: (...),
		filter: (...),
		firstChild: ƒ firstChild(),
		lastChild: ƒ lastChild(),
		nextNode: ƒ nextNode(),
		nextSibling: ƒ nextSibling(),
		parentNode: ƒ parentNode(),
		previousNode: ƒ previousNode(),
		previousSibling: ƒ previousSibling(),
		root: (...),
		whatToShow: (...),
		constructor: ƒ TreeWalker(),
		Symbol(Symbol.toStringTag): "TreeWalker",
		get currentNode: ƒ currentNode(),
		set currentNode: ƒ currentNode(),
		get filter: ƒ filter(),
		get root: ƒ root(),
		get whatToShow: ƒ whatToShow(),
		[[Prototype]]: Object
	}
}
*/

let node = walker.nextNode(); 

while (node !== null) { 
 	console.log(node.tagName); // 输出标签名
 	node = walker.nextNode(); 
}
```

​		不同的是，节点过滤器（`filter`）除了可以返回 `NodeFilter.FILTER_ACCEPT` 和 `NodeFilter.FILTER_SKIP` 之外，还可以返回 `NodeFilter.FILTER_REJECT`。在使用 `NodeIterator` 时，`NodeFilter.FILTER_SKIP` 和 `NodeFilter.FILTER_REJECT` 是一样的。但在使用 `TreeWalker` 时，`NodeFilter.FILTER_SKIP` 表示跳过节点，访问子树中的下一个节点，而 `NodeFilter.FILTER_REJECT` 则表示跳过该节点以及该节点的整个子树。例如，如果把前面示例中的过滤器函数改为返回 `NodeFilter.FILTER_REJECT`（而不是 `NodeFilter.FILTER_SKIP`），则会导致遍历立即返回，不会访问任何节点。这是因为第一个返回的元素是 `<div>`，其中标签名不是 `"li"`，因此过滤函数返回 `NodeFilter.FILTER_REJECT`，表示要跳过 `<div>` 及其整个子树。因为 `<div>` 本身就是遍历的根节点，所以遍历会就此结束，并且返回 `null`。

```js
let div = document.getElementById("div1"); 
let filter = function(node) { 
 	return node.tagName.toLowerCase() == "li" ? NodeFilter.FILTER_ACCEPT : NodeFilter.FILTER_REJECT; 
};

let walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT, filter, false); 
let node = walker.nextNode(); // null

while (node !== null) { 
 	console.log(node.tagName); // 输出标签名
 	node = walker.nextNode(); 
}
```

​		当然，`TreeWalker` 真正的威力是可以在 `DOM` 结构中四处游走。如果不使用过滤器，单纯使用 `TreeWalker` 的漫游能力同样可以在 `DOM` 树中访问 `<li>` 元素，比如：

```js
let div = document.getElementById("div1"); 
let walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT, null, false); 

walker.firstChild(); // 前往<p> 
walker.nextSibling(); // 前往<ul> 

let node = walker.firstChild(); // 前往第一个<li> 

while (node !== null) { 
 	console.log(node.tagName); 
 	node = walker.nextSibling(); 
}
```

​		因为我们知道 `<li>` 元素在文档结构中的位置，所以可以直接定位过去。先使用 `firstChild()` 前往 `<p>` 元素，再通过 `nextSibling()` 前往 `<ul>` 元素，然后使用 `firstChild()` 到达第一个 `<li>` 元素。注意，此时的 `TreeWalker` 只返回元素（这是因为传给 `createTreeWalker()` 的第二个参数将节点类型限制为元素节点）。最后就可以使用 `nextSibling()` 访问每个 `<li>` 元素，直到再也没有元素，此时方法返回 `null`。

​		`TreeWalker` 类型也有一个名为 `currentNode` 的属性，表示遍历过程中上一次返回的节点（无论使用的是哪个遍历方法）。可以通过修改这个属性来影响接下来遍历的起点，如下面的例子所示：

```js
let node = walker.nextNode(); 
console.log(node === walker.currentNode); 	// true 
walker.currentNode = document.body; 		// 修改起点
```

​		相比于 `NodeIterator`，`TreeWalker` 类型为遍历 `DOM` 提供了更大的灵活性。另外，在调用 `nextNode()` 方法时，`NodeIterator` 能够返回遍历的根节点，且在初次默认返回根节点；而 `TreeWalker` 不会返回遍历的根节点，初次返回根节点子树中的第一个子节点。

```js
let iterator = document.createNodeIterator(div, NodeFilter.SHOW_ELEMENT, null, false);
let walker = document.createTreeWalker(div, NodeFilter.SHOW_ELEMENT, null, false);

iterator.nextNode(); 	// <div id="div">...</div>
walker.nextNode(); 		// <p>...</p>
```



### 范围

​		为了支持对页面更细致的控制，`DOM2 Traversal and Range` 模块定义了范围接口。范围可用于在文档中选择内容，而不用考虑节点之间的界限（选择在后台发生，用户是看不到的）。范围在常规 `DOM` 操作的粒度不够时可以发挥作用。



#### `DOM` 范围

​		`DOM2` 在 `Document` 类型上定义了一个 `createRange()` 方法，暴露在 `document` 对象上。使用这个方法可以创建一个 `DOM` 范围对象，如下所示：

```js
document.createRange();
/*
Range {
	collapsed: true,
	commonAncestorContainer: document,
	endContainer: document,
	endOffset: 0,
	startContainer: document,
	startOffset: 0,
	[[Prototype]]: Range {
		END_TO_END: 2,
		END_TO_START: 3,
		START_TO_END: 1,
		START_TO_START: 0,
		cloneContents: ƒ cloneContents(),
		cloneRange: ƒ cloneRange(),
		collapse: ƒ collapse(),
		commonAncestorContainer: (...),
		compareBoundaryPoints: ƒ compareBoundaryPoints(),
		comparePoint: ƒ comparePoint(),
		createContextualFragment: ƒ createContextualFragment(),
		deleteContents: ƒ deleteContents(),
		detach: ƒ detach(),
		expand: ƒ expand(),
		extractContents: ƒ extractContents(),
		getBoundingClientRect: ƒ getBoundingClientRect(),
		getClientRects: ƒ getClientRects(),
		insertNode: ƒ insertNode(),
		intersectsNode: ƒ intersectsNode(),
		isPointInRange: ƒ isPointInRange(),
		selectNode: ƒ selectNode(),
		selectNodeContents: ƒ selectNodeContents(),
		setEnd: ƒ setEnd(),
		setEndAfter: ƒ setEndAfter(),
		setEndBefore: ƒ setEndBefore(),
		setStart: ƒ setStart(),
		setStartAfter: ƒ setStartAfter(),
		setStartBefore: ƒ setStartBefore(),
		surroundContents: ƒ surroundContents(),
		toString: ƒ toString(),
		constructor: ƒ Range(),
		Symbol(Symbol.toStringTag): "Range",
		collapsed: (...),
		endContainer: (...),
		endOffset: (...),
		startContainer: (...),
		startOffset: (...),
		get commonAncestorContainer: ƒ commonAncestorContainer(),
		[[Prototype]]: AbstractRange
	}
}
*/
```

​		与节点类似，这个新创建的范围对象是与创建它的文档关联的，不能在其他文档中使用。然后可以使用这个范围在后台选择文档特定的部分。创建范围并指定它的位置之后，可以对范围的内容执行一些操作，从而实现对底层 `DOM` 树更精细的控制。

​		每个范围都是 `Range` 类型的实例，拥有相应的属性和方法。下面的属性提供了与范围在文档中位置相关的信息。

- `startContainer`：范围起点所在的父节点（选区中第一个子节点的父节点）。
- `startOffset`：范围起点在 `startContainer` 中的偏移量。如果 `startContainer` 是文本节点、注释节点或 `CData` 区块节点，则 `startOffset` 指范围起点之前跳过的字符数；否则，表示范围中第一个节点的索引。
- `endContainer`：范围终点所在的父节点（选区中最后一个子节点的父节点）。
- `endOffset`：范围终点在 `endContainer` 中的偏移量（与 `startOffset` 中偏移量的含义相同）。
- `commonAncestorContainer`：在文档中 `startContainer` 和 `endContainer` 的最小公共容器（即：最近的公共祖先容器）。如果此二者属于包含关系，则最小公共容器为其中较大者；如果不是包含关系，则为同时包含此二者的最小公共容器。

​		这些属性会在范围被放到文档中特定位置时获得相应的值。



#### 简单选择

​		通过范围选择文档中某个部分，最简单的方式就是使用 `selectNode()` 或 `selectNodeContents()` 方法。这两个方法都接收一个节点作为参数，并将该节点的信息添加到调用它的范围。`selectNode()` 方法选择整个节点，包括其后代节点，而 `selectNodeContents` 方法只选择节点的后代。假设有如下 `HTML`：

```html
<!DOCTYPE html> 
<html> 
 	<body> 
 		<p id="p1"><b>Hello</b> world!</p> 
 	</body> 
</html>
```

​		以下 `JavaScript` 代码可以访问并创建相应的范围：

```js
let range1 = document.createRange(), 
 	range2 = document.createRange(), 
 	p1 = document.getElementById("p1"); 

range1.selectNode(p1); 
range2.selectNodeContents(p1); 
```

​		例子中的这两个范围包含文档的不同部分。`range1` 包含 `<p>` 元素及其所有后代，而 `range2` 包含 `<b>` 元素、文本节点 `"Hello"` 和文本节点 `" world!"`，如图所示。

​                                                                <img src="images/DOM2%E5%92%8CDOM3/image-20221129214848502.png" alt="image-20221129214848502" style="zoom: 80%;" /> 

​		调用 `selectNode()` 时，`startContainer`、`endContainer` 和 `commonAncestorContainer` 都等于传入节点的父节点。在这个例子中，这几个属性都等于 `document.body`。`startOffset` 属性等于传入节点在其父节点 `childNodes` 集合中的索引（在这个例子中，`startOffset` 等于 1，因为 `DOM` 的合规实现把空格当成文本节点），而 `endOffset` 等于 `startOffset` 加上 1（因为只选择了一个节点）。实际上，`startContainer`、`endContainer` 和 `commonAncestorContainer` 这三者默认都是相同的，都表示包含范围的容器。

```js
range1; 
/* Range {
	collapsed: false,
	commonAncestorContainer: body,
	endContainer: body,
	endOffset: 2,
	startContainer: body,
	startOffset: 1,
	[[Prototype]]: Range
}
*/
```

​		在调用 `selectNodeContents()` 时，`startContainer`、`endContainer` 和 `commonAncestorContainer` 属性就是传入的节点，在这个例子中是 `<p>` 元素。`startOffset` 属性始终为 0，因为范围从传入节点的第一个子节点开始，而 `endOffset` 等于传入节点的子节点数量（`node.childNodes.length`），在这个例子中等于 2。

```js
range2;
/*
Range {
	collapsed: false,
	commonAncestorContainer: p#p1,
	endContainer: p#p1,
	endOffset: 2,
	startContainer: p#p1,
	startOffset: 0,
	[[Prototype]]: Range
}
*/
```

​		在像上面这样选定节点或节点后代之后，还可以在范围上调用相应的方法，实现对范围中选区的更精细控制。

- `setStartBefore(refNode)`：把范围的起点设置到 *`refNode`* 之前，从而让 *`refNode`* 成为选区的第一个子节点。`startContainer` 属性被设置为 `refNode.parentNode`，而 `startOffset` 属性被设置为 *`refNode`* 在其父节点 `childNodes` 集合中的索引。
- `setStartAfter(refNode)`：把范围的起点设置到 *`refNode`* 之后，从而将 *`refNode`* 排除在选区之外，让其下一个同胞节点成为选区的第一个子节点。`startContainer` 属性被设置为 `refNode.parentNode`，`startOffset` 属性被设置为 *`refNode`* 在其父节点 `childNodes` 集合中的索引加 1。
- `setEndBefore(refNode)`：把范围的终点设置到 *`refNode`* 之前，从而将 *`refNode`* 排除在选区之外、让其上一个同胞节点成为选区的最后一个子节点。`endContainer` 属性被设置为 `refNode.parentNode`，`endOffset` 属性被设置为 *`refNode`* 在其父节点 `childNodes` 集合中的索引。
- `setEndAfter(refNode)`：把范围的终点设置到 *`refNode`* 之后，从而让 *`refNode`* 成为选区的最后一个子节点。`endContainer` 属性被设置为 `refNode.parentNode`，`endOffset` 属性被设置为 *`refNode`* 在其父节点 `childNodes` 集合中的索引加 1。

​		调用这些方法时，所有属性都会自动重新赋值。不过，为了实现复杂的选区，也可以直接修改这些属性的值。

```html
<body>
<div id="box">
    <p>段落</p>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
    <div>块</div>
</div>
</body>
```

```js
let box = document.querySelector("#box"),
    li2 = box.children[1].children[1];

let range = document.createRange();
range.selectNode(li2);
console.log(range);
/*
Range {
	collapsed: false
	commonAncestorContainer: ul
	endContainer: ul
	endOffset: 4
	startContainer: ul
	startOffset: 3
	[[Prototype]]: Range
}
*/

// 将范围起点设置到box之前
range.setStartBefore(box);
console.log(range);
/*
Range {
	collapsed: false
	commonAncestorContainer: body
	endContainer: ul
	endOffset: 4
	startContainer: body
	startOffset: 1
	[[Prototype]]: Range
}
*/

// 将范围起点设置到box之后
range.setStartAfter(box);
console.log(range);
/*
Range {
	collapsed: true
	commonAncestorContainer: body
	endContainer: body
	endOffset: 2
	startContainer: body
	startOffset: 2
	[[Prototype]]: Range
}
*/
```



#### 复杂选择

​		要创建复杂的范围，需要使用 `setStart()` 和 `setEnd()` 方法。这两个方法都接收两个参数：参照节点和偏移量。对 `setStart()` 来说，参照节点会成为 `startContainer`，而偏移量会赋值给 `startOffset`。对 `setEnd()` 而言，参照节点会成为 `endContainer`，而偏移量会赋值给 `endOffset`。

​		使用这两个方法，可以模拟 `selectNode()` 和 `selectNodeContents()` 的行为。比如：

```html
<body> 
    <p id="p1"><b>Hello</b> world!</p> 
</body>
```

```js
let range1 = document.createRange(), 
 	range2 = document.createRange(), 
 	p1 = document.getElementById("p1"), 
 	p1Index = -1, 
 	i, 
 	len; 

for (i = 0, len = p1.parentNode.childNodes.length; i < len; i++) { 
 	if (p1.parentNode.childNodes[i] === p1) { 
 		p1Index = i; 
 		break; 
 	} 
}

range1.setStart(p1.parentNode, p1Index); 
range1.setEnd(p1.parentNode, p1Index + 1); 
range2.setStart(p1, 0); 
range2.setEnd(p1, p1.childNodes.length);

console.log(range1);
/*
Range {
	collapsed: false,
	commonAncestorContainer: body,
	endContainer: body,
	endOffset: 2,
	startContainer: body,
	startOffset: 1,
	[[Prototype]]: Range
}
*/

console.log(range2);
/*
Range {
	collapsed: false,
	commonAncestorContainer: p#p1,
	endContainer: p#p1,
	endOffset: 2,
	startContainer: p#p1,
	startOffset: 0,
	[[Prototype]]: Range
}
*/
```

​		注意，要选择节点（使用 `range1`），必须先确定给定节点（`p1`）在其父节点 `childNodes` 集合中的索引。而要选择节点的内容（使用 `range2`），则不需要这样计算，因为可以直接给 `setStart()` 和 `setEnd()` 传默认值。虽然可以模拟 `selectNode()` 和 `selectNodeContents()`，但 `setStart()` 和 `setEnd()` 真正的威力还是选择节点中的某个部分。

​		假设我们想通过范围从前面示例中选择从 `"Hello"` 中的 `"llo"` 到 `" world!"` 中的 `"o"` 的部分。很简单，第一步是取得所有相关节点的引用，如下面的代码所示：

```js
let p1 = document.getElementById("p1"), 
 	helloNode = p1.firstChild.firstChild, 
 	worldNode = p1.lastChild;
```

​		文本 `"Hello"` 其实是 `<p>` 的孙子节点，因为它是 `<b>` 的子节点。为此可以使用 `p1.firstChild` 取得 `<b>`，而使用 `p1.firstChild.firstChild` 取得 `"Hello"` 这个文本节点。文本节点 `" world!"` 是 `<p>` 的第二个（也是最后一个）子节点，因此可以使用 `p1.lastChild` 来取得它。然后，再创建范围，指定其边界，如下所示：

```js
let range = document.createRange(); 

range.setStart(helloNode, 2); 
range.setEnd(worldNode, 3);
```

​		因为选区起点在 `"Hello"` 中的字母 `"e"` 之后，所以要给 `setStart()` 传入 `helloNode` 和偏移量 2（`"e"` 后面的位置，`"H"` 的位置是 0）。要设置选区终点，则要给 `setEnd()` 传入 `worldNode` 和偏移量 3，即不属于选区的第一个字符的位置，也就是 `"r"` 的位置 3（位置 0 是一个空格）。下图展示了范围对应的选区。

​                                                         <img src="images/DOM2%E5%92%8CDOM3/image-20221129223207742.png" alt="image-20221129223207742" style="zoom:80%;" /> 

​		因为 `helloNode` 和 `worldNode` 是文本节点，所以它们会成为范围的 `startContainer` 和 `endContainer`，这样 `startOffset` 和 `endOffset` 实际上表示每个节点中文本字符的位置，而不是子节点的位置（传入元素节点时的情形）。而 `commonAncestorContainer` 是 `<p>` 元素，即包含这两个节点的第一个祖先节点。

```js
console.log(range);
/*
Range {
	collapsed: false,
	commonAncestorContainer: p#p1,
	endContainer: text,
	endOffset: 3,
	startContainer: text,
	startOffset: 2,
	[[Prototype]]: Range
}
*/
```

​		当然，只选择文档中的某个部分并不是特别有用，除非可以对选中部分执行操作。



#### 操作范围

​		创建范围之后，浏览器会在内部创建一个文档片段节点，用于包含范围选区中的节点。为操作范围的内容，选区中的内容必须格式完好。在前面的例子中，因为范围的起点和终点都在文本节点内部，并不是完好的 `DOM` 结构，所以无法在 `DOM` 中表示。不过，范围能够确定缺失的开始和结束标签，从而可以重构出有效的 `DOM` 结构，以便后续操作。

​		仍以前面例子中的范围来说，范围发现选区中缺少一个开始的 `<b>` 标签，于是会在后台动态补上这个标签，同时还需要补上封闭 `"He"` 的结束标签 `</b>`，结果会把 `DOM` 修改为这样：

```html
<p id="p1"><b>He</b><b>llo</b> world!</p>
```

​		而且，`" world!"` 文本节点会被拆分成两个文本节点，一个包含 `" wo"`，另一个包含 `"rld!"`。最终的 `DOM` 树，以及范围对应的文档片段如图所示。

​                                                           <img src="images/DOM2%E5%92%8CDOM3/image-20221130155520143.png" alt="image-20221130155520143" style="zoom:80%;" />

​		这样创建了范围之后，就可以使用很多方法来操作范围的内容（注意，范围对应文档片段中的所有节点，都是文档中相应节点的指针）。

​		第一个方法最容易理解和使用：`deleteContents()`。顾名思义，这个方法会从文档中删除范围包含的节点。下面是一个例子：

```js
let p1 = document.getElementById("p1"), 
 	helloNode = p1.firstChild.firstChild, 
 	worldNode = p1.lastChild, 
 	range = document.createRange(); 

range.setStart(helloNode, 2); 
range.setEnd(worldNode, 3); 

range.deleteContents();
```

​		执行上面的代码之后，页面中的 `HTML` 会变成这样：

```html
<p id="p1"><b>He</b>rld!</p>
```

​		因为前面介绍的范围选择过程通过修改底层 `DOM` 结构保证了结构完好，所以即使删除范围之后，剩下的 `DOM` 结构照样是完好的。

​		另一个方法 `extractContents()` 跟 `deleteContents()` 类似，也会从文档中移除范围选区。但不同的是，`deleteContents()` 方法返回 `undefined`，而 `extractContents()` 方法返回范围对应的文档片段 。这样，就可以把范围选中的内容插入文档中其他地方。

```js
let p1 = document.getElementById("p1"), 
 	helloNode = p1.firstChild.firstChild, 
 	worldNode = p1.lastChild, 
 	range = document.createRange();

range.setStart(helloNode, 2); 
range.setEnd(worldNode, 3); 

let fragment = range.extractContents(); 
console.log(fragment); // ▶#document-fragment
/*
▼#document-fragment
	<b>llo</b>
	" wo"
*/

p1.parentNode.appendChild(fragment);
```

​		这个例子提取了范围的文档片段，然后把它添加到文档 `<body>` 元素的最后（别忘了，在把文档片段传给 `appendChild()` 时，只会添加片段的子树，不包含片段自身。当片段中的子树被添加到别处之后，该片段就会为空，类似节点仓库）。结果就会得到如下 `HTML`：

```html
<p id="p1"><b>He</b>rld!</p> 
<b>llo</b> wo
```

​		如果不想把范围从文档中移除，也可以使用 `cloneContents()` 创建一个副本，然后把这个副本插入到文档其他地方。比如：

```js
let p1 = document.getElementById("p1"), 
 	helloNode = p1.firstChild.firstChild, 
 	worldNode = p1.lastChild, 
 	range = document.createRange(); 

range.setStart(helloNode, 2); 
range.setEnd(worldNode, 3); 

let fragment = range.cloneContents(); 
p1.parentNode.appendChild(fragment);
```

​		这个方法跟 `extractContents()` 很相似，因为它们都返回文档片段。主要区别是 `cloneContents()` 返回的文档片段包含范围中节点的副本，而非实际的节点。执行上面操作之后，`HTML` 页面会变成这样：

```html
<p id="p1"><b>Hello</b> world!</p> 
<b>llo</b> wo
```

​		此时关键是要知道，为保持结构完好而拆分节点的操作，只有在调用前述方法时才会发生。在 `DOM` 被修改之前，原始 `HTML` 会一直保持不变。



#### 范围插入

​		上一节介绍了移除和复制范围的内容，本节来看一看怎么向范围中插入内容。使用 `insertNode()` 方法可以在范围选区的开始位置插入一个节点。例如，假设我们想在前面例子中的 `HTML` 中插入如下 `HTML`：

```html
<span style="color: red">Inserted text</span>
```

​		可以使用下列代码：

```js
let p1 = document.getElementById("p1"),
	helloNode = p1.firstChild.firstChild, 
 	worldNode = p1.lastChild, 
 	range = document.createRange(); 

range.setStart(helloNode, 2); 
range.setEnd(worldNode, 3); 

let span = document.createElement("span"); 
span.style.color = "red"; 
span.appendChild(document.createTextNode("Inserted text")); 

range.insertNode(span);
```

​		运行上面的代码会得到如下 `HTML` 代码：

```html
<p id="p1"><b>He<span style="color: red">Inserted text</span>llo</b> world</p>
```

​		注意，`<span>` 正好插入到 `"Hello"` 中的 `"llo"` 之前，也就是范围选区的前面。同时，也要注意原始的 `HTML` 并没有添加或删除 `<b>` 元素，因为这里并没有使用之前提到的方法。使用这个技术可以插入有用的信息，比如在外部链接旁边插入一个小图标。

​		除了可以向范围中插入节点，还可以使用 `surroundContents()` 方法插入包含范围的节点。这个方法接收一个参数，即用于接收范围内容的容器节点。调用这个方法时，后台会执行如下操作：

1. 提取出范围的内容（提取内容）；
2. 在原始文档中范围原来所在的位置插入给定的节点（插入容器）；
3. 将范围对应文档片段的内容添加到给定节点（填充容器）。

​		这种功能适合在网页中高亮显示某些关键词，比如：

```js
let p1 = document.getElementById("p1"), 
 	helloNode = p1.firstChild.firstChild,
    range = document.createRange(); 

range.selectNode(helloNode); // 选择"Hello"文本节点

let span = document.createElement("span"); 
span.style.backgroundColor = "yellow"; 

range.surroundContents(span); // 给选区的内容围上一个指定的容器。
```

​		执行以上代码会以黄色背景高亮显示范围选择的文本。得到的 `HTML` 如下所示：

```html
<p id="p1"><b><span style="background-color: yellow">Hello</span></b> world!</p>
```

​		为了插入 `<span>` 元素，范围中必须包含完整的 `DOM` 结构。如果范围中包含部分选择的非文本节点（即：范围的内容未闭合），这个操作会失败并报错。另外，如果给定的节点是 `Document`、`DocumentType` 或 `DocumentFragment` 类型，也会导致抛出错误。

```js
// 范围的内容（如：<b>H）未闭合。
range.selectNodeContents(p1);
range.setEnd(helloNode, 1);

let span = document.createElement("span");
span.style.backgroundColor = "yellow";

range.surroundContents(span); // Uncaught DOMException: Failed to execute 'surroundContents' on 'Range': The Range has partially selected a non-Text node.
```



#### 范围折叠

​		如果范围并没有选择文档的任何部分，则称为折叠或坍塌（`collapsed`）。折叠范围有点类似文本框：如果文本框中有文本，那么可以用鼠标选中以高亮显示全部文本。这时候，如果再单击鼠标，则选区会被移除，光标会落在某两个字符中间。而在折叠范围时，位置会被设置为范围与文档交界的地方，可能是范围选区的开始处，也可能是结尾处。下图展示了范围折叠时会发生什么。

​		                                                                   <img src="images/DOM2%E5%92%8CDOM3/image-20221130171550398.png" alt="image-20221130171550398" style="zoom:80%;" />

​		折叠范围可以使用 `collapse()` 方法，这个方法接收一个参数：布尔值，表示折叠到范围哪一端。`true` 表示折叠到起点，`false` 表示折叠到终点。要确定一个范围是否已经被折叠，可以检测范围的 `collapsed` 属性（坍缩后变为 `true`）：

```js
range.collapse(true); 			// 折叠到起点 ==> 传入true表示坍缩到起点，传入false表示坍缩到终点。
console.log(range.collapsed); 	// 输出 true ==> 值为true表示范围已坍缩，值为false表示范围未坍缩。
```

​		测试范围是否被折叠，能够帮助确定范围中的两个节点是否相邻。例如有以下 `HTML` 代码：

```html
<p id="p1">Paragraph 1</p><p id="p2">Paragraph 2</p>
```

​		如果事先并不知道标记的结构（比如自动生成的标记），则可以像下面这样创建一个范围：

```js
let p1 = document.getElementById("p1"), 
 	p2 = document.getElementById("p2"), 
 	range = document.createRange(); 

// 将范围设置到p1与p2之间，若两元素之间没有任何节点，则范围会自动坍缩。
range.setStartAfter(p1);
range.setEndBefore(p2);

console.log(range.collapsed); // true
```

​		在这种情况下，创建的范围是折叠的，因为 `p1` 后面和 `p2` 前面没有任何内容。



#### 范围比较

​		如果有多个范围，则可以使用 `compareBoundaryPoints()` 方法确定范围之间是否存在公共的边界（起点或终点）。这个方法接收两个参数：要比较的范围和一个常量值（表示比较的方式）。这个常量参数包括：

- `Range.START_TO_START`（0）：比较两个范围的起点；
- `Range.START_TO_END`（1）：比较第一个范围的起点和第二个范围的终点；
- `Range.END_TO_END`（2）：比较两个范围的终点；
- `Range.END_TO_START`（3）：比较第一个范围的终点和第二个范围的起点。

​		`compareBoundaryPoints()` 方法在第一个范围的边界点位于第二个范围的边界点之前时返回 -1，在两个范围的边界点相等时返回 0，在第一个范围的边界点位于第二个范围的边界点之后时返回 1。来看下面的例子：

```js
let range1 = document.createRange(); 
let range2 = document.createRange(); 
let p1 = document.getElementById("p1"); 

range1.selectNodeContents(p1); 
range2.selectNodeContents(p1); 
range2.setEndBefore(p1.lastChild); 

console.log(range1.compareBoundaryPoints(Range.START_TO_START, range2)); // 0 
console.log(range1.compareBoundaryPoints(Range.END_TO_END, range2)); 	 // 1
```

​		在这段代码中，两个范围的起点是相等的，因为它们都是 `selectNodeContents()` 默认返回的值。因此，比较二者起点的方法返回 0。不过，因为 `range2` 的终点被使用 `setEndBefore()` 修改了，所以导致 `range1` 的终点位于 `range2` 的终点之后（如下图），结果这个方法返回了 1。

​                                                           		<img src="images/DOM2%E5%92%8CDOM3/image-20221130172111481.png" alt="image-20221130172111481" style="zoom:80%;" />



#### 复制范围

​		调用范围的 `cloneRange()` 方法可以复制范围。这个方法会创建调用它的范围的副本：

```js
let newRange = range.cloneRange();
```

​		新范围包含与原始范围一样的属性和功能但相互独立，修改其边界点不会影响原始范围。



#### 剥离范围

​		在使用完范围之后，最好调用 `detach()` 方法把范围从创建它的文档中剥离。调用 `detach()` 之后，就可以放心解除对范围的引用，以便垃圾回收程序释放它所占用的内存。下面是一个例子：

```js
range.detach(); // 从文档中剥离范围
range = null; 	// 解除引用
```

​		这两步是最合理的结束使用范围的方式。剥离之后的范围就不能再使用了。



### 小结

​		`DOM2` 规范定义了一些模块，用来丰富 `DOM1` 的功能。`DOM2 Core` 在一些类型上增加了与 `XML` 命名空间有关的新方法。这些变化只有在使用 `XML` 或 `XHTML` 文档时才会用到，在 `HTML` 文档中则没有用处。`DOM2` 增加的与 `XML` 命名空间无关的方法涉及以编程方式创建 `Document` 和 `DocumentType` 类型的新实例。

​		`DOM2 Style` 模块定义了如何操作元素的样式信息。

- 每个元素都有一个关联的 `style` 对象，可用于确定和修改元素特定的样式。
- 要确定元素的计算样式，包括应用到元素身上的所有 `CSS` 规则，可以使用 `getComputedStyle()` 方法。
- 通过 `document.styleSheets` 集合可以访问文档上所有的样式表。

​		`DOM2 Traversal and Range` 模块定义了与 `DOM` 结构交互的不同方式。

- `NodeIterator` 和 `TreeWalker` 可以对 `DOM` 树执行深度优先的遍历。
- `NodeIterator` 接口很简单，每次只能向前和向后移动一步。`TreeWalker` 除了支持同样的行为，还支持在 `DOM` 结构的所有方向移动，包括父节点、同胞节点和子节点。
- 范围是选择 `DOM` 结构中特定部分并进行操作的一种方式。
- 通过范围的选区可以在保持文档结构完好的同时从文档中移除内容，也可复制文档中相应的部分。


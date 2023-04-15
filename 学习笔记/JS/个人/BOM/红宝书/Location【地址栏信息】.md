# Location

​		`location` 是最有用的 `BOM` 对象之一，提供了当前窗口中加载文档的信息，以及通常的导航功能。这个对象独特的地方在于，它既是 `window` 的属性，也是 `document` 的属性。也就是说，`window.location` 和 `document.location` 指向同一个对象。`location` 对象不仅保存着当前加载文档的信息，也保存着把 `URL` 解析为离散片段后能够通过属性访问的信息。这些解析后的属性在下表中有详细说明（`location` 前缀是必需的）。

​		假设浏览器当前加载的 `URL` 是 `http://foouser:barpassword@www.wrox.com:80/WileyCDA/?q=javascript#contents`，`location` 对象的内容如下表所示。

|        属性         |                             值                             |                             说明                             |
| :-----------------: | :--------------------------------------------------------: | :----------------------------------------------------------: |
|   `location.hash`   |                       `"#contents"`                        |  `URL` 散列值（井号后跟零或多个字符），如果没有则为空字符串  |
|   `location.host`   |                    `"www.wrox.com:80"`                     |                       服务器名及端口号                       |
| `location.hostname` |                      `"www.wrox.com"`                      |                      服务器名（主机名）                      |
|   `location.href`   | `"http://www.wrox.com:80/WileyCDA/?q=javascript#contents"` | 当前加载页面的完整 `URL`。`location` 的 `toString()` 方法返回这个值 |
| `location.pathname` |                       `"/WileyCDA/"`                       |                 `URL` 中的路径和（或）文件名                 |
|   `location.port`   |                           `"80"`                           |      请求的端口号。如果 `URL`中没有端口，则返回空字符串      |
| `location.protocol` |                         `"http:"`                          |         页面使用的协议。通常是`"http:"`或`"https:"`          |
|  `location.search`  |                     `"?q=javascript"`                      |           `URL` 的查询字符串。这个字符串以问号开头           |
| `location.username` |                        `"foouser"`                         |                      域名前指定的用户名                      |
| `location.password` |                      `"barpassword"`                       |                       域名前指定的密码                       |
|  `location.origin`  |                  `"http://www.wrox.com"`                   |                     `URL` 的源地址。只读                     |

```js
new URL('http://foouser:barpassword@www.wrox.com:8080/WileyCDA/?q=javascript#contents');

/*
URL {
    hash: "#contents",
	host: "www.wrox.com:8080",
	hostname: "www.wrox.com",
	href: "http://foouser:barpassword@www.wrox.com:8080/WileyCDA/?q=javascript#contents",
	origin: "http://www.wrox.com:8080",
	password: "barpassword",
	pathname: "/WileyCDA/",
	port: "8080",
	protocol: "http:",
	search: "?q=javascript",
	searchParams: URLSearchParams {},
	username: "foouser",
    [[Prototype]]: URL
}
*/
```



### 查询字符串

​		`location` 的多数信息都可以通过上面的属性获取。但是 `URL` 中的查询字符串并不容易使用。虽然 `location.search` 返回了从问号开始直到 `URL` 末尾的所有内容，但没有办法逐个访问每个查询参数。下面的函数解析了查询字符串，并返回一个以每个查询参数为属性的对象：

```js
let getQueryStringArgs = function() { 
 	// 提取字符串：取出问号之后的查询字符串
 	let qs = (location.search.length > 0 ? location.search.substring(1) : ""), 
 	// 保存数据的对象
 	args = {}; 
 	// 把每个参数添加到 args 对象
 	for (let item of qs.split("&").map(kv => kv.split("="))) { 
        // 取得每个参数的条目数组
 		let name = decodeURIComponent(item[0]), 
 			value = decodeURIComponent(item[1]);
 		if (name.length) { 
 			args[name] = value; 
 		} 
 	}
    return args; 
}
```

​		这个函数首先删除了查询字符串开头的问号，当然前提是 `location.search` 必须有内容。解析后的参数将被保存到 `args` 对象，这个对象以字面量形式创建。接着，先把查询字符串按照 `"&"` 分割成数组，每个元素的形式为 `"name=value"`。`for` 循环迭代这个数组，将每一个元素按照 `"="` 分割成数组，这个数组第一项是参数名，第二项是参数值。参数名和参数值在使用 `decodeURIComponent()` 解码后（这是因为查询字符串通常是被编码后的格式）分别保存在 `name` 和 `value` 变量中。最后，`name` 作为属性而 `value` 作为该属性的值被添加到 `args` 对象。这个函数可以像下面这样使用：

```js
// 以 "https://www.baidu.com/s?ie=UTF-8&wd=%E6%A8%B1%E6%A1%83" 为例
getQueryStringArgs();
/*
{
	ie: "UTF-8"
	wd: "樱桃"
}
*/
```

​		现在，查询字符串中的每个参数都是返回对象的一个属性，这样使用起来就方便很多了。

​		`URLSearchParams` 提供了一组标准 `API` 方法，通过它们可以检查和修改查询字符串。给 `URLSearchParams` 构造函数传入一个查询字符串，就可以创建一个实例。这个实例上暴露了 `get()`、`set()` 和 `delete()` 等方法，可以对查询字符串执行相应操作。

```js
new URLSearchParams("?q=javascript&num=10"); // URLSearchParams {}
/*
URLSearchParams {
	[[Prototype]]: URLSearchParams {
		append: ƒ append(),
		delete: ƒ delete(),
		entries: ƒ entries(),
		forEach: ƒ forEach(),
		get: ƒ (),
		getAll: ƒ getAll(),
		has: ƒ has(),
		keys: ƒ keys(),
		set: ƒ (),
		sort: ƒ sort(),
		toString: ƒ toString(),
		values: ƒ values(),
		constructor: ƒ URLSearchParams(),
		Symbol(Symbol.iterator): ƒ entries(),
		Symbol(Symbol.toStringTag): "URLSearchParams",
		[[Prototype]]: Object
	}
}
*/
```

​		下面来看一个例子：

```js
let qs = "?q=javascript&num=10"; 
let searchParams = new URLSearchParams(qs); 

console.log(searchParams.toString()); // " q=javascript&num=10" 

searchParams.has("num"); // true 
searchParams.get("num"); // 10 
searchParams.set("page", "3"); 

console.log(searchParams.toString()); // " q=javascript&num=10&page=3" 

searchParams.delete("q"); 

console.log(searchParams.toString()); // " num=10&page=3"
```

​		大多数支持 `URLSearchParams` 的浏览器也支持将 `URLSearchParams` 的实例用作可迭代对象：

```js
let qs = "?q=javascript&num=10"; 
let searchParams = new URLSearchParams(qs); 
for (let param of searchParams) { 
 	console.log(param); 
} 
/*
> ["q", "javascript"] 
> ["num", "10"]
*/
```



### 操作地址

​		可以通过修改 `location` 对象修改浏览器的地址。首先，最常见的是使用 `assign()` 方法并传入一个 `URL`，如下所示：

```js
location.assign("http://www.baidu.com"); // 默认在当前窗口中，打开URL所指向的页面
```

​		这行代码会立即启动导航到新 `URL` 的操作，同时在浏览器历史记录中增加一条记录。如果给 `location.href` 或`window.location` 设置一个 `URL`，也会以同一个 `URL` 值调用 `assign()` 方法。比如，下面两行代码都会执行与显式调用 `assign()` 一样的操作：

```js
// 通过直接修改以下属性，也可以像调用assign一样。
window.location = "http://www.baidu.com"; 

location.href = "http://www.baidu.com";
```

​		在这 3 种修改浏览器地址的方法中，设置 `location.href` 是最常见的。

​		修改 `location` 对象的属性也会修改当前加载的页面。其中，`hash`、`search`、`hostname`、`pathname` 和 `port` 属性被设置为新值之后都会修改当前 `URL`，如下面的例子所示：

```js
// 假设当前URL为 https://baike.baidu.com/item/%E6%A8%B1%E6%A1%83/338?fr=kg_general

// 修改URL的hash片段
location.hash = "#section1"; 
location.toString(); // 'https://baike.baidu.com/item/%E6%A8%B1%E6%A1%83/338?fr=kg_general#section1'

// 修改URL的参数片段
location.search = "?q=javascript"; 
location.toString(); // 'https://baike.baidu.com/item/%E6%A8%B1%E6%A1%83/338?q=javascript#section1'

// 修改URL的主机域名
location.hostname = "www.taobao.com";
location.toString(); // 'https://www.taobao.com/item/%E6%A8%B1%E6%A1%83/338?q=javascript#section1'

// 修改URL的路由片段
location.pathname = "mydir"; 
location.toString(); // 'https://www.taobao.com/mydir?q=javascript#section1'

// 修改URL的端口号
location.port = 8080;
location.toString(); // 'https://www.taobao.com:8080/mydir?q=javascript#section1'
```

​		除了 `hash` 之外，只要修改 `location` 的一个属性，就会导致页面重新加载新 `URL`。

注意：修改 `hash` 的值会在浏览器历史中增加一条新记录。在早期的 `IE` 中，点击 “后退” 和 “前进” 按钮不会更新 `hash` 属性，只有点击包含散列的 `URL` 才会更新 `hash` 的值。

​		在以前面提到的方式修改 `URL` 之后，浏览器历史记录中就会增加相应的记录。当用户单击 “后退” 按钮时，就会导航到前一个页面。如果不希望增加历史记录，可以使用 `replace()` 方法。这个方法接收一个 `URL` 参数，但**重新加载后不会增加历史记录**。调用 `replace()` 之后，用户**不能回到前一页**。比如下面的例子：

```html
<!DOCTYPE html> 
<html> 
	<head> 
 		<title>You won't be able to get back here</title> 
	</head> 
	<body> 
 		<p>Enjoy this page for a second, because you won't be coming back here.</p> 
 		<script> 
 			setTimeout(() => location.replace("http://www.baidu.com/"), 1000); 
 		</script> 
	</body> 
</html>
```

​		浏览器加载这个页面 1 秒之后会重定向到 `"www.baidu.com"`。此时，“后退” （左向）按钮是禁用状态，即不能返回上一个页面，除非手动输入完整的 `URL`。

​		最后一个修改地址的方法是 `reload()`，它能重新加载当前显示的页面。调用 `reload()` 而不传参数，页面会以最有效的方式重新加载。也就是说，如果页面自上次请求以来没有修改过，浏览器可能会从缓存中加载页面。如果想强制从服务器重新加载，可以像下面这样给 `reload()` 传个 `true`：

```js
location.reload(); // 重新加载，可能是从缓存加载

location.reload(true); // 重新加载，从服务器加载
```

​		脚本中位于 `reload()` 调用之后的代码可能执行也可能不执行，这取决于网络延迟和系统资源等因素。为此，最好把 `reload()` 作为最后一行代码。


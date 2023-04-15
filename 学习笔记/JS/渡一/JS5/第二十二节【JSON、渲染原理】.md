### `JSON`

首先，`JSON`也是对象，它是前后端用来传输数据的标准格式。最早的格式是使用`XML`语言编写的文档。

`XML`格式：

```xml
// XML语言可以自定义标签，用来标识传输的数据。曾风靡一时，导致现在数据库的基础格式仍是xml格式。
<student>
	<name>邓</name>
    <age>20</age>
</student>
// 但这种格式与对象极其类似，因此后来被对象形式的JSON格式替代了。
```

`JSON`格式：

```json
// 为了与对象区别开，JSON格式要求，属性名必须是字符串格式。这实际上是对象的另一种表示形式，只是表面上的区分。
{
    'name' : '邓',
    'age' : 20
}
// 对象的属性名，可以是变量，也可以是字符串。本质是字符串。
```

但在实际传输中，传输的是二进制文本格式的数据。因此，`JSON`数据在传输时要先转换为字符串形式，`JSON`上提供了转换方法。

##### 转换方法

`JSON`是一个构造函数 / 类，在它的原型上定义了两个方法：`parse`和`stringify`。

##### `stringify`方法

`JSON.stringify()`方法，用来将对象形式的`JSON`数据转换为字符串形式【发送时使用】

##### `parse`方法

`JSON.parse()`方法，用来将字符串形式的`JSON`数据转换为对象形式【接收时使用】

### 渲染原理

渲染引擎先根据`DOM`结构绘制`dom`树，绘制时遵循”深度优先“原则，即：先绘制完一条枝干，再绘制下一条枝干。

##### DOM解析

`DOM`树的解析是根据节点来解析绘制的，其数据内容是异步加载的。

因此，`DOM`解析一定先于`DOM`加载之前完成。`DOM`树绘制完成，不代表页面内容加载完毕。

##### 渲染树

`DOM`树绘制完成后，会接着绘制`CSS`树，最终将`DOM`树与`CSS`树结合为render树，渲染引擎根据`render`树来绘制页面。

而如果通过`JS`对`DOM`结构进行修改，导致`DOM`树重新构建，进而导致`render`树也要重新构建，极其浪费性能。

##### `DOM`优化

`DOM`优化的重点就是要避免重绘和重排。重绘指重新绘制样式，重排指重新排版结构。

触发重排`reflow`：【增删改查`DOM`节点】

- `DOM`节点的删除，添加。
- `DOM`节点的宽高变化，字体大小，位置变化，如：`display ：none --> block`
- 访问节点的实时信息，实时位置，如：`offsetWidth`，`offsetHetght`【为了获取实时信息，重新构建页面】

触发重绘`repaint`：【修改节点的`CSS`样式】

- 只是修改局部节点的`CSS`样式，并不会导致页面的整体重绘，而是针对局部重绘样式。
- 修改字体，背景的颜色，修改背景图片等。
- 但修改字体大小，宽高等影响页面布局的样式，会触发重排。

总结：但凡修改涉及到页面布局变化的，都会导致重排。不涉及布局变化的，触发重绘。

### 异步加载

`JS`的下载过程与执行过程不能与`HTML`和`CSS`并行执行，`JS`默认同步加载，它会阻断时间线。

`JS`同步加载的缺点：

- 加载辅助工具 / `JS`包等没必要阻塞文档，过多的`JS`加载会影响页面渲染效率。
- 一旦网速不好，整个网络将等待`JS`加载而不进行后续渲染等工作，导致页面空白。
- 因此，需要将工具方法按需加载，用到再加载，不用不加载。

为此，`JavaScript`提供了几种异步加载`JS`文件的方式：`defer`，`async`，`onload`，创建`script`节点

##### 延迟执行`defer`

在`script`标签中写入`defer`属性，表示将该文件延迟到`DOM`解析完毕后再执行，其加载方式为异步的。

`defer`原是`IE`浏览器独有的属性方法，它可以用作外部`JS`文件，也可以用作页面级`script`代码。

##### 异步执行`async`

`async`是`W3C`的标准方法，表示异步加载该文件。但是加载完毕就立即执行，且只能用作外部文件。

注意：设置`async`或者`defer`属性的文件，其执行方式也是异步的，不会阻塞页面。

##### 创建script节点

在触发特定事件时，创建一个`script`标签，并插入到`DOM`中，加载完毕后`callback`

```html
<script>
	let script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = 'demo.js'; // 灯塔模式
    // 设置好src后，程序会立即异步加载该文件
    // 文件加载完成后，必须添加到页面中，否则无法执行。
    document.head.appendChild(script);
    // 异步加载导致一个问题，文件在加载过程中，不能使用其中方法。因此，必须知道文件何时加载完成。
    script.onload = function () { // 非IE浏览器，使用load方法
        // load：等待该script文件加载完成后，才开始执行里面提供的方法。
        test();
    }
    // IE浏览器为script设计了独有的readyState属性：监听文件加载的状态，动态设置状态码的值。
    // readyState：loading（加载中 / 初始值），loaded / complete（加载完毕）
    // 为此，IE还设置了监听状态码改变的事件：onreadystatechange【状态码改变一次，就触发一次】
    if (script.readyState) { // 兼容处理
        script.onreadystatechange = function () { 
        	if (script.readyState == 'complete' || script.readState == 'loaded') {
            	test();
        	}
    	}
    } else {
        script.onload = function () {
            test();
        }
    }
</script>
```

封装方法：

```js
// 封装方法
function loadScript (url, callback) {
    let script = document.createElement('script');
    script.type = 'text/javascript';
    if (script.readyState) {
        script.onreadystatechange = function () {
            if (script.readyState == 'complete' || script.readyState == 'loaded') {
                callback();
            }
        }
    } else {
        script.onload = function () {
            callback();
        }
    }
    script.src = url; // 为避免加载速度过快，导致浏览器监听不到状态码变化而无法调用方法。先绑定监听，再加载文件。
    document.head.appendChild(script);
}
// 使用方法：使用时要注意的是，异步加载，文件加载过程中，还不能使用里面的方法。推荐使用以下两种方法解决：
// 第一种：传入参数时，使用一个匿名函数引用，让方法在其内部执行。保证一定能在文件加载完成后，再使用方法。
loadScript('./demo.js', function () {
    test();
})
// 第二种：传入字符串，在待加载文件内部，使用一个对象，绑定所有的方法。通过调用对象方法的形式执行。
// demo.js中定义
let tools = {
    test : function () {}
}
// 调用方法
function loadScript (url, callback) {
    let script = document.createElement('script');
    script.type = 'text/javascript';
    if (readyState) {
        script.onreadystatechange = function () {
            if (script.readyState == 'complete' || script.readyState == 'loaded') {
                tools[callback]();
            }
        }
    } else {
        script.onload = function () {
             tools[callback]();
        }
    }
    script.src = url; // 为避免加载速度过快，导致浏览器监听不到状态码变化而无法调用方法。先绑定监听，再加载文件。
    document.head.appendChild(script);
}
loadScript('./demo.js', 'test');
```

### 时间线

`JS`时间线指，`JS`在浏览器中从诞生到执行的一系列事件顺序。

1. 创建`Document`对象，开始解析`web`页面。在解析`HTML`元素和他们的文本内容后，添加`Element`对象和`Text`节点到文档中。这个阶段`document.readyState = "loading"`。
2. 遇到`link`外部`css`，创建线程加载，并继续解析文档。
3. 遇到`script`外部`js`，并且没有设置`async`、`defer`的，浏览器加载并阻塞。等待这些同步脚本加载并执行完后，继续解析文档。
4. 遇到`script`外部`js`，并且设置有`async`、`defer`的，浏览器创建线程加载，并继续解析文档。
   - 异步脚本禁止使用`document.write()`方法，它会清空之前的所有文件，仅显示执行结果。
5. 遇到`img`等，先正常解析`dom`结构，然后浏览器异步加载`src`，并继续解析文档。
6. 当文档解析完成时，`document.readyState = "interactive // 活跃状态"`
7. 文档解析完成后，所有设置有`defer`属性的脚本会按照下载后的文件顺序执行。
8. `document`对象触发`DOMContentLoaded`事件，标志着程序执行从同步脚本执行阶段，转入事件驱动阶段。
9. 当所有`async`的脚本加载完成并执行后、`img`等所有资源都加载完成后，`document.readyState = "complete"`，`window`对象触发`load`事件。
10. 从此，以异步响应方式处理用户输入、网络事件等。

案例：打印状态码

```html
<script> // script也是DOM元素节点，在其内部状态码仍是loading。当最后一个script节点解析完后，才转为interactive。
	console.log(document.readyState); // loading
    document.onreadystatechange = function () { // 通过监听状态码变化，执行打印。
        console.log(document.readyState); // interactive -- complete
    }
    document.onload = function () { // 文档解析并加载完成后，状态码已改变。
        console.log(ducoment.readyState); // complete
    }
</script>
```

##### `DOMContentLoaded`事件

`DOMContentLoaded`事件，发生在`interactive`与`complete`之间，`defer`脚本之后，只能通过`addEventListener`监听事件来绑定。

注意：

- 一般，我们希望`JS`主程序最好是在`DOM`树构建完成后就开始执行，不必等待页面所有资源加载完成。
- 此时，就可以使用`DOMContentLoaded`事件来绑定页面的主程序，并且可以将`<script>`放到`<head>`中。
- `DOMContentLoaded`事件与`load`事件的区别：`DOMContentLoaded`发生在页面解析完成时，`load`发生在页面加载完成后。

```html
<head>
    <script>
    	document.addEventListener('DOMContentLoaded', function () {
            // 此处执行页面主程序，比使用load事件效率更高。
        }, false);
    </script>
</head>
```

当然，最快最标准的方式，还是将`JS`代码放到文档的最后面。当解析到`script`元素时，开始执行代码。


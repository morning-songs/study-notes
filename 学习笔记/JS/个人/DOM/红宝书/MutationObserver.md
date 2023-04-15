# MutationObserver 接口

​		不久前添加到 `DOM` 规范中的 `MutationObserver` 接口，可以**在 `DOM` 被修改时异步执行回调**。使用 `MutationObserver` 可以观察整个文档、`DOM` 树的一部分，或某个元素。此外还可以观察元素属性、子节点、文本，或者前三者任意组合的变化。

注意：新引进 `MutationObserver` 接口是为了取代废弃的 `MutationEvent`。【`mutation observer`：变化观察者】



### 基本用法

​		`MutationObserver` 的实例要通过调用 `MutationObserver` 构造函数并传入一个回调函数来创建：

```js
let observer = new MutationObserver(() => console.log('DOM was mutated!'));

observer; // MutationObserver {}
/*
MutationObserver {
	[[Prototype]]: MutationObserver {
		disconnect: ƒ disconnect(),
		observe: ƒ observe(),
		takeRecords: ƒ takeRecords(),
		constructor: ƒ MutationObserver(),
		Symbol(Symbol.toStringTag): "MutationObserver",
		[[Prototype]]: Object
}
*/
```



##### `observe` 方法

​		新创建的 `MutationObserver` 实例不会关联 `DOM` 的任何部分。要把这个 `observer` 与 `DOM` 关联起来，需要使用 `observe()` 方法。这个方法接收两个必需的参数：要观察其变化的 `DOM` 节点（被观察者），以及一个 `MutationObserverInit` 对象（观察方面）。

​		`MutationObserverInit` 对象用于控制观察哪些方面的变化，是一个键 / 值对形式配置选项的字典。例如，下面的代码会创建一个观察者（`observer`）并配置它观察 `<body>` 元素上的属性变化：

```js
let observer = new MutationObserver(() => console.log('<body> attributes changed')); 
// 观察body元素的属性变化
observer.observe(document.body, { attributes: true });
```

​		执行以上代码后，`<body>` 元素上任何属性发生变化都会被这个 `MutationObserver` 实例发现，然后就会异步执行注册的回调函数。`<body>` 元素后代的修改或其他非属性修改都不会触发回调**进入任务队列**。可以通过以下代码来验证：

```js
let observer = new MutationObserver(() => console.log('<body> attributes changed')); 

observer.observe(document.body, { attributes: true }); // 监听元素在HTML中的属性的变化

// 修改元素在HTML的中属性，才会触发。
document.body.className = 'foo'; 
console.log('Changed body class'); 
/*
> Changed body class 
> <body> attributes changed
*/
```

​		注意，回调中的 `console.log()` 是后执行的。这表明回调并非与实际的 `DOM` 变化同步执行。



##### 回调与 `MutationRecord`

​		每个回调都会收到一个包含 `MutationRecord` 实例的数组。`MutationRecord` 实例包含的信息包括：发生了什么变化，以及 `DOM` 的哪一部分受到了影响。因为回调执行之前可能同时发生多个满足观察条件的事件，所以每次执行回调都会传入一个包含按顺序入队的 `MutationRecord` 实例的数组。

​		下面展示了反映一个属性变化的 `MutationRecord` 实例的数组：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords));

observer.observe(document.body, { attributes: true }); 

document.body.setAttribute('foo', 'bar'); 

// [MutationRecord]
/* 
[
	0: MutationRecord {
		addedNodes: NodeList [],
		attributeName: "foo",
		attributeNamespace: null,
		nextSibling: null,
		oldValue: null,
		previousSibling: null,
		removedNodes: NodeList [],
		target: body,
		type: "attributes",
		[[Prototype]]: MutationRecord
    },
	length: 1,
	[[Prototype]]: Array(0) 
]
*/
```

​		下面是一次涉及命名空间的类似变化：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

observer.observe(document.body, { attributes: true }); 

document.body.setAttributeNS('baz', 'foo', 'bar');

// [MutationRecord]
/*
[
	0: MutationRecord {
		addedNodes: NodeList [],
		attributeName: "foo",
		attributeNamespace: "baz",
		nextSibling: null,
		oldValue: null,
		previousSibling: null,
		removedNodes: NodeList [],
		target: body,
		type: "attributes",
		[[Prototype]]: MutationRecord
	},
	length: 1,
	[[Prototype]]: Array(0)
]
*/
```

​		连续修改会生成多个 `MutationRecord` 实例，下次回调执行时就会收到包含所有这些实例的数组，顺序为变化事件发生的顺序：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

observer.observe(document.body, { attributes: true }); 

document.body.className = 'foo'; 
document.body.className = 'bar'; 
document.body.className = 'baz'; 

// [MutationRecord, MutationRecord, MutationRecord]
```

​		下表列出了 `MutationRecord` 实例的属性。

|         属性         |                             说明                             |
| :------------------: | :----------------------------------------------------------: |
|       `target`       |           被修改影响的目标节点，即：当前的被观察者           |
|        `type`        | 字符串，表示变化的类型：`"attributes"`、`"characterData"` 或 `"childList"` |
|      `oldValue`      | 如果在 `MutationObserverInit` 对象中启用（`attributeOldValue` 或 `characterDataOldValue` 为 `true`），相应的 `"attributes"` 或 `"characterData"` 的变化事件会设置这个属性为被替代的值， `"childList"` 类型的变化始终将这个属性设置为 `null` |
|   `attributeName`    | 对于 `"attributes"` 类型的变化，这里保存被修改属性的名字，其他变化事件会将这个属性设置为 `null` |
| `attributeNamespace` | 对于使用了命名空间的 `"attributes"` 类型的变化，这里保存被修改属性的名字，其他变化事件会将这个属性设置为 `null` |
|     `addedNodes`     | 对于 `"childList"` 类型的变化，返回包含变化中添加节点的 `NodeList` ，默认为空 `NodeList` |
|    `removedNodes`    | 对于 `"childList"` 类型的变化，返回包含变化中删除节点的 `NodeList` ，默认为空 `NodeList` |
|  `previousSibling`   | 对于 `"childList"` 类型的变化，返回变化节点的前一个同胞 `Node` ，默认为 `null` |
|    `nextSibling`     | 对于 `"childList"` 类型的变化，返回变化节点的后一个同胞 `Node`，默认为 `null` |

```html
<div id="box" class="foo"></div>

<script>
	let box = document.getElementById("box");
    
    let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords));
    
    observer.observe(box, {attributeOldValue: true, attributes: true});
    
    box.className = "bar";
    
    // [MutationRecord]
    /* [
    		0: MutationRecord {
    			addedNodes: NodeList [],
				attributeName: "class",
				attributeNamespace: null,
				nextSibling: null,
				oldValue: "foo",
				previousSibling: null,
				removedNodes: NodeList [],
				target: div#box.bar,
				type: "attributes",
				[[Prototype]]: MutationRecord
    		},
    		1: MutationRecord {
    			addedNodes: NodeList [text],
				attributeName: null,
				attributeNamespace: null,
				nextSibling: null,
				oldValue: null,
				previousSibling: null,
				removedNodes: NodeList [],
				target: div#box.bar,
				type: "childList",
				[[Prototype]]: MutationRecord
    		},
    		2: MutationRecord {
    			addedNodes: NodeList [],
				attributeName: null,
				attributeNamespace: null,
				nextSibling: null,
				oldValue: null,
				previousSibling: null,
				removedNodes: NodeList [text],
				target: div#box.bar,
				type: "childList",
				[[Prototype]]: MutationRecord
    		},
			length: 3,
			[[Prototype]]: Array(0)
    ]*/
</script>
```

​		传给回调函数的第二个参数是观察变化的 `MutationObserver` 的实例（即：观察者本身），演示如下：

```js
let observer = new MutationObserver(
    (mutationRecords, mutationObserver) => console.log(mutationObserver === observer)); 

observer.observe(document.body, { attributes: true }); 

document.body.className = 'foo'; 

// true
```



##### `disconnect()` 方法

​		默认情况下，只要被观察的元素不被垃圾回收，`MutationObserver` 的回调就会响应 `DOM` 变化事件，从而被执行。要提前终止执行回调，可以调用 `disconnect()` 方法。下面的例子演示了同步调用 `disconnect()` 之后，不仅会停止此后变化事件的回调，也会抛弃已经加入任务队列要异步执行的回调：

```js
let observer = new MutationObserver(() => console.log('<body> attributes changed')); 

observer.observe(document.body, { attributes: true }); 
document.body.className = 'foo'; 

observer.disconnect(); 
document.body.className = 'bar'; 
//（没有日志输出）
```

​		要想让已经加入任务队列的回调执行，可以使用 `setTimeout()` 让已经入列的回调执行完毕再调用 `disconnect()`：

```js
let observer = new MutationObserver((records) => console.log(records)); 

observer.observe(document.body, { attributes: true });
document.body.className = 'foo'; 

setTimeout(() => { 
 	observer.disconnect(); 
 	document.body.className = 'bar'; 
}, 0); 
// [MutationRecord]
```



##### 复用 `MutationObserver`

​		多次调用 `observe()` 方法，可以复用一个 `MutationObserver` 对象观察多个不同的目标节点。此时，`MutationRecord` 的  `target` 属性可以标识发生变化事件的目标节点。下面的示例演示了这个过程：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords.map((x) => x.target))); 

// 向页面主体添加两个子节点
let childA = document.createElement('div'), 
 	childB = document.createElement('span'); 
document.body.appendChild(childA); 
document.body.appendChild(childB); 

// 观察两个子节点
observer.observe(childA, { attributes: true }); 
observer.observe(childB, { attributes: true }); 

// 修改两个子节点的属性
childA.setAttribute('name', 'div'); 
childB.setAttribute('name', 'span'); 
// [<div>, <span>]
```

​		`disconnect()` 方法是一个 “一刀切” 的方案，调用它会停止观察所有目标：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords.map((x) => x.target))); 

// 向页面主体添加两个子节点
let childA = document.createElement('div'), 
 	childB = document.createElement('span'); 
document.body.appendChild(childA); 
document.body.appendChild(childB); 

// 观察两个子节点
observer.observe(childA, { attributes: true }); 
observer.observe(childB, { attributes: true }); 

// 停止所有观察
observer.disconnect(); 

// 修改两个子节点的属性
childA.setAttribute('name', 'div'); 
childB.setAttribute('name', 'span'); 
// （没有日志输出）
```



##### 重用 `MutationObserver`

​		调用 `disconnect()` 并不会结束 `MutationObserver` 的生命。还可以重新使用这个观察者，再将它关联到新的目标节点。下面的示例在两个连续的异步块中先断开然后又恢复了观察者与 `<body>` 元素的关联：

```js
let observer = new MutationObserver(() => console.log('<body> attributes changed')); 
observer.observe(document.body, { attributes: true }); 

// 这行代码会触发变化事件
document.body.setAttribute('foo', 'bar'); 

setTimeout(() => { 
 	observer.disconnect(); // 断开连接
 	document.body.setAttribute('bar', 'baz'); // 这行代码不会触发变化事件
}, 0); 

setTimeout(() => { 
 	observer.observe(document.body, { attributes: true }); // 重新连接
 	document.body.setAttribute('baz', 'qux'); // 这行代码会触发变化事件
}, 0); 
// <body> attributes changed 
// <body> attributes changed
```



### 观察范围

​		`MutationObserverInit` 对象用于控制对目标节点的观察范围。粗略地讲，观察者可以观察的事件包括属性变化、文本变化和子节点变化。

​		下表列出了 `MutationObserverInit` 对象的属性。

|          属性           |                             说明                             |
| :---------------------: | :----------------------------------------------------------: |
|        `subtree`        | 布尔值，表示是否观察目标节点及其子树（后代），如果是 `false`，则只观察目标节点的变化；如果是 `true`，则观察目标节点及其整个子树，默认为 `false` |
|      `attributes`       |    布尔值，表示是否观察目标节点的属性变化，默认为 `false`    |
|    `attributeFilter`    | 字符串数组，表示要观察哪些属性的变化，把这个值设置为 `true` 也会将 `attributes` 的值转换为 `true`，默认为观察所有属性 |
|   `attributeOldValue`   | 布尔值，表示 `MutationRecord` 是否记录变化之前的属性值，把这个值设置为 `true` 也会将 `attributes` 的值转换为 `true`，默认为 `false` |
|     `characterData`     |   布尔值，表示修改字符数据是否触发变化事件，默认为 `false`   |
| `characterDataOldValue` | 布尔值，表示 `MutationRecord` 是否记录变化之前的字符数据，把这个值设置为 `true` 也会将 `characterData` 的值转换为 `true`，默认为 `false` |
|       `childList`       | 布尔值，表示修改目标节点的子节点是否触发变化事件，默认为 `false` |

注意：在调用 `observe()` 时，第二参数 `MutationObserverInit` 对象中的 `attribute`、`characterData` 和 `childList` 属性必须至少有一项为 `true`（无论是直接设置这几个属性，还是通过设置 `attributeOldValue` 等属性间接导致它们的值转换为 `true`）。否则会抛出错误，因为没有任何变化事件可能触发回调。



##### 属性节点

​		`MutationObserver` 可以观察节点属性的添加、移除和修改。要为属性变化注册回调，需要在 `MutationObserverInit` 对象中将 `attributes` 属性设置为 `true`，如下所示：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

observer.observe(document.body, { attributes: true }); 

// 添加属性 
document.body.setAttribute('foo', 'bar'); 
// 修改属性
document.body.setAttribute('foo', 'baz'); 
// 移除属性
document.body.removeAttribute('foo'); 

// [MutationRecord, MutationRecord, MutationRecord]
```

​		把 `attributes` 设置为 `true` 的默认行为是观察所有属性，但不会在 `MutationRecord` 对象中记录原来的属性值。如果想观察某个或某几个属性，可以使用 `attributeFilter` 属性来设置白名单，即一个属性名字符串数组：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords));

observer.observe(document.body, { attributeFilter: ['foo'] }); 

// 添加白名单属性
document.body.setAttribute('foo', 'bar'); 
// 添加被排除的属性
document.body.setAttribute('baz', 'qux');

// 只有 foo 属性的变化被记录了
// [MutationRecord]
```

​		如果想在变化记录中保存属性原来的值，可以将 `attributeOldValue` 属性设置为 `true`：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords.map(x => x.oldValue)));

observer.observe(document.body, { attributeOldValue: true }); 

document.body.setAttribute('foo', 'bar'); 
document.body.setAttribute('foo', 'baz'); 
document.body.setAttribute('foo', 'qux'); 

// 每次变化都保留了上一次的值
// [null, 'bar', 'baz']
```



##### 字符数据

​		`MutationObserver` 可以观察文本节点（如 `Text`、`Comment` 或 `ProcessingInstruction` 节点）中字符的添加、删除和修改。要为字符数据注册回调，需要在 `MutationObserverInit` 对象中将 `characterData` 属性设置为 `true`，如下所示：

注释：设置元素文本内容的标准方式是 `textContent` 属性。`Element` 类也定义了 `innerText` 属性，与 `textContent` 类似。但由于对  `innerText` 的定义尚不严谨，浏览器间的实现也存在兼容性问题，因此不建议再使用了。

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

// 创建要观察的文本节点
document.body.firstChild.textContent = 'foo'; 
observer.observe(document.body.firstChild, { characterData: true }); 

// 赋值为相同的字符串
document.body.firstChild.textContent = 'foo'; 
// 赋值为新字符串
document.body.firstChild.textContent = 'bar'; 
// 通过节点设置函数赋值
document.body.firstChild.textContent = "baz";

// 以上变化都被记录下来了
// [MutationRecord, MutationRecord, MutationRecord]
```

​		将 `characterData` 属性设置为 `true` 的默认行为不会在 `MutationRecord` 对象中记录原来的字符数据。如果想在变化记录中保存原来的字符数据，可以将 `characterDataOldValue` 属性设置为 `true`：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords.map(x => x.oldValue)));

// 创建要观察的文本节点
document.body.firstChild.textContent = 'foo';
observer.observe(document.body.firstChild, { characterDataOldValue: true });

// 赋值为相同的字符串
document.body.firstChild.textContent = 'foo';
// 赋值为新字符串
document.body.firstChild.textContent = 'bar';
// 通过节点设置函数赋值
document.body.firstChild.textContent = "baz";

// 每次变化都保留了上一次的值
// ["foo", "foo", "bar"]
```



##### 观察子节点

​		`MutationObserver` 可以观察目标节点子节点的添加和移除。要观察子节点，需要在 `MutationObserverInit` 对象中将 `childList` 属性设置为 `true`。

​		下面的例子演示了添加子节点：

```js
// 清空主体
document.body.innerHTML = ''; 

let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

observer.observe(document.body, { childList: true }); 

document.body.appendChild(document.createElement('div')); 

/*
[
	0: MutationRecord {
		addedNodes: NodeList [div],
		attributeName: null,
		attributeNamespace: null,
		nextSibling: null,
		oldValue: null,
		previousSibling: null,
		removedNodes: NodeList [],
		target: body,
		type: "childList",
		[[Prototype]]: MutationRecord
	},
	length: 1,
	[[Prototype]]: Array(0)
]
*/
```

​		下面的例子演示了移除子节点：

```html
<div id="div-box"></div>
```

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

observer.observe(document.body, { childList: true }); 

document.body.removeChild(document.getElementById('div-box')); 
/*
[
	0: MutationRecord {
		addedNodes: NodeList [],
		attributeName: null,
		attributeNamespace: null,
		nextSibling: null,
		oldValue: null,
		previousSibling: null,
		removedNodes: NodeList [div],
		target: body,
		type: "childList",
		[[Prototype]]: MutationRecord
	},
	length: 1,
	[[Prototype]]: Array(0)
]
*/
```

​		对子节点重新排序（尽管调用一个方法即可实现）会报告两次变化事件，因为从技术上会涉及先移除和再添加：

```js
// 清空主体
document.body.innerHTML = '';

let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

// 创建两个初始子节点
document.body.appendChild(document.createElement('div')); 
document.body.appendChild(document.createElement('span')); 

observer.observe(document.body, { childList: true }); 

// 交换子节点顺序
document.body.insertBefore(document.body.lastChild, document.body.firstChild); 

// 发生了两次变化：第一次是节点被移除，第二次是节点被添加
/*
[
	0: MutationRecord {
		addedNodes: NodeList [],
		attributeName: null,
		attributeNamespace: null,
		nextSibling: null,
		oldValue: null,
		previousSibling: div,
		removedNodes: NodeList [span],
		target: body,
		type: "childList",
		[[Prototype]]: MutationRecord
	},
	1: MutationRecord {
		addedNodes: NodeList [span],
		attributeName: null,
		attributeNamespace: null,
		nextSibling: text,
		oldValue: null,
		previousSibling: null,
		removedNodes: NodeList [],
		target: body,
		type: "childList",
		[[Prototype]]: MutationRecord
	},
	length: 2,
	[[Prototype]]: Array(0)
]
*/
```



##### 观察子树

​		默认情况下，`MutationObserver` 将观察的范围限定为一个元素及其子节点的变化。可以把观察的范围扩展到这个元素的子树（所有后代节点），这需要在 `MutationObserverInit` 对象中将 `subtree` 属性设置为 `true`。

​		下面的代码展示了观察元素及其后代节点属性的变化：

```js
// 清空主体
document.body.innerHTML = '';

let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

// 插入一个子元素节点
document.body.appendChild(document.createElement('div'));

// 观察<body>元素及其子树
observer.observe(document.body, { attributes: true, subtree: true }); 

// 修改<body>元素的子树
document.body.firstChild.setAttribute('foo', 'bar'); 

// 记录了子树变化的事件
/*
[
	0: MutationRecord {
		addedNodes: NodeList [],
		attributeName: "foo",
		attributeNamespace: null,
		nextSibling: null,
		oldValue: null,
		previousSibling: null,
		removedNodes: NodeList [],
		target: div,
		type: "attributes",
		[[Prototype]]: MutationRecord
	},
	length: 1,
	[[Prototype]]: Array(0)]
*/
```

​		有意思的是，被观察子树中的节点被移出子树之后仍然能够触发变化事件。这意味着在子树中的节点离开该子树后，即使严格来讲该节点已经脱离了原来的子树，但它仍然会触发变化事件。

​		下面的代码演示了这种情况：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

let subtreeRoot = document.createElement('div'), 
 	subtreeLeaf = document.createElement('span'); 

// 创建包含两层的子树
document.body.appendChild(subtreeRoot); 
subtreeRoot.appendChild(subtreeLeaf); 

// 观察子树
observer.observe(subtreeRoot, { attributes: true, subtree: true }); 

// 把节点转移到其他子树（并没有触发删除子节点的变化事件）
document.body.insertBefore(subtreeLeaf, subtreeRoot); 
subtreeLeaf.setAttribute('foo', 'bar'); 

// 移出的节点仍然触发变化事件
/* 
[
	0: MutationRecord {
		addedNodes: NodeList [],
		attributeName: "foo",
		attributeNamespace: null,
		nextSibling: null,
		oldValue: null,
		previousSibling: null,
		removedNodes: NodeList [],
		target: span,
		type: "attributes",
		[[Prototype]]: MutationRecord
	},
	length: 1,
	[[Prototype]]: Array(0)
]
*/
```



### 记录队列

​		`MutationObserver` 接口是**出于性能考虑而设计**的，其**核心是异步回调与记录队列模型**。为了在大量变化事件发生时不影响性能，每次变化的信息（由观察者实例决定）会保存在 `MutationRecord` 实例中，然后添加到记录队列。这个队列对每个 `MutationObserver` 实例都是唯一的，是所有 `DOM` 变化事件的有序列表。



##### 记录队列

​		每次 `MutationRecord` 被添加到 `MutationObserver` 的记录队列时，仅当之前没有已排期的微任务回调时（队列中微任务长度为 0），才会将观察者注册的回调（在初始化 `MutationObserver` 时传入）作为微任务调度到任务队列上。这样可以保证记录队列的内容不会被回调处理两次。

​		不过在回调的微任务异步执行期间，有可能又会发生更多变化事件。因此被调用的回调会接收到一个包含 `MutationRecord` 实例的数组，顺序为它们进入记录队列的顺序。回调要负责处理这个数组的每一个实例，因为函数退出之后这些实现就不存在了。回调执行后，这些 `MutationRecord` 就用不着了，因此记录队列会被清空，其内容会被丢弃。

注释：记录队列与这个包含 `MutationRecord` 实例的数组虽然相似，但并不是一个概念。另外，回调通常只在最后统一地执行一次。



##### `takeRecords()` 方法

​		调用 `MutationObserver` 实例的 `takeRecords()` 方法可以清空记录队列，取出并返回其中的所有 `MutationRecord` 实例。看这个例子：

```js
let observer = new MutationObserver((mutationRecords) => console.log(mutationRecords)); 

observer.observe(document.body, { attributes: true }); 

document.body.className = 'foo'; 
document.body.className = 'bar'; 
document.body.className = 'baz'; 

console.log(observer.takeRecords()); // (3) [MutationRecord, MutationRecord, MutationRecord]
console.log(observer.takeRecords()); // []
```

​		这在希望断开与观察目标的联系，但又希望处理由于调用 `disconnect()` 而被抛弃的记录队列中的 `MutationRecord` 实例时比较有用。



### 性能回收

​		`DOM Level 2` 规范中描述的 `MutationEvent` 定义了一组会在各种 `DOM` 变化时触发的事件。由于浏览器事件的实现机制，这个接口出现了严重的性能问题。因此，`DOM Level 3` 规定废弃了这些事件。`MutationObserver` 接口就是为替代这些事件而设计的更实用、性能更好的方案。

​		将变化回调委托给微任务来执行可以保证事件同步触发，同时避免随之而来的混乱。为 `MutationObserver` 而实现的记录队列，可以保证即使变化事件被爆发式地触发，也不会显著地拖慢浏览器。

​		无论如何，使用 `MutationObserver` 仍然不是没有代价的。因此理解什么时候避免出现这种情况就很重要了。



##### `MutationObserver` 的引用

​		`MutationObserver` 实例与目标节点之间的引用关系是非对称的。`MutationObserver` 拥有对要观察的目标节点的弱引用。因为是弱引用，所以不会妨碍垃圾回收程序回收目标节点。

​		然而，目标节点却拥有对 `MutationObserver` 的强引用。如果目标节点从 `DOM` 中被移除，随后被垃圾回收，则与之关联的  `MutationObserver` 也会被垃圾回收。



##### `MutationRecord` 的引用

​		记录队列中的每个 `MutationRecord` 实例至少包含对已有 `DOM` 节点的一个引用。如果变化是 `childList` 类型，则会包含多个节点的引用。记录队列和回调处理的默认行为是耗尽这个队列，处理每个 `MutationRecord`，然后让它们超出作用域并被垃圾回收。

​		有时候可能需要保存某个观察者的完整变化记录。保存这些 `MutationRecord` 实例，也就会保存它们引用的节点，因而会妨碍这些节点被回收。如果需要尽快地释放内存，建议从每个 `MutationRecord` 中抽取出最有用的信息，然后保存到一个新对象中，最后抛弃 `MutationRecord`。



### 小结

​		文档对象模型（`DOM`，`Document Object Model`）是语言中立的 `HTML` 和 `XML` 文档的 `API`。`DOM Level 1` 将 `HTML` 和 `XML` 文档定义为一个节点的多层级结构，并暴露出 `JavaScript` 接口以操作文档的底层结构和外观。

​		`DOM` 由一系列节点类型构成，主要包括以下几种。

- `Node` 是基准节点类型，是文档一个部分的抽象表示，所有其他类型都继承 `Node`。
- `Document` 类型表示整个文档，对应树形结构的根节点。在 `JavaScript` 中，`document` 对象是`Document` 的实例，拥有查询和获取节点的很多方法。
- `Element` 节点表示文档中所有 `HTML` 或 `XML` 元素，可以用来操作它们的内容和属性。
- 其他节点类型分别表示文本内容、注释、文档类型、`CDATA` 区块和文档片段。

​		`DOM` 编程在多数情况下没什么问题，在涉及 `<script>` 和 `<style>` 元素时会有一点兼容性问题。因为这些元素分别包含脚本和样式信息，所以浏览器会将它们与其他元素区别对待。

​		要理解 `DOM`，最关键的一点是知道影响其性能的问题所在。`DOM` 操作在 `JavaScript` 代码中是代价比较高的，`NodeList` 对象尤其需要注意。`NodeList` 对象是 “实时更新” 的，这意味着每次访问它都会执行一次新的查询。考虑到这些问题，实践中要尽量减少 `DOM` 操作的数量。

​		`MutationObserver` 是为代替性能不好的 `MutationEvent` 而问世的。使用它可以有效精准地监控`DOM` 变化，而且 `API` 也相对简单。


# `Hooks`

​		*`Hook`* 是 `React 16.8` 的新增特性。它可以让你在不编写 `class` 的情况下使用 `state` 以及其他的 `React` 特性。

​		`Hook` 主要解决了以下几个难以理解的问题：在组件之间复用状态的逻辑，复杂组件，`class`。更多参考：[使用 `Hook` 的原因](https://zh-hans.reactjs.org/docs/hooks-intro.html#motivation)。



### 基础

##### 基本概念

​		`Hook` 是什么？`Hook` 是一个特殊的函数，它可以让你 “钩入” `React` 的特性。例如，`useState` 是允许你在 `React` 函数组件中添加 `state` 的 `Hook`。

​		什么时候使用 `Hook`？如果你在编写函数组件并意识到需要向其添加一些 `state`，以前的做法是必须将其转化为 `class`。现在你可以在现有的函数组件中使用 `Hook`。【使用 `Hook` 可以使我们在函数组件中直接使用 `React` 的特性，而无需再将其转为 `class`】

##### 使用规则

`Hook` 就是 `JavaScript` 函数，但是使用它们会有两个额外的规则：

- 只能在 **函数最顶层** 调用 `Hook`。不要在循环、条件判断或者子函数中调用。
- 只能在 **`React` 的函数组件** 或自定义的 `Hook` 中调用 `Hook`。不要在其他 `JavaScript` 函数中调用。

​		同时，我们提供了 [`linter` 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks) 来自动执行这些规则。这些规则乍看起来会有一些限制和令人困惑，但是要让 `Hook` 正常工作，它们至关重要。

##### 渲染机制

​		函数组件每次更新（`props` 或 `state`）时，所对应的函数都会被重新创建和执行。但 `useState` 只会在组件初始化时执行一次。

##### 生命周期

​		函数组件没有生命周期（但可通过 `useEffect` 来部分模拟）。默认是当组件更新时，整个函数都会重新创建和执行，如前所述。



### `API`

​		`Hook API` 以 `use` 开头，内置在 `React` 对象中。使用时，可直接从 `React` 对象中导入。

```jsx
// 导入 React 对象：
import React from 'react';

// 以导入 useState 为例：
const {useState} = React;
```

​		更多参考：[`Hook API`](https://zh-hans.reactjs.org/docs/hooks-reference.html) 



#### `useState`

​		`useState` 用于在函数组件中设置响应式数据。它接收一个参数来定义初始数据，可以是一条任意数据或一个返回初始数据的函数。

```jsx
// 直接传入一条初始数据
let [state, setState] = useState('该数据的初始状态');

// 传入一个初始化函数（无参）
let [state, setState] = useState(() => {
    return '该数据的初始状态';
});
```

​		`useState` 返回此数据的当前状态 `state` 和重写数据的方法 `setState`。其中，`setState` 可接收一条新的数据或一个重写旧数据的回调。这个重写回调也可接收一个参数，即：数据的当前状态 `state`。

```jsx
setState('重写该数据的状态');

setState((state) => {
    return '重写该数据的状态';
});
```

​		如前所述，`useState()` 用于设置组件的初始状态。因此，它只会在组件初始化时执行一次，后续组件每次更新时都不会再触发。

​		如果你重写后的 `state` 与当前的 `state` 相同时，`React` 将跳过子组件的渲染并且不会触发 `effect` 的执行。因为，`React` 会使用 [`Object.is` 比较算法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 来比较重写前后的 `state`。



#### `useEffect`

​		由于函数组件的渲染机制，每次更新时都会重新执行函数。因此，不能直接在函数中发起请求并更新数据，否则会造成死循环。

```jsx
function Home() {
    let [data, setData] = useState();
    
    axios.get('https://jsonplaceholder.typicode.com/posts?userId=2&name=kaola&age=18')
    	 .then((res) => {
        	setData(res.data);
    	})
    	 .catch((err) => {
        	console.log(err);
    	});
    
    console.log('组件更新');
}
```

​		上面的请求和更新数据的方式会导致死循环。不过，如果请求数据只是初始数据的话，可以将其置入 `useState` 中进行请求。

```jsx
function Home() {
    let [data, setData] = useState(() => {
		axios.get('https://jsonplaceholder.typicode.com/posts?userId=2&name=kaola&age=18')
    	 	 .then((res) => {
        		setData(res.data);
    		})
    	 	 .catch((err) => {
        		console.log(err);
    		});
    });
    
    console.log('组件更新');
}
```

​		另一种更推荐的方式，是使用 `useEffect` 来处理这种副作用。传入一个 `effect` 回调，在其中进行请求即可。

```jsx
const {useEffect} = React;

function Home() {
    let [data, setData] = useState();
    
    useEffect(() => {
		axios.get('https://jsonplaceholder.typicode.com/posts?userId=2&name=kaola&age=18')
    	 	 .then((res) => {
        		setData(res.data);
    		})
    	 	 .catch((err) => {
        		console.log(err);
    		});
    });
    
    console.log('组件更新');
}
```

​		默认地，`useEffect` 在函数组件初始渲染和每次更新之后都会被执行。不过，可以通过第二个参数来控制 `useEffect` 的执行。

```jsx
// 控制useEffect的执行，避免造成死循环。
useEffect(() => {
    axios.get('https://jsonplaceholder.typicode.com/posts?userId=2&name=kaola&age=18')
        .then((res) => {
        setData(res.data);
    })
        .catch((err) => {
        console.log(err);
    });
}, []);
```

​		第二个参数，是一个控制是否调用 `effect` 回调（或者说，控制调用时机）的依赖项。具有以下使用规则：

- 省略：在首次渲染完成之后，以及每次重新渲染之后都调用 `effect` 回调。
- `[]`：只在首次渲染完成之后调用（没有指定任何依赖项）。
- `[dependency1, dependency2, ...]`：在首次渲染完成之后，以及任一依赖项更新后调用。

```jsx
const { useState, useEffect } = React;

export default function Home() {
    let [data, setData] = useState('');
    let [num, setNum] = useState(0);

    useEffect(() => {
        setData('更新');
        console.log('effect');
    }, [num]);

    console.log('组件更新');

    let change = () => {
        setNum(++num);
    }

    return (
        <div className="Home">
            <h1>Home组件</h1>
            <button onClick={change}>点击</button>
        </div>
    );
}
```

​		注意，如果使用定时器更新数据，则应将定时器设置在 `effect` 回调中。否则，每次数据更新触发组件更新时，都会添加一个新的定时器。这样，运行久了之后，势必会导致内存爆满。

```jsx
// 执行一些只在初次渲染执行的操作，如：定时器，操作DOM，发起请求等。
useEffect(() => {
    console.log('effect');
    setInterval(() => {});
}, []);
```

##### 副作用

​		副作用函数是相对于纯函数来说的。纯函数是指与外部没有任何交互行为的函数，副作用函数则是指与外部有交互行为的函数。

```js
let a = 0;

// 纯函数（不操作外部数据）
function pureFunction() {
    let a = 1;
    return a;
}

// 副作用函数（操作了外部数据）
function sideEffectFunction() {
    a += 1;
    return a;
}
```

​		而 `useEffect` 就是用来处理函数副作用的。`React` 中常见的副作用有：订阅，定时器更新数据，发起请求更新数据，操作 `DOM`。

​		有些副作用需要清除，有些则不需要。有时候，我们只想 **在 `React` 更新 `DOM` 之后运行一些额外的代码。**比如发送网络请求，手动变更 `DOM`，记录日志，这些都是常见的无需清除的操作。因为我们在执行完这些操作之后，就可以忽略他们了。然而，也有一些副作用是需要清除的。例如 **订阅外部数据源**，定时器。这种情况下，清除工作是非常重要的，可以防止引起内存泄漏！

​		`useEffect` 可以在组件渲染后实现各种不同的副作用。有些副作用可能需要清除，此时，需要返回一个清除函数：

```jsx
// 清除副作用
useEffect(() => {
	let timer = setInterval(() => {});
    
    return () => {
    	clearInterval(timer); // 清除定时器    
    };
}, []);
```

​		其他的 `effect` 可能不必清除，所以不需要返回。

```jsx
// 无需清除时
useEffect(() => {
	document.title = `You clicked ${count} times`;
});
```

​		`effect Hook` 使用同一个 `API` 来满足这两种情况。

##### 模拟生命周期

​		虽然函数组件没有生命周期，但通过 `useEffect` 可以模拟部分生命周期。

- `useEffect(() => {}, [])`：挂载时，执行一次。
- `useEffect(() => {})`：挂载以及每次重新渲染时，都执行一次（这是函数组件默认的渲染行为）。
- `useEffect(() => {return () => {}})`：销毁时，执行一次。



#### `useLayoutEffect`

​		其函数签名与 `useEffect` 相同，但它会在所有的 `DOM` 变更之后同步调用 `effect`。可以使用它来读取 `DOM` 布局并同步触发重渲染。在浏览器执行绘制之前，`useLayoutEffect` 内部的更新计划将被同步刷新。

​		尽可能使用标准的 `useEffect` 以避免阻塞视觉更新。

> **提示**
>
> ​		如果你正在将代码从 `class` 组件迁移到使用 `Hook` 的函数组件，则需要注意 `useLayoutEffect` 与 `componentDidMount`、`componentDidUpdate` 的调用阶段是一样的。但是，我们推荐你**一开始先用 `useEffect`**，只有当它出问题的时候再尝试使用 `useLayoutEffect`。
>
> ​		如果你使用服务端渲染，请记住，*无论* `useLayoutEffect` *还是* `useEffect` 都无法在 `Javascript` 代码加载完成之前执行。这就是为什么在服务端渲染组件中引入 `useLayoutEffect` 代码时会触发 `React` 告警。解决这个问题，需要将代码逻辑移至 `useEffect` 中（如果首次渲染不需要这段逻辑的情况下），或是将该组件延迟到客户端渲染完成后再显示（如果直到 `useLayoutEffect` 执行之前 `HTML` 都显示错乱的情况下）。
>
> ​		若要从服务端渲染的 `HTML` 中排除依赖布局 `effect` 的组件，可以通过使用 `showChild && <Child />` 进行条件渲染，并使用 `useEffect(() => { setShowChild(true); }, [])` 延迟展示组件。这样，在客户端渲染完成之前，`UI` 就不会像之前那样显示错乱了。



#### `useCallback`

​		`useCallback` 用于处理性能优化问题。根据函数组件的渲染机制，当组件更新时会重新创建整个函数，这无疑是浪费性能的。

​		`useCallback` 接收一个回调函数和一个依赖项数组，并返回该回调函数的 `memoized` 版本。该回调函数及其存储版本仅在某个依赖项改变时才会被更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 `shouldComponentUpdate`）的子组件时，它将非常有用。`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。

```jsx
// 传入一个回调函数，返回该回调的存储版本。
const memoizedCallback = useCallback(() => {},[a, b]);
```

​		第二个参数，是一个可设置多个依赖项的数组。

- 省略：初始渲染和每次重新渲染时，都会更新回调函数及其存储版本。
- `[]`：在初始渲染时，创建回调函数及其初始存储版本。
- `[dependency1, dependency2, ...]`：初始渲染以及其中任何一个依赖项更新时，都会更新回调函数及其存储版本。

```js
// 组件每次挂载和更新时，都会重新创建并执行整个函数。
let callback = () => {
    setNum(num + 1); // 每次更新callback的存储版本时，都会获取并存储最新的num值。
};

let change1 = useCallback(callback);
let change2 = useCallback(callback, []);
let change3 = useCallback(callback, [dependency]);

console.log(change1 === callback, change2 === callback, change3 === callback); // true, true, true
```

​		注释：`change1`、`change2` 和 `change3` 都会在初始渲染时，存储 `callback` 并返回该存储版本。而后，`change1` 会在每次组件更新时，更新存储版本并返回该存储版本；`change2` 不会再更新和返回其存储版本；`change3` 会在 `dependency` 每次更新时，更新存储版本并返回该存储版本。

​		因此，如果将 `change1`、`change2` 和 `change3` 都注册为点击事件处理程序。那么，`change1` 将能够正常更新 `num`；`change2` 只能更新一次 `num`；`change3` 则在更新一次 `num` 之后，只在后续依赖项更新时才会更新 `num`。



#### `useMemo`

​		`useMemo` 用于缓存数据（类似 `computed`）。如果直接在函数中计算数据，那么每次组件更新时，都会重新创建和计算它们。

```jsx
// 重新创建并执行
function Home() {
    let num = 0;
    
    for (let i = 0; i < 5; i++) {
        num++;
    }
    
    console.log(num); // 5
}
```

​		`useMemo` 接收一个回调函数和一个依赖项数组，它会执行回调函数并将计算结果（即：该回调的返回值）缓存，然后返回该计算结果的 `memoized` 版本。后续只在某个依赖项改变时才重新计算 `memoized` 值。这种优化有助于避免在每次渲染时都进行高开销的计算。

```jsx
// 传入一个回调函数以及一个依赖项，执行回调存储并返回其结果。
let memoziedData = useMemo(() => {}, [a, b]);
```

​		第二个参数用于控制回调的执行时机，是一个可接收多个依赖项的数组，具有以下用法：

- 省略：初始渲染和每次重新渲染时，都会调用回调以更新计算结果及其存储版本。
- `[]`：在初始渲染时，调用回调以创建计算结果并将其存储。
- `[dependency1, dependency2, ...]`：初始渲染以及其中任何一个依赖项更新时，都会调用回调以更新计算结果及其存储版本。

​		记住，传给 `useMemo` 的函数会在渲染期间执行。请不要在这个函数内部执行不应该在渲染期间执行的操作，诸如副作用这类操作，属于 `useEffect` 的适用范畴，而不是 `useMemo`。

​		**你可以把 `useMemo` 作为性能优化的手段，但不要把它当成语义上的保证。**将来，`React` 可能会选择 “遗忘” 以前的一些 `memoized` 值，并在下次渲染时重新计算它们，比如为离屏组件释放内存。先编写在没有 `useMemo` 的情况下也可以执行的代码 —— 之后再在你的代码中添加 `useMemo`，以达到优化性能的目的。



#### `useReducer`

​		`useReducer` 是 `useState` 的替代方案。在某些场景下，`useReducer` 会比 `useState` 更适用，例如 `state` 逻辑较复杂且包含多个子值，或者下一个 `state` 依赖于之前的 `state` 等。并且，使用 `useReducer` 还能给那些会触发深更新的组件做性能优化，因为 [你可以向子组件传递 `dispatch` 而不是回调函数](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down) 。

​		`useReducer` 接收三个参数，首参是一个形如 `(state, action) => newState` 的 `reducer`，用于更新 `state` 的状态；第二个参数用于指定 `state` 的初始状态；第三个参数是一个用于惰性地创建初始 `state` 的初始化函数。`useReducer` 返回一个数组，包含当前的 `state` 状态以及用于通知 `reducer` 更新 `state` 的 `dispatch` 方法（如果你熟悉 `Redux` 的话，就已经知道它如何工作了）。

```jsx
const [state, dispatch] = useReducer(reducer, initState, init);
```

##### 通知更新

​		`dispatch` 用于通知 `reducer` 更新 `state`，并可向 `reducer` 传递新的 `state` 状态或一个 `action` 对象。

```jsx
dispatch('state的新状态'); // 向reducer传递新的state。

dispatch({type: '', data: 'state的新状态'}); // 向reducer传递一个action。
```

##### 更新状态

​		`reducer` 用于操作 `state` 并返回 `state` 的新状态。它接收两个参数，依次是 `state` 的当前状态和调用 `dispatch` 提交的参数。`reducer` 用于更新 `state` 状态。因此，它在组件初始化时不会被调用，只在 `dispatch` 通知更新 `state` 时才会被触发。

```jsx
// 当dispatch直接上传数据的新状态时
const [state, dispatch] = useReducer((state, newState) => {
    console.log(state, newState);
    return newState;
}, 'initState');
```

​		通常，`dispatch` 会传递一个 `action` 对象。其中，包含 `state` 的新状态以及一个 `type` 属性（告知 `reducer` 该操作的类型）。

```jsx
dispatch({type: 'add', data: 1});
```

​		然后，`reducer` 就可以根据本次 `dispatch` 通知的 `action` 类型来对 `state` 执行相应的更新操作。

```jsx
let reducer = (state, { type, data }) => {
    switch (type) {
        case 'add':
            return state + data;
        case 'subtract':
            return state - data;
        default:
            return data;
    }
}

const [state, dispatch] = useReducer(reducer, 0);
```

##### 惰性初始化

​		`useReducer` 的第三个参数，是一个用于惰性地创建初始 `state` 的函数。它可接收初始的 `state` 状态，即：第二个参数。

```jsx
let reducer = (state, action) => newState; 	// 更新state的当前状态
let initState = ''; 						// 定义state的初始状态
let init = (initState) => newInitState; 	// 调整state的初始状态

const [state, dispatch] = useReducer(reducer, initState, init);
```

​		传入的 `init` 回调只会在组件初始化时执行一次，后续更新将不再被触发。它必须返回一个值，来作为处理后的 `state` 初始状态。

​		这么做可以将用于计算初始 `state` 的逻辑从 `reducer` 提取到 `init` 中，也为将来对重置 `state` 的 `action` 做处理提供了便利：

```jsx
// 初始化函数
function init(initialCount) {
  	return {count: initialCount};
}

// 更新函数
function reducer(state, action) {
  	switch (action.type) {
    	case 'increment':
      		return {count: state.count + 1};
    	case 'decrement':
      		return {count: state.count - 1};
    	case 'reset':
      		return init(action.payload); // 使用初始化函数来重载状态
    	default:
      		throw new Error();
  	}
}

// Counter组件
export default function Counter({initialCount}) {
    // 使用组件props的数据
  	const [state, dispatch] = useReducer(reducer, initialCount, init);
  	return (
    	<>
      		Count: {state.count}
      		<button onClick={() => dispatch({type: 'reset', payload: initialCount})}>Reset</button>
      		<button onClick={() => dispatch({type: 'decrement'})}>-</button>
      		<button onClick={() => dispatch({type: 'increment'})}>+</button>
    	</>
  	);
}
```



#### `useId`

​		`useId` 是一个用于生成横跨服务端和客户端的稳定的唯一 `ID`，同时避免 `hydration` 不匹配的 `hook`。

```js
const id = useId(); // 生成一个ID标识值（字符串）。
```

​		注意：`useId` 不是用于在列表中生成密钥，密钥应该从您的数据中生成。

​		它可用于为组件生成一个 `ID`，然后为组件中的元素以该 `ID` 为前缀生成各个元素的 `ID`。`useId` 生成的 `ID` 值可设给元素的 `id` 属性，能够被 `getElementById` 获取，但不能作为 `CSS` 选择器使用（其不符合 `CSS` 选择器的命名规范）。

```jsx
function Home() {
    const id = useId(); // 生成该组件的ID标识值。
    
    return (
    	<div>
        	<h1>Home组件</h1>
     	 	<div>
                <label htmlFor={id + '-firstName'}>First Name</label>
        		<input id={id + '-firstName'} type="text" />
      		</div>
      		<div>
                <label htmlFor={id + '-lastName'}>Last Name</label>
        		<input id={id + '-lastName'} type="text" />
      		</div>
        </div>
    );
}
```

​		`useId` 生成一个被 `:` 包裹的字符串 `token`。这有助于确保 `token` 是唯一的，但在 `CSS` 选择器或 `querySelectorAll` 等 `API` 中不受支持。`useId` 支持 `identifierPrefix` 以防止在多个根应用的程序中发生冲突。 

```js
console.log(useId()); // ':r0:'
```

​		要进行配置，请参阅 [`hydrateRoot`](https://zh-hans.reactjs.org/docs/react-dom-client.html#hydrateroot) 和 [`ReactDOMServer`](https://zh-hans.reactjs.org/docs/react-dom-server.html) 的选项。



#### `useImperativeHandle`

​		`useImperativeHandle` 用于处理重要的操作，接收三个参数：第一个参数是组件接收到的 `Ref` 容器，由父组件创建并传递；第二个参数是一个回调函数，其返回值将回抛给父组件传递的 `Ref` 获取容器；第三个参数是一个包含依赖项的数组。

```js
useImperativeHandle(ref, createHandle, [deps])
```

​		`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 `ref` 这样的命令式代码。`useImperativeHandle` 应当与 [`forwardRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactforwardref) 一起使用：

```jsx
function FancyInput(props, ref) {
  	const inputRef = useRef();
  	useImperativeHandle(ref, () => ({
    	focus: () => {
      		inputRef.current.focus();
    	}
  	}));
  	return <input ref={inputRef} />;
}

FancyInput = forwardRef(FancyInput);
```

​		在本例中，渲染 `<FancyInput ref={myInputRef} />` 的父组件可以调用 `myInputRef.current.focus()`。

```jsx
function T() {
    let myInputRef = useRef();
    let f = () => {
        myInputRef.current.focus();
    }
    return (
        <div>
            <h1>获得子组件焦点</h1>
            <FancyInput ref={myInputRef} />
            <button onClick={f}>获取焦点</button>
        </div>
    );
}
```

​		它的另一个作用是，可以在父组件中获取子组件中的多个元素。如下所示：

```jsx
// 在父组件中
import { useRef } from "react"
import Son from "./Son/Son.jsx";

export default function () {
    const eleS = useRef();
    
    const getElement = () => { 
        console.log(eleS.current.ele1.current);
        console.log(eleS.current.ele2.current);
    };
    
    return (
        <div>
            <button onClick={() => getElement()}>点击获取子组件元素</button>
            <Son ref={eleS} />
        </div>
    );
} 
```

```jsx
// 在子组件中
import { useRef, forwardRef, useImperativeHandle } from "react"

export default forwardRef(function (props, ref) {
    const ele1 = useRef();
    const ele2 = useRef();
    
    // 第二参数的返回数据将回抛给ref容器，父组件可以直接从中获取。
    useImperativeHandle(ref, () => ({ ele1, ele2 }), []);
    
    return (
        <div>
            <h2 ref={ele1}>子组件中的元素1</h2>
            <h3 ref={ele2}>子组件中的元素2</h3>
        </div>
    );
}) 
```



#### `useTransition`

​		`useTransition` 可创建一个过渡任务。常用于过渡加载任务（即：延迟加载），例如：降低其余元素的加载优先级。

​		`useTransition` 不接收任何参数，返回一个数组，其中包含一个表示过渡任务等待状态的布尔值（`true` 表示处于等待中，`false` 表示已开启，默认 `false`），以及一个启动该过渡任务的函数（接收一个回调函数，用于指定要延迟执行的任务）。

```js
// 1、isPending表示过渡任务是否处于等待状态
// 2、startTransition用于指定并启动过渡任务
const [isPending, startTransition] = useTransition();
```

​		默认情况下，组件更新时其虚拟 `DOM` 会同时更新。使用 `useTransition` 可以使指定的更新任务延迟执行，延迟时间不固定，取决于机器性能。当过渡任务的等待状态为 `true` 时，表示正在等待任务完成过渡（或者说，过渡任务正在执行）；为 `false` 时，表示已经开启过渡任务（或者说，过渡任务执行完毕）。

```jsx
import {useState, useTransition} from 'react';

export default function Home() {
    let [data, setData] = useState(null);
    const [isPending, startTransition] = useTransition();
    
    const change = (e) => {
        // 指定并开启延迟执行的任务
        startTransition(() => {
            setData(e.target.value); // 执行过渡任务，此时isPending为false。
        });
    };
    
    return (
    	<div>
        	<h1>useTransition</h1>
            <input onChange={change} />{isPending ? '加载中' : '加载完'}
            {
                Array(1000).fill().map((item, index) => {
                    return (<p key={index}>{data}</p>);
                })
            }
        </div>
    );
}
```

​		注意：过渡任务中触发的更新会让更紧急的更新先进行，比如点击。过渡任务中的更新将不会展示由于再次挂起而导致降级的内容。这个机制允许用户在 `React` 渲染更新的时候继续与当前内容进行交互。



#### `useDeferredValue`

​		`useDeferredValue` 用于延迟一个数据的更新。

​		`useDeferredValue` 接受一个值，并返回该值的延迟更新副本，对该副本的更新将推迟到更紧急的更新之后。如果当前渲染是一个紧急更新的结果，比如用户输入，`React` 将返回之前的副本，然后在紧急渲染完成后更新副本并触发重新渲染。

```jsx
const deferredValue = useDeferredValue(value);
```

​		该 `hook` 与使用防抖和节流去延迟更新的用户空间 `hooks` 类似。使用 `useDeferredValue` 的好处是，`React` 将在其他工作完成（而不是等待任意时间）后立即进行更新，并且像 [`startTransition`](https://zh-hans.reactjs.org/docs/react-api.html#starttransition) 一样，延迟值可以暂停，而不会导致现有内容的意外降级。

```jsx
import {useState, useDeferredValue} from 'react';

export default function Home() {
    let [data, setData] = useState(null);
    // 传入一个数据，并返回它的延迟更新副本。
    const deferredValue = useDeferredValue(data);
    
    const change = (e) => {
		setData(e.target.value);
    };
    
    return (
    	<div>
        	<h1>useDeferredValue</h1>
            <input onChange={change} />
            {
                Array(1000).fill().map((item, index) => {
                    return (<p key={index}>{deferredValue}</p>);
                })
            }
        </div>
    );
}
```

##### 存储延迟的子组件

​		`useDeferredValue` 仅延迟更新你传递给它的值。如果你想要在紧急更新期间防止子组件重新渲染，则还必须使用 `React.memo` 或 `React.useMemo` 记忆该子组件：

```jsx
function Typeahead() {
  	const query = useSearchQuery('');
  	const deferredQuery = useDeferredValue(query);

  	// Memoizing 告诉 React 仅当 deferredQuery 改变的时候才重新渲染，而不是 query。
  	const suggestions = useMemo(() => <SearchSuggestions query={deferredQuery} />, [deferredQuery]);

  	return (
    	<>
      		<SearchInput query={query} />
      		<Suspense fallback="Loading results...">
        		{suggestions}
      		</Suspense>
    	</>
  	);
}
```

​		记忆该子组件告诉 `React` 仅当 `deferredQuery` 改变的时候才需要去重新渲染，而不是 `query` 改变的时候重新渲染。这个限制不是 `useDeferredValue` 独有的，它和使用防抖或节流的 `hooks` 使用相同的模式。



#### `useDebugValue`

​		`useDebugValue` 用于在 `React` 开发者工具中显示自定义 `hook` 的名称，在要显示名称的自定义 `Hook` 中调用。默认情况下，当调用该 `Hook` 之后，开发者工具会在组件的 `hooks` 属性中，以省略 `use` 的自定义 `Hook` 名称为键，值为传递的 `debug` 值。

```js
export const useCustomHook = () => {
    useDebugValue(); // 在React开发者工具中显示该自定义Hook的名称：CustomHook，但没有debug值。
}
```

​		可以给 `useDebugValue` 传入一个值，来为自定义 `Hook` 添加 `debug` 值。但 `React` 不推荐向每个自定义 `Hook` 添加 `debug` 值，因为当它作为共享库的一部分时才最有价值。

```js
export const useCustomHook = () => {
    useDebugValue('useCustomHook'); // 在React开发者工具中显示该自定义Hook的名称，且其debug值为：'useCustomHook'。
}
```

##### 延迟格式化

​		在某些情况下，格式化值的显示可能是一项开销很大的操作。除非需要检查 `Hook`，否则没有必要这么做。

​		因此，`useDebugValue` 接受一个格式化函数作为可选的第二个参数。该函数只有在 `Hook` 被检查时才会被调用。它接受 `debug` 值作为参数，并且会返回一个格式化的显示值（即：新的 `debug` 值）。

```js
useDebugValue('debug', (debug) => 'newDebug');
```



### 自定

​		可以结合已有的 `Hook API` 来实现自定义的 `Hook API`， 通过自定义的 `Hook`，可以将组件逻辑提取到可重用的函数中。

​		自定义 `Hook` 以 `use` 开头，小驼峰式命名。用于抽离或封装组件逻辑，以提高代码复用性。与其他 `Hook` 一样，自定义的 `Hook` 也只能在函数组件的顶层使用。



#### 管理目录

​		为了集中管理自定义的 `Hooks`，建议将它们统一放置到 `src` 目录下的 `hooks` 文件夹中。

```diff
|- src
+	|- hooks
+		|- my-hook.js
```

​		例如，在 `hooks` 中建立一个 `my-hook.js` 文件。



#### 封装逻辑

​		在 `my-hook.js` 文件中，封装并导出函数。

```js
import React from 'react';
const {useState} = React;

// 定义并导出自定义的Hook
export const useNumInfo = () => {
    // 只在顶层使用Hook。
    return '';
}
```



#### 导入使用

​		在函数组件中，直接导入 `hook` 文件中的自定义 `Hook` 即可。

```jsx
import { useNumInfo } from "@/hooks/my-hook.js";

function Home() {
    // 只在顶层使用Hook。
    let [data, setData] = useNumInfo();
}
```



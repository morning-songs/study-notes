# 组合式`API`

在`vue2`中，`computed`和`watch`等选项式`API`在`vue3`的`setup`中具有不一样的写法，且需要手动导入。

<hr>

### `computed`计算属性

在`vue2`的项目中，`computed`在项目初始运行时已不再自动计算，而是在首次使用时计算结果，或依赖数据变动时重新计算。

在`vue3`的项目中，`computed`作为方法使用，其参数为一个回调函数，在这个回调中的所有响应式数据都会自动被追踪监听。

```js
import { computed, ref } from "vue"

export default {
    setup () {
    	let age = ref(10);
    	// 在计算属性中，只作简单的表达式运算，必须通过return返回数据。
    	let newAge = computed(() => {
        	return age.vaule += 10;
    	});
	}
}
```



##### `get`与`set`

当需要在`computed`中做更多的操作时，可通过传入一个对象来设置`get`函数和`set`函数。

```js
setup () {
    let age = ref(10);
    
    let newAge = computed({
        get() {
            // 获取计算结果时触发
            return age.vaule += 10;
        },
        set(newValue) {
            // 修改计算结果时触发，参数为新设置的值
            // 例如：在外部修改newAge = 20;时，此处newValue = 20;
        }
    });
}
```

<hr>

### `watch`侦听器

在`vue2`中，`watch`用于侦听组件实例上的某个数据，仅当数据变化时触发，初始运行时不会触发。

**参数**：

- 一：数据源（引用值），可以是`ref`、`reactive`、`getter`函数或者存储多个数据源的数组（前三者之一）。
- 二：回调处理，回调处理参数分别为新值和旧值。【新值是通过拷贝旧值后，根据要求修改而来的】
- 三：配置对象，可配置是否开启深度监听、是否开启立即监听等。

```js
import { watch, ref } from "vue"

export default {
    setup() {
        let count = ref(0),
            num = ref(18);
        // 监听count数据的变化
        watch(count, (newVal, oldVal) => {
            console.log("变化后：", newVal, "变化前：", oldVal);
        });
        // getter函数形式：通过一个回调return出监听值（原始值）
        watch(() => count.value, (newVal, oldVal) => {
            console.log("变化后：", newVal, "变化前：", oldVal);
        });
        // 数组形式：监听的任一数据变动时，都会触发函数。一般用于相同变化（处理逻辑）的一组值
        watch([count, num], (newVal, oldVal) => {
            console.log("变化后：", newVal, "变化前：", oldVal);
        })
        
    }
}
```



##### 深度监听

在`vue2`的`watch`中，默认只是浅度监听数组和对象引用地址的变化。而在`vue3`中，则会深度监听引用值内部数据的变化。

**提示**：`watch`监听的数组和对象，其内部数据变动时，会触发监听。但变化前后的值一样，是因为新值是旧值拷贝而来的。

**注释**：而当有特殊需要，查看旧值时，可通过导入`lodash`的对象，调用该对象的`cloneDeep`方法深度克隆一个一样的旧值。

**提示**：当想要关闭深度监听时，可配置`deep`值为`false`，但监听的数据源必须通过`getter`函数的形式返回。

```js
import {watch, reactive} from "vue"
import _ from "lodash"

export default {
    setup() {
        let obj = reactive({name: "丸子", age: 16}),
            arr = reactive([10, 20, 30, 40]);
        // 默认开启深度监听
        watch(obj, (newVal, oldVal) => {
            console.log(newVal === oldVal); // true
        })
        // 通过getter函数的回调，深克隆一个旧值，然后存到oldVal之中
        watch(() => _.cloneDeep(arr), (newVal, oldVal) => {
            console.log(newVal === oldVal); // false
        })
        // 关闭深度监听：当数据量很庞大时，将消耗大量的资源。
        watch (
            () => obj, // 数据源必须通过回调函数形式返回
            (newVal, oldVal) => {
            	console.log(newVal === oldVal); // true
        	},
        	{
            	deep: false // 关闭深度监听
        	}    
        )
        // 监听某一条数据
        watch(() => obj.name, (newVal, oldVal) => {
            console.log("变化前", newVal, "变化后", oldVal);
        })
    }
}
```



##### 立即监听

与开启和关闭深度监听一样，设置立即监听的数据源必须通过`getter`函数形式返回。

```js
setup() {
	let obj = reactive({name: "丸子", age: 16}),
		arr = reactive([10, 20, 30, 40]);
	// 开启立即监听，默认关闭
	watch (
		() => obj, // 数据源必须通过回调函数形式返回
		(newVal, oldVal) => {
			console.log(newVal === oldVal); // true
		},
		{
			immediate: true // 开启立即监听，初始时触发
		}    
	)
}
```

**提示**：通过配置`immediate`来设置立即监听相对繁琐，`vue3`提供了`watchEffect`方法来触发立即监听。

**注释**：在`watchEffect`中使用的响应数据都会被自动追踪监听，任一数据变动时都将触发监听回调处理。

```js
// 假设需要通过用户id请求用户相关数据，并且当id变化后，重新请求用户数据。
let userId = ref(0),
    userInfo = ref(null);

// watch的写法
watch (
	userId,
    (newVal, oldVal) => {
        // 发起请求，得到响应
        console.log("得到响应：", userInfo);
    },
    {
        immediate: true
    }
)

// watchEffect的写法
watchEffect (() => {
    let id = userId.value;
    // 发起请求，得到响应
    console.log("得到响应：", userInfo);
})
```

**小结**：`watch`与`watchEffect`的区别

- `watchEffect`：不需要指定侦听的数据源；自动追踪内部使用的响应式数据的变化；初始时主动触发一次。类似`vue2`的`computed`
- `watch`：必须指定侦听的数据源（深度监听）；还需要指定对该数据源变化前后的回调处理；初始时不会触发。



##### 异步监听

当在异步执行的程序中设置监听器后，会导致该侦听即使在其组件销毁后也无法停止，从而造成内存泄漏。

```js
setTimeout(() => {
    // 一直监听，导致内存泄漏
    watchEffect(() => { ... })
}, 2000)
```

**注释**：一般不建议在异步回调中设置监听，但有特殊需要时，可接收监听器的返回值，它是一个终止该监听器的方法。

```js
let unwatch = watch(data, (newVal, oldVal) => {}),
	unwatchEffect = watchEffect(() => { ... });

// 清除侦听器
setTimeout(() => {
	unwatch();
    unwatchEffect();
}, 2000)
                                       
// 可在组件销毁时，清除其所有监听
```


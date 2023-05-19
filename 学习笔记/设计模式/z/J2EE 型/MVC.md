# `MVC`

`MVC` 模式代表 `Model-View-Controller`（模型 - 视图 - 控制器）模式。这种模式用于应用程序的分层开发。

- **`Model`（模型）** - 模型代表一个存取数据的对象。它也可以带有逻辑，在数据变化时更新控制器。
- **`View`（视图）** - 视图代表模型包含的数据的可视化。
- **`Controller`（控制器）** - 控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开。

​														 <img src="images/MVC/1200px-ModelViewControllerDiagram2.svg_.png" alt="img" style="zoom: 33%;" /> 

在前端

- `Model`：对应数据
- `View`：对应 `HTML`
- `Controller`：对应事件（交互事件和控制事件等）

三者的关系大致如以下流程所示：首先，当用户在视图上产生交互行为（如点击按钮）时，视图会告知控制器发生了什么；然后，控制器会处理该事件（事件逻辑在控制器中，不在视图上）；接着，控制器在处理过事件后会告知模型如何更新数据；最后，模型更新完数据之后，驱动所有依赖了变更数据的视图更新渲染。



### 实现

##### 简单案例

```html
<div id="wrap">
    <h3>我张三今天就要吃水果！</h3>
    <p class="buy">
        买：
        <input type="button" value="买个苹果">
        <input type="button" value="买个梨子">
    </p>
    <p class="eat">
        吃：
        <input type="button" value="吃个苹果">
        <input type="button" value="吃个梨子">
    </p>
    <p class="count">
        <b>张三</b>还剩：<span>0</span>个苹果，<span>0</span>个梨子。
    </p>
    <div class="total">
        总共还有：<span>0</span>个水果。
    </div>
</div>
<style>
    * {
        margin: 0;
        padding: 0;
    }
    p {
        margin: 20px 10px;
    }
    span {
        color: red;
        font-weight: bolder;
    }
</style>
```

一般逻辑如下

```js
let buyBtns = document.querySelectorAll("#wrap .buy input"),
    eatBtns = document.querySelectorAll("#wrap .eat input"),
    countspans = document.querySelectorAll("#wrap .count span"),
    totalspan = document.querySelector("#wrap .total span"),
    countArr = [0, 0]; // 0: 苹果，1: 梨子

// 买（增加）
buyBtns.forEach((item, i) => {
    item.onclick = function() {
        countspans[i].innerHTML = ++countArr[i];
        totalspan.innerHTML = countArr.reduce((a, b) => a + b);
    }
})

// 吃（减少）
eatBtns.forEach((item, i) => {
    item.onclick = function () {
        countspans[i].innerHTML = countArr[i] = Math.max(0, --countArr[i]);
        totalspan.innerHTML = countArr.reduce((a, b) => a + b);
    }
})
```

##### 简单实现

首先，定义一个 `MVC` 自执行函数。

```js
const MVC = (function() {
    // 数据层(M)
    const Model = (function() {
       	// 初始数据
        const data = [0, 0];
        // 修改方法
        return {
            // 访问数据
            getData() {
                return [...data]; // 这里，应该深拷贝一份并返回该副本
            },
            // 更新数据
            update(i, isAdd) {
                // 修改数据
                isAdd ? data[i]++ : data[i]--;
                // 校验数据
                this.validator();
                // 驱动视图
                View.update(data, i);
            },
            // 校验数据
            validator() {
                data.forEach((item, i, arr) => {
                    arr[i] = Math.max(0, Math.min(item, 20)); // 限制范围：[0, 20]
                });
            }
        }
    })();
    
    // 视图层(V)
    const View = (function() {
        // 需要变更的DOM
        let buyBtns = document.querySelectorAll("#wrap .buy input"),
            eatBtns = document.querySelectorAll("#wrap .eat input"),
            countspans = document.querySelectorAll("#wrap .count span"),
            totalspan = document.querySelector("#wrap .total span");
        
        return {
            // 初始化视图
            init() {
                this.update(Model.getData());
                this.callController();
            },
            // 更新视图
            update(data, i) {
                if(typeof i === "number") {
                    countspans[i].innerHTML = data[i]; // 部分更新
                } else {
                    countspans.forEach((dom, i) => dom.innerHTML = data[i]); // 全部更新
                }
                totalspan.innerHTML = data.reduce((a, b) => a + b);
            },
            // 通知控制器（每次事件触发时）
            callController() {
                buyBtns.forEach((dom, i) => {
                    dom.onclick = function() {
                        Controller.buyEvent(i); 
                    }
                });
                eatBtns.forEach((dom, i) => {
                    dom.onclick = function() {
                        Controller.eatEvent(i);
                    }
                });
            }
        }
    })();
    
    // 控制器(C)
    const Controller = {
		// buy的控制器
        buyEvent(i) {
            Model.update(i, true);
        },
        // eat的控制器
        eatEvent(i) {
            Model.update(i, false);
        }
    };

	// 返回对外使用的接口
	return {
        init() {
            View.init(); // 初始更新视图
        }
    }
})();

// 使用时，只需要调用init方法进行初始化即可。
MVC.init();
```

如果要新增一项水果，则只需要增加一条 `HTML` 结构以及 `Model` 中的初始化数据即可。

```html
<p class="buy">
    买：
    <input type="button" value="买个苹果">
    <input type="button" value="买个梨子">
    <input type="button" value="买个枣子">
</p>
<p class="eat">
    吃：
    <input type="button" value="吃个苹果">
    <input type="button" value="吃个梨子">
    <input type="button" value="吃个枣子">
</p>
<p class="count">
    <b>张三</b>还剩：<span>0</span>个苹果，<span>0</span>个梨子，<span>0</span>个枣子。
</p>
```

```js
const data = [0, 0, 0];
```


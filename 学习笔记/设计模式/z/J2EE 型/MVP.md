# `MVP`

`MVP` 全称：`Model-View-Presenter`；从经典的 `MVC` 模式演变而来，它们的基本思想有相通的地方：`Controller/Presenter` 负责逻辑的处理，`Model`提供数据，`View`负责显示。【`Presenter`：主持人、任命者，引申为 “表示器、表示层”】

`MVP` 模式，将 `View` 与 `Model` 完全分离，是一种基于表示器的线性的双向通信的模式。在 `M` 和 `V` 中间的是 `P`，`M` 和 `V` 分别与 `P` 建立双向的通信渠道，而 `M` 和 `V` 之间不再进行通信。

`MVP` 从 `MVC` 演变而来，通过表示器将视图与模型巧妙地分开。在该模式中，视图通常由表示器初始化，它呈现用户界面（`UI`）并接受用户所发出命令，但不对用户的输入作任何逻辑处理，而仅仅是将用户输入转发给表示器。通常每一个视图对应一个表示器，但是也可能一个拥有较复杂业务逻辑的视图会对应多个表示器，每个表示器完成该视图的一部分业务处理工作，降低了单个表示器的复杂程度，一个表示器也能被多个有着相同业务需求的视图复用，增加单个表示器的复用度。表示器包含大多数表示逻辑，用于处理视图以及与模型交互以获取或更新数据等。模型描述了系统的处理逻辑，用于存储和处理数据，模型对于表示器和视图一无所知。

##### 优缺点

`MVP` 模式下表示层的优势体现在下面三个方面：

1、`View` 与 `Model` 完全隔离。

得益于此，`Model` 和 `View` 之间具有良好的松耦合设计。这意味着，如果 `Model` 或 `View` 中的一方发生变化，只要交互接口不变，另一方就没必要对上述变化做出改变。这使得 `Model` 层的业务逻辑具有很好的灵活性和可重用性。

2、`Presenter` 与 `View` 的具体实现技术无关。

也就是说，采用诸如 `Windows` 表单、`WPF`、`Web` 表单等用户界面构建技术中的任意一种来实现 `View` 层，都无需改变系统的其他部分。甚至为了使 `B/S`，`C/S` 部署架构能够被同时支持，应用程序可以用同一个 `Model` 层适配多种技术构建的 `View` 层。

3、可以进行 `View` 的模拟测试。

过去，由于 `View` 和 `Model` 之间的紧耦合，在 `Model` 和 `View` 同时开发完成之前对其中一方进行测试是不可能的。出于同样的原因，对 `View` 或 `Model` 进行单元测试也很困难。现在，`MVP` 模式解决了所有的问题。在 `MVP` 模式中，`View` 和 `Model` 之间没有直接依赖，开发者能够借助模拟对象注入测试两者中的任一方。

缺点：

`MVP` 的明显缺点是增加了代码的复杂度，特别是针对小型 `Android` 应用的开发，会使程序变得冗余。`Presenter` 中除了应用逻辑以外，还有大量的 `View --> Model`，`Model --> View` 的手动同步逻辑，这导致 `Presenter` 臃肿，维护困难。视图的渲染过程也会放在 `Presenter` 中，造成视图与 `Presenter` 交互过于频繁。如果某特定视图的渲染很多，就会造成 `Presenter` 与该视图的联系过于紧密，而一旦该视图需要变更，那么 `Presenter` 也需要变更，不能像预期的那样降低耦合度和增加复用性。

##### `MVC` 与 `MVP`

`MVP` 从经典的模式 `MVC` 演变而来，它们的基本思想有相通的地方：`Controller/Presenter` 负责逻辑的处理，`Model` 提供数据，`View` 负责显示。作为一种新的模式，`MVP` 与 `MVC` 有着一个重大的区别：在 `MVP` 中 `View` 并不直接使用 `Model`，它们之间的通信是通过 `Presenter`（`MVC` 中的 `Controller`）来进行的，所有的交互都发生在 `Presenter` 内部，而在 `MVC` 中 `View` 会直接从 `Model` 中读取数据而不是通过 `Controller`。

在 `MVC` 里，`View` 是可以直接访问 `Model` 的。从而，`View` 里会包含 `Model` 信息，不可避免的还要包括一些业务逻辑。在 `MVC` 模型里，更关注的 `Model` 的不变，而同时有多个对 `Model` 的不同显示及 `View`。所以，在 `MVC` 模型里，`Model` 不依赖于 `View`，但是 `View` 是依赖于 `Model` 的。不仅如此，因为有一些业务逻辑在 `View` 里实现了，导致要更改 `View` 也是比较困难的，至少那些业务逻辑是无法重用的。

`MVP` 模式，使得视图层完全不依赖于模型层，相当于将视图从特定的业务场景中脱离出来，做到了对业务完全不可知的状态。因此可以将视图层组件化，提供一系列接口供表示层操作，这样就可以做出高度可复用的视图组件了。

##### `MVP` 改进

在 `MVP` 里，`Presenter` 完全把 `Model` 和 `View` 进行了分离，主要的程序逻辑在 `Presenter` 里实现。而且 `Presenter` 与具体的 `View` 是没有直接关联的，而是通过定义好的接口进行交互，从而使得在变更 `View` 时候可以保持 `Presenter` 的不变（即重用）。不仅如此，我们还可以编写测试用的 `View`，模拟用户的各种操作，从而实现对 `Presenter` 的测试，而不需要使用自动化的测试工具。我们甚至可以在 `Model` 和 `View` 都没有完成时候，就可以通过编写 `MockObject`（即实现了 `Model` 和 `View` 的接口，但没有具体的内容）来测试 `Presenter` 的逻辑。

在 `MVP` 里，应用程序的逻辑主要在 `Presenter` 里实现，其中的 `View` 是很薄的一层。因此就有人提出了 `PresenterFirst` 的设计模式，也就是根据 `UserStory` 来首先设计和开发 `Presenter`。在这个过程中，`View` 是很简单的，它只要能够把信息显示清楚就可以了。后续可根据需要再随便更改 `View`，而对 `Presenter` 不会有任何的影响了。如果要实现的 `UI` 比较复杂，而且相关的显示逻辑还跟 `Model` 有关系，就可以在 `View` 和 `Presenter` 之间放置一个 `Adapter`。由这个 `Adapter` 来访问 `Model` 和 `View`，避免两者之间的关联。而同时，因为 `Adapter` 实现了 `View` 的接口，从而可以保证与 `Presenter` 之间接口的不变。这样就可以保证 `View` 和 `Presenter` 之间接口的简洁，又不失去 `UI` 的灵活性。在 `MVP` 模式里，`View` 只应该有简单的 `Set/Get` 的方法、用户输入和设置界面显示的内容，除此之外就不应该有更多的内容，并且绝不容许它直接访问 `Model` —— 这就是与 `MVC` 很大的不同之处。



### 实现

##### 简单案例

例如，实现以下结构与功能。

```html
<div id="wrap">
    <ul>
        <li>
            <h4>亚极陀</h4>
            <div class="picture"><img src="./protect1/images/agito.png"></div>
            <p class="like">赞：<span>0</span></p>
            <p class="dislike">踩：<span>0</span></p>
        </li>
        <li>
            <h4>电王</h4>
            <div class="picture"><img src="./protect1/images/den-o.webp"></div>
            <p class="like">赞：<span>0</span></p>
            <p class="dislike">踩：<span>0</span></p>
        </li>
        <li>
            <h4>龙骑</h4>
            <div class="picture"><img src="./protect1/images/ryuki.webp"></div>
            <p class="like">赞：<span>0</span></p>
            <p class="dislike">踩：<span>0</span></p>
        </li>
    </ul>
</div>
```

样式如下：

```css
* {
    margin: 0;
    padding: 0;
}
#wrap {
    width: 1000px;
    margin: 0 auto;
}
#wrap ul {
    display: flex;
    justify-content: space-between;
    align-items: center;
}
#wrap ul li {
    flex: 1;
    list-style: none;
    text-align: center;
}
#wrap ul li .picture img {
    width: 300px;
    height: 385px;
    vertical-align: middle;
}

#wrap ul li p {
    margin: 8px auto 0;
    width: 150px;
    height: 30px;
    line-height: 30px;
    border: 1px solid #ccc;
    cursor: pointer;
    user-select: none;
}

#wrap ul li .like span {
    color: green;
    font-weight: bold;
}
#wrap ul li .dislike span {
    color: red;
    font-weight: bold;
}
```

##### 简单实现

```html
<div id="wrap">
    <ul>
		<!-- 结构通过循环生成 -->
    </ul>
</div>
```

```js
const MVP = (function () {
    // Model —— 数据层
    const M = (function () {
        const data = [
            {name: "亚极陀", src: "./protect1/images/agito.png", like: 0, dislike: 0},
            {name: "电王", src: "./protect1/images/den-o.webp", like: 0, dislike: 0},
            {name: "龙骑", src: "./protect1/images/ryuki.webp", like: 0, dislike: 0}
        ]
        return {
            getData() {
                return [...data]; // 此处应该深拷贝！！！
            },
            updateLike(i) {
                return ++data[i].like;
            },
            updateDislike(i) {
                return ++data[i].dislike;
            }
        }
    })();
    
    // View —— 视图层
    const V = (function () { 
        const oUl = document.querySelector("#wrap ul");
        let likeSpans = null,
            dislikeSpans = null;
        return {
            // 视图层初始化
            init(data) {
                let html = "";
                data.forEach(item => {
                    html += `
                            <li>
                                <h4>${item.name}</h4>
                                <div class="picture"><img src=${item.src}></div>
                                <p class="like">赞：<span>${item.like}</span></p>
                                <p class="dislike">踩：<span>${item.dislike}</span></p>
                            </li>
                        `;
                })
                oUl.innerHTML = html;
                likeSpans = document.querySelectorAll("#wrap .like span");
                dislikeSpans = document.querySelectorAll("#wrap .dislike span");
            },
            // 更新like
            updateLike(i, value) {
                likeSpans[i].innerHTML = value;
            },
            // 更新disLike
            updateDislike(i, value) {
                dislikeSpans[i].innerHTML = value;
            }
        }
    })();
    
    // Presenter —— 管理层
    const P = (function () { 
        // 保存元素
        let likeBtns = null,
            dislikeBtns = null;

        return {
            // 管理层初始化
            init() {
                V.init(M.getData());
                // 获取元素
                likeBtns = document.querySelectorAll("#wrap .like");
                dislikeBtns = document.querySelectorAll("#wrap .dislike");
                this.addEvent();
            },
            // 添加事件
            addEvent() {
                likeBtns.forEach((btn, i) => {
                    btn.onclick = function() {
                        // 通知 M 更新数据
                        M.updateLike(i);
                        let value = M.getData()[i].like;
                        // 通知 V 更新视图
                        V.updateLike(i, value);
                    }
                });
                dislikeBtns.forEach((btn, i) => {
                    btn.onclick = function () {
                        // 通知 M 更新数据
                        M.updateDislike(i);
                        let value = M.getData()[i].dislike;
                        // 通知 V 更新视图
                        V.updateDislike(i, value);
                    }
                });
            }
        }
    })();

    return {
        // 启动程序
        exe() {
            P.init();
        }
    }
})();

MVP.exe();
```



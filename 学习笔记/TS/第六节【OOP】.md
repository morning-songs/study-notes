# 面向对象

`TS` 主要是在 `JS` 的基础上对类型进行了较为严格的要求。因此，在面向对象编程中也会在 `JS` 的写法有所增益。



### 声明

##### 预先声明

在 `TS` 的类中通过 `this` 读写指定的属性或方法时，必须在类中的顶部位置预先声明属性或方法，否则将不能被 `this` 调用。

```js
// 在JS中
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
}

console.log(new Point(0, 0)); // Point { x: 0, y: 0 }
```

以上代码在 `JS` 中运行没有任何问题。但当将它迁移到 `TS` 时，便会因为没有预先 `x` 和 `y` 声明而报错，因为类型 `Point` 上没有它们。

```tsx
// 在TS中
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
}

console.log(new Point(0, 0)); // Point { x: 0, y: 0 }
```

`TS` 严格限制类型，禁止未经声明地在类型上自定义属性。因此对于 `TS` 类来说，只有预先声明的属性和方法，才会被添加到其类型上。

##### 参数声明

`TS` 会在公共的属性和方法前，默认加上一个 `public` 修饰符，通常可以省略。上面的代码，实际上应该是这样的。

```tsx
class Point {
    public x: number;
    public y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
}

console.log(new Point(0, 0)); // Point { x: 0, y: 0 }
```

实际上，`public` 不仅可以用于声明属性和方法，还可以用于声明参数，并且 `public` 参数会成为类类型上的属性。了解到这一点之后，便可以解决将所有属性声明都堆叠到类顶部的问题了。

```tsx
class Point {
    constructor(public x: number, public y: number) {
        this.x = x;
        this.y = y;
    }
}

console.log(new Point(0, 0)); // Point { x: 0, y: 0 }
```

##### 类修饰符

为标注类字段的使用权限，`TS` 为类类型提供了三个修饰符：`private`（私有的）、`protected`（受保护的）和 `public`（公共的）。

其中，私有字段只能在类自身的内部使用，受保护字段只能在类及其子类的内部使用，公共字段则可以在类及其子类的内外部使用。

```tsx
class Base {
    public a: string;
    protected b: number;
	private c: boolean;
    constructor(a: string, b: number, c: boolean) {
        this.a = a;
        this.b = b;
        this.c = c;
        console.log(this.a, this.b, this.c); // 's' 0 true
    }
}

class Sub extends Base {
    constructor(a: string, b: number, c: boolean) {
        super(a, b, c);
        console.log(this.a); // 's'
        console.log(this.b); // 0
        console.log(this.c); // Property 'c' is private and only accessible within class 'Base'.
    }
}

let sub = new Sub('s', 0, true);
```

##### 只读字段

在类中也可以使用 `readonly` 修饰符来将字段设为只读。更重要的是，它可以结合类修饰符一起使用，但必须放在它们后面。

初始化：只读字段只能在 `constructor()` 中进行赋值，被称为初始化。然后它们在其他任何地方都只能访问，不能再修改了。

```tsx
class Base {
    public readonly a: string;
    protected readonly b: number;
    private readonly c: boolean;
    constructor(a: string, b: number, c: boolean) {
        // 初始化只读字段
        this.a = a;
        this.b = b;
        this.c = c;
        console.log(this.a, this.b, this.c); // 'b' 0 true
    }
    fn() {
        // 只能访问，不能修改
        this.a = '10'; // Cannot assign to 'a' because it is a read-only property.
    }
}

let base = new Base('b', 0, true);
```



### 在 `Vue` 中使用

创建 `vue` 项目时，可以勾选 `TypeScript` 来将项目中原先支持 `JS` 语法的地方替换成支持 `TS` 的语法。

变化：

- 项目中所有的 `js` 文件都会被升级为 `ts` 文件。
- 项目中所有支持 `JS` 语法的地方，也都支持 `TS` 的语法，并被 `TS` 的语法所替换。

注意：要清楚项目中 `Vue` 和 `JS` 各自的环境。`Vue` 具有自己的语法，不要在该使用 `Vue` 语法的地方使用 `JS` 或 `TS` 语法。

```vue
<template>
	<div class="home">
        {{a}}
    </div>
</template>

<script lang="ts" setup>
    let a: string = "Home";
</script>
```



### 配置

#### 配置文件

通过 `tsc --init` 指令，或者直接在项目根目录下创建，都会将隐藏在项目根目录下的 `tsconfig.json` 配置文件生成出来。

更多参考：[`tsconfig`](https://www.typescriptlang.org/tsconfig) 

#### 基本配置

更多参考：[基本配置](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#tsconfig-bases) 

```json
{
  	"extends": "@tsconfig/node12/tsconfig.json",
  	"compilerOptions": {
    	"preserveConstEnums": true
  	},
  	"include": [], // 指定需要被编译的文件
  	"exclude": ["node_modules", "**/*.spec.ts"] // 指定不要被编译的文件
}
```

##### `extends`

配置是可以继承的，通过 `extends` 就可以使该配置文件继承另一个配置文件。其值是要继承的另一个配置文件的路径字符串。

更多参考：[`extends` 配置](https://www.typescriptlang.org/tsconfig#extends) 

##### `files`

`files` 用于指定需要被编译的文件（如：`.ts`、`.tsx`、`.d`、`.js` 和 `.jsx`）。如果找不到，就会报错。

编译文件：在 `files` 中指定要被编译的 `ts` 文件后，直接执行 `tsc` 指令，便可以将指定的 `ts` 文件编译成相应的 `js` 文件了。

```json
"files": [
    "core.ts",
    "sys.ts",
    "types.ts",
    "scanner.ts",
    "parser.ts",
    "utilities.ts",
    "binder.ts",
    "checker.ts",
    "tsc.ts"
]
```

在 `files` 中必须指定确切的文件路径（相对或绝对），其灵活性不高。当需要指定很多文件或一个目录时，则应配置 `include` 选项。

更多参考：[`files` 配置](https://www.typescriptlang.org/tsconfig#files) 

##### `include`

`include` 用于指定需要被编译的文件或目录（包括其中的文件和所有后代文件）。

由于要编译的文件可能很多，因此 `"include"` 允许在其数组中使用一些正则表达式（支持使用通配符来创建全局匹配模式）。

- `*`：匹配零个或多个字符（不包括目录分隔符）
- `?`：匹配任意一个字符（不包括目录分隔符）
- `**/`：匹配任何嵌套到任何级别的目录

如果在全局匹配模式中未指定文件扩展名，则只能包含受支持的扩展名的文件（例如：默认的 `.ts`、`.tsx` 和 `.d`。如果 `allowJs` 设置为 `true`，则默认为 `.js` 和 `.jsx`）。

```json
// 默认匹配.ts、.tsx和.d文件
"include": [
    "./src", 		// 匹配src目录及其后代目录下的所有文件
    "./src/*", 		// 匹配src目录下的所有文件，不含其后代
    "./src/**/*" 	// 与"./src"一样
]
```

更多参考：[`include` 配置](https://www.typescriptlang.org/tsconfig#include) 

##### `exclude`

`exclude` 用于指定不需要被编译的文件或目录（包括其中的文件和所有后代文件）。

它的语法与 `include` 相似。通常用来从 `include` 中排除不需要被编译的文件或目录。

更多参考：[`exclude` 配置](https://www.typescriptlang.org/tsconfig#exclude) 

##### 初始配置

执行 `tsc --init` 指令，会在控制台提示 `TS` 的初始化配置，并将这些初始化配置加到生成的 `tsconfig.json` 配置文件中。

```shell
Created a new tsconfig.json with:

  target: es2016
  module: commonjs
  strict: true
  esModuleInterop: true
  skipLibCheck: true
  forceConsistentCasingInFileNames: true


You can learn more at https://aka.ms/tsconfig
```

#### 编译配置

在初始化的 `tsconfig.json` 中，默认只有一个根节点 `"compilerOptions"`，即：`TS` 的编译器配置。

```json
{
    "compilerOptions": {...}
}
```

以下列出部分的编译器配置，所有配置请参考：[编译配置](https://www.tslang.cn/docs/handbook/compiler-options.html)、[`MSBuild` 中的编译配置](https://www.typescriptlang.org/docs/handbook/compiler-options-in-msbuild.html) 【`MSBuild`：`Microsoft Build Engine`】

##### 目标版本

在编译器的配置中，有一个 `"target"` 根节点。它用于配置将 `ts` 文件编译成指定 `ES` 版本的 `js` 文件，值为 `ES` 版本。

```json
"target": "es2016" // 编译为：ES5版本的js文件，并使它尽量向下（向后）兼容之前的版本。
```

```tsx
let a: number = 0; 		// 编译结果：var a = 0;

const b: string = ''; 	// 编译结果：var b = '';
```

##### 模块系统

`module` 用于为程序指定模块系统。在 `node` 项目中，很可能通常需要的是 `"CommonJS"`。

```json
"module": "commonjs" // 采用commonjs标准
```

更多参考：[`module` 配置](https://www.typescriptlang.org/tsconfig#module) 

##### 严格模式

```json
"strict": true // 启用所有严格的类型检查设置
```

##### 模块交互

```json
"esModuleInterop": true // ES模块之间的交互操作
```

开启 `"esModuleInterop"` 配置，将允许模块之间进行交互，如：在模块中导入另一个模块。它会生成额外的 `JavaScript` 以简化对导入 `CommonJS` 模块的支持，使得 `'allowSyntheticDefaultImports'` 类型兼容。

##### 声明检查

```tsx
"skipLibCheck": true // 跳过对Lib（即：编译过程中需要引入的库文件的列表）的检查
```

开启它，表示 —— 跳过对所有的声明文件（ `*.d.ts`）的类型检查。

这可以在编译期间节省时间，但代价是类型系统的准确性。例如，两个库可以以不一致的方式定义同一类型的两个副本。`TypeScript` 不会对所有 `d.ts` 文件进行全面检查，而是会对你在应用源代码中特别引用的代码进行类型检查。

在以下情况中，你应该开启它：

- 在 `node_modules` 中有两个库类型的副本时，您可能会考虑使用 `skipLibCheck`。在这些情况下，您应该考虑使用像 `yarn` 的解析这样的特性来确保您的树中只有一个依赖项副本，或者研究如何通过理解依赖项解析来确保只有一个副本，从而在不使用其他工具的情况下解决问题。
- 当你在 `TypeScript` 版本之间迁移时，这些更改会导致 `node_modules` 和 `JS` 标准库的破坏，而你不想在 `TypeScript` 更新期间处理这些破坏。

更多参考：[`Skip Lib Check`](https://www.typescriptlang.org/tsconfig/#skipLibCheck) 

##### 编译引入

`lib` 用于指定在编译过程中需要引入的库文件。【`lib`：`library`】

当在项目引入一些第三方库时，可能需要将其中一些库文件引入到编译过程中使用。

`TypeScript` 为内置的 `JS APIs`（如：`Math`）提供了一个默认的类型定义集，也为出现在浏览器环境中的东西（如：`document`）提供了类型定义。`TypeScript` 还为匹配你指定的目标版本中较新的 `JS` 特性提供了 `APIs`，例如：如果目标版本是 `ES6` 或更新的，则 `Map` 的定义是可用的。

然而，出于以下原因，你可能会想要改变这些。

- 你的程序不是在浏览器中运行的，所以你不需要 `"dom"` 的类型定义
- 运行时平台提供了某些 `JavaScript API` 对象（可能是通过 `polyfills`），但还不支持给定 `ECMAScript` 版本的完整语法
- 对于更高级别的 `ECMAScript` 版本，您有一些（但不是全部）`polyfills` 或原生实现

在 `TypeScript 4.5` 中，`lib` 文件会被 `npm` 模块覆盖，更多参考：[`Supporting lib from node_modules`](https://devblogs.microsoft.com/typescript/announcing-typescript-4-5-beta/#supporting-lib-from-node_modules)。

```json
"lib": [
    "esnext",
    "dom",
    "dom.iterable",
    "scripthost"
]
```

更多参考：[`lib` 配置](https://www.typescriptlang.org/tsconfig#lib) 

##### 导入 `JS`

`allowJs` 用于指定是否允许在项目中导入 `js` 文件。

默认情况下，在 `ts` 文件中导入 `js` 文件会导致报错。开启该配置，将会允许 `.ts` 和 `.tsx` 文件与现有的 `JavaScript` 文件共存。

```json
"allowJs": true
```

更多参考：[`allowJs` 配置](https://www.typescriptlang.org/tsconfig#allowJs) 

##### 编译输出

`outDir` 用于指定将编译文件输出到哪个目录。其值为输出目录的路径字符串。

默认情况下，编译后的 `js` 文件将会被发送到在生成它的 `ts` 文件的同一目录。

```json
"outDir": "dist" // 将js文件输出到dist目录中
```

更多参考：[`outDir` 配置](https://www.typescriptlang.org/tsconfig#outDir) 


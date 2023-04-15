# Navigator

​		`navigator` 是由 `Netscape Navigator 2` 最早引入浏览器的，现在已经成为客户端标识浏览器的标准。只要浏览器启用 `JavaScript`，`navigator` 对象就一定存在。但是与其他 `BOM` 对象一样，每个浏览器都支持自己的属性。

注释：在 `Chrome` 中，`clientInformation` 与 `navigator` 是同一个对象，即：`navigator === clientInformation;`。

更多参考：[`Navigator MDN`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator) 

注意：`navigator` 对象中关于系统能力的属性将在《客户端检测》中详细介绍。

​		`navigator` 对象实现了 `NavigatorID` 、 `NavigatorLanguage` 、 `NavigatorOnLine` 、`NavigatorContentUtils` 、 `NavigatorStorage` 、 `NavigatorStorageUtils` 、 `NavigatorConcurrentHardware`、`NavigatorPlugins` 和 `NavigatorUserMedia` 接口定义的属性和方法。

​		下表列出了这些接口定义的属性和方法：

|            属性/方法            |                             说明                             |
| :-----------------------------: | :----------------------------------------------------------: |
|       `activeVrDisplays`        | 返回数组，包含 `ispresenting` 属性为 `true` 的 `VRDisplay` 实例 |
|          `appCodeName`          |       即使在非 `Mozilla` 浏览器中也会返回 `"Mozilla"`        |
|            `appName`            |                          浏览器全名                          |
|          `appVersion`           |           浏览器版本。通常与实际的浏览器版本不一致           |
|            `battery`            |    返回暴露 `Battery Status API` 的 `BatteryManager` 对象    |
|            `buildId`            |                       浏览器的构建编号                       |
|          `connection`           | 返回暴露 `Network Information API` 的 `NetworkInformation` 对象 |
|         `cookieEnabled`         |             返回布尔值，表示是否启用了 `cookie`              |
|          `credentials`          | 返回暴露 `Credentials Management API` 的 `CredentialsContainer` 对象 |
|         `deviceMemory`          |                返回单位为 `GB` 的设备内存容量                |
|          `doNotTrack`           |          返回用户的 “不跟踪”（`do-not-track`）设置           |
|          `geolocation`          |       返回暴露 `Geolocation API` 的 `Geolocation` 对象       |
|        `getVRDisplays()`        |          返回数组，包含可用的每个 `VRDisplay` 实例           |
|        `getUserMedia()`         |                返回与可用媒体设备硬件关联的流                |
|      `hardwareConcurrency`      |                   返回设备的处理器核心数量                   |
|          `javaEnabled`          |           返回布尔值，表示浏览器是否启用了 `Java`            |
|           `language`            |                      返回浏览器的主语言                      |
|           `languages`           |                   返回浏览器偏好的语言数组                   |
|             `locks`             |        返回暴露 `Web Locks API` 的 `LockManager` 对象        |
|       `mediaCapabilities`       | 返回暴露 `Media Capabilities API` 的 `MediaCapabilities` 对象 |
|         `mediaDevices`          |                      返回可用的媒体设备                      |
|        `maxTouchPoints`         |                返回设备触摸屏支持的最大触点数                |
|           `mimeTypes`           |              返回浏览器中注册的 `MIME` 类型数组              |
|            `onLine`             |                返回布尔值，表示浏览器是否联网                |
|             `oscpu`             |          返回浏览器运行设备的操作系统和（或）`CPU`           |
|          `permissions`          |       返回暴露 `Permissions API` 的 `Permissions` 对象       |
|           `platform`            |                   返回浏览器运行的系统平台                   |
|            `plugins`            | 返回浏览器安装的插件数组。在 `IE` 中，这个数组包含页面中所有 `<embed>` 元素 |
|            `product`            |               返回产品名称（通常是 `"Gecko"`）               |
|          `productSub`           |         返回产品的额外信息（通常是 `Gecko` 的版本）          |
|   `registerProtocolHandler()`   |              将一个网站注册为特定协议的处理程序              |
| `requestMediaKeySystemAccess()` |       返回一个期约，解决为 `MediaKeySystemAccess` 对象       |
|         `sendBeacon()`          |                      异步传输一些小数据                      |
|         `serviceWorker`         | 返回用来与 `ServiceWorker` 实例交互的 `ServiceWorkerContainer` |
|            `share()`            |                  返回当前平台的原生共享机制                  |
|            `storage`            |       返回暴露 `Storage API` 的 `StorageManager` 对象        |
|           `userAgent`           |                  返回浏览器的用户代理字符串                  |
|            `vendor`             |                     返回浏览器的厂商名称                     |
|           `vendorSub`           |                   返回浏览器厂商的更多信息                   |
|           `vibrate()`           |                         触发设备振动                         |
|           `webdriver`           |              返回浏览器当前是否被自动化程序控制              |

​		`navigator` 对象的属性通常用于确定浏览器的类型。

```js
navigator;
/*
Navigator {
	appCodeName: "Mozilla",
	appName: "Netscape",
	appVersion: "5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36",
	bluetooth: Bluetooth {},
	clipboard: Clipboard {},
	connection: NetworkInformation {onchange: null, effectiveType: '4g', rtt: 150, downlink: 1.55, saveData: false},
	cookieEnabled: true,
	credentials: CredentialsContainer {},
	deviceMemory: 8
	doNotTrack: null
	geolocation: Geolocation {}
	hardwareConcurrency: 12,
	hid: HID {onconnect: null, ondisconnect: null},
	ink: Ink {},
	keyboard: Keyboard {},
	language: "zh-CN",
	languages: (2) ['zh-CN', 'zh'],
	locks: LockManager {},
	managed: NavigatorManagedData {onmanagedconfigurationchange: null},
	maxTouchPoints: 0,
	mediaCapabilities: MediaCapabilities {},
	mediaDevices: MediaDevices {ondevicechange: null},
	mediaSession: MediaSession {metadata: null, playbackState: 'none'},
	mimeTypes: MimeTypeArray {0: MimeType, 1: MimeType, application/pdf: MimeType, text/pdf: MimeType, length: 2},
	onLine: true,
	pdfViewerEnabled: true,
	permissions: Permissions {},
	platform: "Win32",
	plugins: PluginArray {0: Plugin, 1: Plugin, 2: Plugin, 3: Plugin, 4: Plugin, PDF Viewer: Plugin, Chrome PDF Viewer: Plugin, Chromium PDF Viewer: Plugin, Microsoft Edge PDF Viewer: Plugin, WebKit built-in PDF: Plugin, ...},
	presentation: Presentation {defaultRequest: null, receiver: null},
	product: "Gecko",
	productSub: "20030107",
	scheduling: Scheduling {},
	serial: Serial {onconnect: null, ondisconnect: null},
	serviceWorker: ServiceWorkerContainer {controller: null, ready: Promise, oncontrollerchange: null, onmessage: null, onmessageerror: null},
	storage: StorageManager {},
	usb: USB {onconnect: null, ondisconnect: null},
	userActivation: UserActivation {hasBeenActive: true, isActive: true},
	userAgent: "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.0.0 Safari/537.36",
	userAgentData: NavigatorUAData {brands: Array(3), mobile: false, platform: 'Windows'},
	vendor: "Google Inc.",
	vendorSub: "",
	virtualKeyboard: VirtualKeyboard {boundingRect: DOMRect, overlaysContent: false, ongeometrychange: null},
	wakeLock: WakeLock {},
	webdriver: false,
	webkitPersistentStorage: DeprecatedStorageQuota {},
	webkitTemporaryStorage: DeprecatedStorageQuota {},
	windowControlsOverlay: WindowControlsOverlay {visible: false, ongeometrychange: null},
	xr: XRSystem {ondevicechange: null},
	[[Prototype]]: Navigator
}
*/
```



### 检测插件

​		检测浏览器是否安装了某个插件是开发中常见的需求。除 `IE10` 及更低版本外的浏览器，都可以通过 `plugins` 数组来确定。这个数组中的每一项都包含如下基本属性。

- `name`：插件名称
- `description`：插件介绍
- `filename`：插件的文件名
- `length`：由当前插件处理的 `MIME` 类型数量。

​		通常，`name` 属性包含识别插件所需的必要信息，尽管不是特别准确。

```js
plugins: PluginArray {
	0: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', description: 'Portable Document Format',...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...}, 
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...}, 
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format', 
        length: 2
    },
	1: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', description: 'Portable Document Format',...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'Chrome PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format', 
        length: 2
    },
	2: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', description: 'Portable Document Format',...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'Chromium PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format',
        length: 2
    },
	3: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', description: 'Portable Document Format',...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'Microsoft Edge PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format',
        length: 2
    },
	4: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', description: 'Portable Document Format',...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'WebKit built-in PDF', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format',
        length: 2
    },
	Chrome PDF Viewer: Plugin {
        0: {type: 'application/pdf', suffixes: 'pdf', ...},
        1: {type: 'text/pdf', suffixes: 'pdf', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...}, 
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'Chrome PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format',
        length: 2
    },
	Chromium PDF Viewer: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...}, 
        name: 'Chromium PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format', 
        length: 2
    },
	Microsoft Edge PDF Viewer: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'Microsoft Edge PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format',
        length: 2
    },
	PDF Viewer: Plugin {
        0:MimeType {type: 'application/pdf', suffixes: 'pdf', ...}, 
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'PDF Viewer', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format',
        length: 2
    },
	WebKit built-in PDF: Plugin {
        0: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        1: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
        text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', ...},
        name: 'WebKit built-in PDF', 
        filename: 'internal-pdf-viewer', 
        description: 'Portable Document Format',
        length: 2
    },
	length: 5,
	[[Prototype]]: PluginArray
}
```

​		检测插件就是遍历浏览器中可用的插件，并逐个比较插件的名称，如下所示：

```js
// 插件检测，IE10 及更低版本无效 
let hasPlugin = function(name) { 
 	name = name.toLowerCase();
    for (let plugin of window.navigator.plugins){
    	// 检查指定名称是否是某个插件名的片段
 		if (plugin.name.toLowerCase().indexOf(name) > -1){ 
 			return true; 
 		} 
 	} 
 	return false; 
}

// 检测 Flash 
console.log(hasPlugin("Flash")); // false
// 检测 PDF
console.log(hasPlugin("PDF")); // true
```

​		这个 `hasPlugin()` 方法接收一个参数，即待检测插件的名称。第一步是把插件名称转换为小写形式，以便于比较。然后，遍历 `plugins` 数组，通过 `indexOf()` 方法检测每个 `name` 属性，看传入的名称是不是存在于某个数组中。比较的字符串全部小写，可以避免大小写问题。传入的参数应该尽可能独一无二，以避免混淆。像 `"Flash"`、`"QuickTime"` 这样的字符串就可以避免混淆。这个方法可以在 `Firefox`、`Safari`、`Opera` 和 `Chrome` 中检测插件。

注意：`plugins` 数组（其实是一个 `PluginArray` 类数组）中的每个插件对象都是一个 `Plugin` 类数组，其元素都是 `MimeType` 对象。每个 `MimeType` 对象有 4 个属性： `description` 描述 `MIME` 类型，`enabledPlugin` 是指向插件对象的指针，`suffixes` 是该 `MIME` 类型对应扩展名的逗号分隔的字符串，`type` 是完整的 `MIME` 类型字符串。

```js
navigator.plugins;
/*
PluginArray {
	0: Plugin {
		0: MimeType {
			description: "Portable Document Format",
			enabledPlugin: Plugin {...}, // ==> 指回该插件对象本身
			suffixes: "pdf",
			type: "application/pdf"
		},
		...
	},
	...
}
*/

navigator.plugins[0] === navigator.plugins[0][0].enabledPlugin; // true
```

​		`IE11` 的 `window.navigator` 对象开始支持 `plugins` 和 `mimeTypes` 属性。这意味着前面定义的函数可以适用于所有较新版本的浏览器。而且，`IE11` 中的 `ActiveXObject` 也从 `DOM` 中隐身了，意味着不能再用它来作为检测特性的手段。

```js
navigator.mimeTypes;
/*
MimeTypeArray {
	0: MimeType {type: 'application/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...},
	1: MimeType {type: 'text/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...}, 
	application/pdf: MimeType {type: 'application/pdf', suffixes: 'pdf', ...},
	text/pdf: MimeType {type: 'text/pdf', suffixes: 'pdf', description: 'Portable Document Format', ...}, 
	length: 2
}
*/
```



#### 旧版本 `IE` 中的插件检测

​		`IE10` 及更低版本中检测插件的问题比较多，因为这些浏览器不支持 `Netscape` 式的插件。在这些 `IE` 中检测插件要使用专有的 `ActiveXObject`，并尝试实例化特定的插件。`IE` 中的插件是实现为 `COM` 对象的，由唯一的字符串标识。因此，要检测某个插件就必须知道其 `COM` 标识符。例如，`Flash` 的标识符是 `"ShockwaveFlash.ShockwaveFlash"`。知道了这个信息后，就可以像这样检测 `IE` 中是否安装了 `Flash`：

```js
// 在旧版本 IE 中检测插件
function hasIEPlugin(name) { 
 	try { 
 		new ActiveXObject(name); 
 		return true; 
 	} catch (e) { 
 		return false; 
 	} 
} 

// 通过COM标识符检测IE插件
// 检测 Flash 
console.log(hasIEPlugin("ShockwaveFlash.ShockwaveFlash")); 
// 检测 QuickTime 
console.log(hasIEPlugin("QuickTime.QuickTime"));
```

​		在这个例子中，`hasIEPlugin()` 函数接收一个 `COM` 标识符参数。为检测插件，这个函数会使用传入的标识符创建一个新 `ActiveXObject` 实例。相应代码封装在一个 `try/catch` 语句中，因此如果创建的插件不存在则会抛出错误。如果创建成功则返回 `true`，如果失败则在 `catch` 块中返回 `false`。上面的例子还演示了如何检测 `Flash` 和 `QuickTime` 插件。

​		因为检测插件涉及两种方式，所以一般要针对特定插件写一个函数，而不是使用通常的检测函数。比如下面的例子：

```js
// 在所有浏览器中检测 Flash 
function hasFlash() { 
 	var result = hasPlugin("Flash"); 
 	if (!result){ 
 		result = hasIEPlugin("ShockwaveFlash.ShockwaveFlash"); 
 	} 
 	return result; 
}

// 在所有浏览器中检测 QuickTime 
function hasQuickTime() { 
 	var result = hasPlugin("QuickTime"); 
 	if (!result){ 
 		result = hasIEPlugin("QuickTime.QuickTime"); 
 	} 
 	return result; 
} 

// 检测 Flash 
console.log(hasFlash()); 
// 检测 QuickTime 
console.log(hasQuickTime());
```

​		以上代码定义了两个函数 `hasFlash()` 和 `hasQuickTime()`。每个函数都先尝试使用非 `IE` 插件检测方式，如果返回 `false`（对 `IE`可能会），则再使用 `IE` 插件检测方式。如果 `IE` 插件检测方式再返回 `false`，整个检测方法也返回 `false`。只要有一种方式返回 `true`，检测方法就会返回 `true`。

```js
// 封装整理
let hasPlugin = (function () {
    function others(name) {
        name = name.toLowerCase();
        for (const plugin of window.navigator.plugins) {
            if (plugin.name.toLowerCase().indexOf(name) > -1) {
                return true;
            }
        }
        return false;
    }
    function ie(COMId) {
        try {
            new ActiveXObject(COMId);
            return true;
        } catch (e) {
            return false;
        }
    }
    return function (name, COMId) {
        let result = others(name);
        if (!result) {
            result = ie(COMId);
        }
        return result;
    }
})();
```

注意：`plugins` 有一个 `refresh()` 方法，用于刷新 `plugins` 属性以反映新安装的插件。这个方法接收一个布尔值参数，表示刷新时是否重新加载页面。如果传入 `true`，则**所有包含插件的页面都会重新加载**。否则，只有 `plugins` 会更新，但页面不会重新加载。



### 注册处理程序

​		现代浏览器支持 `navigator` 上的（在 `HTML5` 中定义的）`registerProtocolHandler()` 方法。这个方法可以把一个网站注册为一个专门处理某种特定类型信息的应用程序。随着在线 `RSS` 阅读器和电子邮件客户端的流行，可以借助这个方法将 `Web` 应用程序注册为像桌面软件一样的默认应用程序。

​		要使用 `registerProtocolHandler()` 方法，必须传入 3 个参数：要处理的协议（如 `"mailto"` 或 `"ftp"`）、处理该协议的 `URL`，以及应用名称。比如，要把一个 `Web` 应用程序注册为默认邮件客户端，可以这样做：

```js
navigator.registerProtocolHandler("mailto", 
 								  "http://www.somemailclient.com?cmd=%s", 
 								  "Some Mail Client");

// Uncaught DOMException: Failed to execute 'registerProtocolHandler' on 'Navigator': The scheme of the url provided must be HTTP(S).
```

​		这个例子为 `"mailto"` 协议注册了一个处理程序，这样邮件地址就可以通过指定的 `Web` 应用程序打开。注意，第二个参数是负责处理请求的 `URL`，`%s` 表示原始的请求。


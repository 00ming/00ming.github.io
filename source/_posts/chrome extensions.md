---
title: chrome extensions
date: 2018-10-18 17:12:44
tags: chrome extensions
---
# chrome extensions
## [概观](https://developer.chrome.com/extensions/overview)
扩展程序是HTML，CSS，JavaScript，图像以及Web平台中使用的其他文件的压缩包，可自定义Google Chrome浏览体验。扩展是使用Web技术构建的，可以使用浏览器提供给开放Web的相同API。
## 清单
每一个chrome插件都有一个清单文件 `manifest.json`,
```
{
    "manifest_version": 2,                        // manifest编写规范版本，目前主流2
    "name": "My Extension",                        // 插件名
    "version": "versionString",                    // 版本号
}
```
这里是最基本的信息 有了这些信息就算是一个插件了,
别的配置可以需配置,下面会介绍一些比较常用的配置
<!-- more -->
### 图标
```
"browser_action": { // 图标显示在地址栏右边，能在所有页面显示
  "default_icon": { // 图标
    "19": "images/icon.png", // 不同的size
    "38": "images/icon.png" 
  },
  "default_title": "My Extension", // hover时展示的文字
  "default_popup": "popup.html" // 点击时的弹窗
},
```
举个栗子 ↓

![image](http://pbyegcye6.bkt.clouddn.com/popup.png)
* 箭头所指就是`default_icon`对应的图片
* 下面弹窗就是`default_popup` 所对应的页面
* 其中`default_popup`对应的HTML文件可以引入需要的js文件和CSS文件

### 页面脚本
嵌入页面的js脚本 ↓
```js
"content_scripts":[
  {
    "matches": ["http://*/*", "https://*/*"], // 生效的地址
    // "matches": ["<all_urls>"] "<all_urls>" 表示匹配所有地址
    // 多个JS按顺序注入
    "js":[
    "jquery.js",
    "content_scripts.js"
    ],
    "css": ["css/index.css"], // 注入css
    // 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle
    "run_at": "document_start"
  }
]
```
如果我做的是百度搜索的插件 可以修改`matches`的值为`["https://www.baidu.com/"]`,这样这个注入的脚本就只会在你打开`www.baidu.com`的时候运行
### 常驻后台js或者后台页面
```js
"background": {
  "scripts": [
  // 2种指定方式，如果指定JS，那么会自动生成一个背景页
  //"page": "background.html"
  "scripts"["background.js"]
  ]
},
```
`background`的页面也可以通过chrome的开发这工具进行调试,方法如下:

点击浏览器右侧的3个点 -> 更多工具 -> 扩展程序
点击背景页

![image](http://pbyegcye6.bkt.clouddn.com/chrome-extenison-background.png)
### 权限
```js
"permissions": [
  "tabs", //标签
  "contextMenus", // 右键菜单
  "notifications", // 通知
  ...
]
```
## 消息传递
`background`、`content_scripts`和`popup`都是独立的页面,通过使用消息传递机制进行通信，任何一方可以收到另一方传递来的消息，并且在相同的通道上答复。这个消息可以包含 任何一个有效的JSON对象(null, boolean, number, string, array, or object)。他们之间通过` runtime.sendMessage`或者` tabs.sendMessage `和`runtime.connect`或者` tabs.connect`来实现通信.
### 简单请求
从内容脚本发送请求如下所示：
```js
chrome.runtime.sendMessage({greeting: "hello"}, function(response) {
  console.log(response.farewell);
});
```
将扩展中的请求发送到内容脚本看起来非常相似，只是您需要指定将其发送到哪个选项卡。此示例演示如何向所选选项卡中的内容脚本发送消息。
```js
chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
  chrome.tabs.sendMessage(tabs[0].id, {greeting: "hello"}, function(response) {
    console.log(response.farewell);
  });
});
```
在接收端，您需要设置 `runtime.onMessage` 事件侦听器来处理该消息。内容脚本或扩展页面看起来相同。
```js
chrome.runtime.onMessage.addListener(
  function(request, sender, sendResponse) {
    console.log(sender.tab ?
                "from a content script:" + sender.tab.url :
                "from the extension");
    if (request.greeting == "hello")
      sendResponse({farewell: "goodbye"});
  });
```
在上面的例子中，`sendResponse`被同步调用。如果要异步使用`sendResponse`，请添加 `return true;`到`onMessage`事件处理程序。

### 长连接
有时，让对话持续时间超过单个请求和响应是有用的。在这种情况下，您可以分别使用`runtime.connect` 或`tabs.connect`打开从内容脚本到扩展页面的长期通道，反之亦然。通道可以选择使用名称，以便区分不同类型的连接。

一个用例可能是自动表单填写扩展。内容脚本可以打开特定登录的扩展页面的通道，并向页面上的每个输入元素的扩展发送消息以请求填写表单数据。共享连接允许扩展保持共享状态链接来自内容脚本的几条消息。

建立连接时，每个端都有一个` runtime.Port` 对象，用于通过该连接发送和接收消息。

以下是如何从内容脚本打开频道，以及发送和侦听消息的方式：
```js
var port = chrome.runtime.connect({name: "knockknock"});
port.postMessage({joke: "Knock knock"});
port.onMessage.addListener(function(msg) {
  if (msg.question == "Who's there?")
    port.postMessage({answer: "Madame"});
  else if (msg.question == "Madame who?")
    port.postMessage({answer: "Madame... Bovary"});
});
```
将扩展中的请求发送到内容脚本看起来非常相似，只是您需要指定要连接的选项卡。只需用`tabs.connect`替换上面示例中的connect连接。

为了处理传入连接，您需要设置` runtime.onConnect` 事件侦听器。这从内容脚本或扩展页面看起来是相同的。当扩展的另一部分调用“connect（）”时，将触发此事件以及 可用于通过连接发送和接收消息的` runtime.Port`对象。以下是响应传入连接的样子：
```js
chrome.runtime.onConnect.addListener(function(port) {
  console.assert(port.name == "knockknock");
  port.onMessage.addListener(function(msg) {
    if (msg.joke == "Knock knock")
      port.postMessage({question: "Who's there?"});
    else if (msg.answer == "Madame")
      port.postMessage({question: "Madame who?"});
    else if (msg.answer == "Madame... Bovary")
      port.postMessage({question: "I don't get it."});
  });
});
```
## 小结
开发了一个简单的扩展程序也踩了几个坑,谷歌官网的文档也很详细就是全英文吭起来比较辛苦.扩展程序有2个重点,一个是配置文件,另一个就是通信.解决了这两个问题debug和权限都很好解决,扩展程序的强大与否那就看开发者的编码能力了.

## 参考内容
- [谷歌浏览器扩展程序manifest.json参数详解 - plugnt - 博客园](https://www.cnblogs.com/dreamman/p/9139080.html)
- [ChromeExtension入门浅谈 - Dulk - 博客园](https://www.cnblogs.com/deng-cc/p/9053539.html)
- [What are extensions? - Google Chrome](https://developer.chrome.com/extensions) 
- [消息传递--扩展开发文档](http://open.chrome.360.cn/extension_dev/messaging.html#external) 360浏览器用的是chrome内核,以上面chrome官方文档为准
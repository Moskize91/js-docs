---
description: 引导你正确安装 White SDK
---

# 安装

本章节将引导你将 White SDK 安装到你的项目中。在此之前，你需要根据自己团队的技术栈选择合适的方式来安装。

## 安装 White SDK

你可以通过包管理工具（如 npm 和 yarn）安装 White SDK，或者通过直接引入 JS 文件的方式安装。

### 使用 npm 或 yarn 安装

npm 和 yarn 是 JavaScript 社区知名的包管理工具。如果你的团队刚好使用它们之一，你可以非常方便地利用它们安装 White SDK。如果你不了解什么是包管理工具，可以阅读如下两篇文章来了解。

* [《About npm》](https://docs.npmjs.com/about-npm/)
* [《Introducation \| yarn》](https://yarnpkg.com/getting-started)

你可以使用如下命令安装 `white-web-sdk`。

{% tabs %}
{% tab title="npm" %}
```bash
$ npm install white-web-sdk
```
{% endtab %}

{% tab title="yarn" %}
```bash
$ yarn add white-web-sdk
```
{% endtab %}
{% endtabs %}

如果你使用 React 开发 Web 应用，你可以使用如下命令安装 `white-react-sdk`。该 SDK 是 `white-web-sdk` 的超集，提供了可供 React 直接使用的组建，同时可以完全代替 `white-web-sdk`。

{% tabs %}
{% tab title="npm" %}
```bash
$ npm install white-react-sdk
```
{% endtab %}

{% tab title="yarn" %}
```bash
$ yarn add white-react-sdk
```
{% endtab %}
{% endtabs %}

### 直接引入 JS 来安装

如果你的团队基于某种原因，决定不使用任何包管理工具。或你的团队为了优化 JS 文件大小或页面加载速度，决定仅在需要使用白板的页面中使用白板 SDK。此时，你可以通过在 `<head/>` 中直接引入 JS 文件的 URL 来安装 Netless 互动白板 SDK。

你需要在你的 `html` 文件中的 `<head>` 中插入如下代码。

```markup
<head>
    <script src="https://sdk.herewhite.com/white-web-sdk/2.9.0.js"></script>
</head>
```

## 在你的项目引用 White SDK

如果你使用了 `webpack` 之类的打包工具，并配合 npm、yarn 之类的包管理工具安装了互动白板 SDK。你可以通过如下方式引用 SDK。

在进阶教程接下来的篇章中，我们将默认你是用这种模式引用 SDK 的。

{% tabs %}
{% tab title="JavaScript" %}
```javascript
var WhiteWebSdk = require("white-web-sdk");
```
{% endtab %}

{% tab title="ES 6" %}
```javascript
import WhiteWebSdk from "white-web-sdk";
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
import * as WhiteWebSdk from "white-web-sdk";
```
{% endtab %}
{% endtabs %}

如果你使用在 `<head>` 中直接引用 JS 文件的 URL 的方式安装 SDK。则 SDK 的 JS 文件在执行完之后，会在 `window` 对象中添加一个 `WhiteWebSdk` 的全局变量。你可以通过如下代码取到该全局变量。

```javascript
var WhiteWebSdk = window.WhiteWebSdk;
```

{% hint style="warning" %}
`<script>` 标签在 `<head>` 中的顺序很重要。请确保 `white-web-sdk`的 JS 文件的标签在你的业务代码之前。这样，你的业务代码才能顺利获取全局变量 `WhiteWebSdk`。
{% endhint %}

## 引入 CSS

Netless 互动白板 SDK 在前端有自己的样式。只有正确引入项目中，才能让互动白板以正确的样子显示出来。

你需要根据你的项目打包、发布方案，来决定以何种方式引入 Netless 互动白板的 CSS。

### 项目使用 webpack 打包

如果你的项目使用 `webpack` 打包，我们推荐你 `css-loader` 来引入互动白板的 CSS。如果你对此不太了解，可以阅读[《css-loader \| webpack》](https://webpack.js.org/loaders/css-loader/)。

你可以直接在 \*.js 文件（如果你使用 TypeScript，则是 \*.ts 文件）中直接引入。

{% tabs %}
{% tab title="JavaScript" %}
```javascript
require("white-web-sdk/style/index.css");
```
{% endtab %}

{% tab title="ES 6" %}
```javascript
import "white-web-sdk/style/index.css";
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
import "white-web-sdk/style/index.css";
```
{% endtab %}
{% endtabs %}

注意，请确保这行代码所在的文件会被 `webpack` 打包到目标文件。如果做不到，可能导致 CSS 引入失败，最终互动白板的样式无法正常显示。为了避免这种情况，**推荐将这行代码写到 entry 所指示的文件中**。

如果你的项目也有样式文件（如 css、less、sass 等），可以在样式文件中加入这么一行来引入。

```css
@import "white-web-sdk/style/index.css";
```

如上这两种方案二选一，仅选择适合你的项目的方案即可。

### 直接引入 css 文件

你也可以在 HTML 文件中的 `<head>` 中插入如下内容，以此引入 CSS 样式文件。

```markup
<head>
    <link rel="stylesheet" href="https://sdk.herewhite.com/white-web-sdk/2.9.0.css">
</head>
```

{% hint style="info" %}
如果你的互动白板样子看起来比较奇怪，你可以首先怀疑是否 CSS 文件没有引入，或引入失败。
{% endhint %}

## 使用枚举类型

继续阅读《进阶教程》后你会发现，SDK 中普遍使用枚举类型。你可以使用如下方式引用枚举类型（例如，你需要引用 `RoomPhase` 这个枚举类型）。

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import { RoomPhase } from "white-web-sdk";
console.log(RoomPhase.Connected);
```
{% endtab %}

{% tab title="ES 6" %}
```javascript
import { RoomPhase } from "white-web-sdk";
console.log(RoomPhase.Connected);
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
import { RoomPhase } from "white-web-sdk";
console.log(RoomPhase.Connected);
```
{% endtab %}
{% endtabs %}

在 `white-web-sdk` 中所用到的枚举类型本质上是 `string` 。例如，`RoomPhase.Connected` 可以使用字符串 `"connected"` 代替。实际上，所有的枚举类型都可以用它的小驼峰命名法的字符串代替。

> 如果你不了解小驼峰命名法则，可以阅读[《驼峰式大小写](https://zh.wikipedia.org/wiki/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)》。

我们非常不推荐你使用字符串代替枚举。使用字符串的开发难度较高，倘若不小心拼错了单词，将会耗费你不少时间 debug。使用枚举有助于你的 IDE 和编译器直接拦截掉拼写错误。

{% hint style="info" %}
如果你用在 `<head>` 中插入 `<script>` 的方式安装 SDK，你将无法使用枚举类型。此时，你不得不用字符串代替枚举类型。
{% endhint %}


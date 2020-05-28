---
description: 引导你正确安装互动白板 SDK
---

# 安装

本章将介绍若干种安装 White SDK 的方法。你可以根据自己团队的技术栈来选择适合的方法。

## 安装 White SDK

可以用包管理工具（如 npm 和 yarn）来安装 White SDK，或者直接引入 JS 文件。

### 使用 npm 或 yarn 来安装

npm 和 yarn 是 JavaScript 社区知名的包管理工具。如果你的刚好使用了它们之一，就可以直接用它们来安装 White SDK。如果你不知道什么是包管理工具，可以先阅读下面这两篇文章。

* [《About npm》](https://docs.npmjs.com/about-npm/)
* [《Introducation \| yarn》](https://yarnpkg.com/getting-started)

使用如下命令安装 `white-web-sdk`。

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

如果你用 React 开发 Web 应用，可以安装 `white-react-sdk`。该库是 `white-web-sdk` 的超集，既提供了可供 React 直接使用的组件，又可以完全代替 `white-web-sdk`。

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

你可能决定不用任何包管理工具来安装。比如，为了减少 JS 文件的大小，以优化页面加载速度，决定仅在需要使用白板的页面中使用 White SDK。那么，也可以通过在 `<head>` 中直接引入 JS 文件的 URL 来安装 White SDK。

在的 `html` 文件中的 `<head>` 中插入如下代码即可。

```markup
<head>
    <script src="https://sdk.herewhite.com/white-web-sdk/2.9.0.js"></script>
</head>
```

## 在项目中引入 White SDK

如果用了 `webpack` 之类的打包工具，并用 npm、yarn 之类的包管理工具安装 White SDK，就可以通过如下方式引入项目。

在进阶教程接之后的内容中，我们将默认你用这种方式来引入项目。

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

如果用了在 `<head>` 中直接引用 JS 文件的 URL 的方式来安装 White SDK，则可以用如下代码取到名为 `WhiteWebSdk` 的全局变量。

```javascript
var WhiteWebSdk = window.WhiteWebSdk;
```

{% hint style="warning" %}
`<script>` 标签在 `<head>` 中的排列顺序很重要。请确保 `white-web-sdk`的 JS 文件在你的业务代码的 JS 文件之前。只有这样，业务代码才能顺利获取全局变量 `WhiteWebSdk`。
{% endhint %}

## 引入 CSS

White SDK 有自己的样式。只有将样式成功引入到项目中，才能让互动白板正确显示。你需要根据项目的打包、发布方案，来决定以什么方式引入 White SDK 的样式。

### 用 webpack 打包项目

如果你已经用 `webpack` 打包项目，那么，推荐用 `css-loader` 引入互动白板的 CSS 样式文件。如果你不了解 `css-loader`，建议先阅读[《css-loader \| webpack》](https://webpack.js.org/loaders/css-loader/)。

在 \*.js 文件（如果你用的是 TypeScript，则是 \*.ts 文件）里写下如下代码，直接引入样式文件。

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

注意，请确保 `webpack` 会打包这行代码所在的文件。若做不到，CSS 文件的引入便失败了，如此一来，互动白板将无法正常显示样式。为了避免这种情况，**推荐将这行代码写到 entry 所指示的文件中**。

如果你的项目也用到了样式文件（如 css、less、sass 等），也可以在你自己的样式文件中加入如下代码。这等效于上面所说的方式，也能引入互动白板的样式文件。

```css
@import "white-web-sdk/style/index.css";
```

根据你项目的具体情况，二选一即可。

### 直接引入 CSS 文件

在 HTML 文件的 `<head>` 中插入如下内容，也能引入互动白板的样式文件。

```markup
<head>
    <link rel="stylesheet" href="https://sdk.herewhite.com/white-web-sdk/2.9.0.css">
</head>
```

{% hint style="info" %}
如果互动白板的样子看起来比较奇怪，应该确认 CSS 文件是否引入成功。
{% endhint %}

## 使用枚举类型

White SDK 的 API 普遍用到枚举类型。例如，你可以通过如下代码引用 `RoomPhase` 这个枚举类型。

{% tabs %}
{% tab title="JavaScript" %}
```javascript
var RoomPhase = require("white-web-sdk").RoomPhase;
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

`white-web-sdk` 中，所有枚举类型本质上是 `string` 。举个例子，`RoomPhase.Connected` 的值就是字符串 `"connected"`。这个例子可以推而广之，其实所有枚举类型都可以用它的小驼峰命名法的字符串代替。

> 如果不了解小驼峰命名法则，可以先阅读[《驼峰式大小写](https://zh.wikipedia.org/wiki/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)》。

我们建议不要用字符串代替枚举。这种方式开发难度很高，如果不小心拼错了单词，出了 bug，也很难定位。使用枚举的话，IDE 和编译器可以直接拦截掉拼写错误。

{% hint style="info" %}
如果用在 `<head>` 中插入 `<script>` 的方式来安装 White SDK，则无法使用枚举类型。此时，你不得不用字符串代替枚举类型。
{% endhint %}


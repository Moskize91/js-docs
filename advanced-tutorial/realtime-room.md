---
description: 创建、加入一个可以实时互动的白板
---

# 实时房间

每一个白板都属于一个房间。某一个人在白板上写写画画的轨迹可以被同一个房间里的其他人看到。本章教你如何创建一个可以实时互动的房间，并管理房间的状态。

{% hint style="info" %}
本章教程只会让你把房间状态管理的相关内容涉猎一遍。如果你想深入了解相关内容，可以在阅读完本章后参考[《实时房间状态管理》](https://developer.netless.group/documents/client/realtime-room-state-management)。
{% endhint %}

## 创建房间

Netless 互动白板的一切都基于房间，只有创建了房间，你才能开始互动白板之旅。为了创建一个房间，你需要准备一个 App Identifier。此物用来表明这个房间是归哪个应用所有（应用从管理控制台创建）。有了它，我们就知道是你的企业账号要创建房间。此后，该房间产生的一切费用也将关联到你的企业账号。

此外，你还需在应用下签出 SDK Token，以标示你的企业账号的授权。我们的服务端会通过 SDK Token 确认操作是合法的。

你可以阅读[《应用与权限》](https://developer.netless.group/documents/guan-li-kong-zhi-tai/applications-and-authority)来了了解如何得到 App Identifier 和 SDK Token。然后，通过如下代码，来调用我们的服务端 API 以创建房间。

```javascript
var url = "https://shunt-api.netless.link/v5/rooms";
var requestInit = {
    method: "POST",
    headers: {
        "content-type": "application/json",
        "token": sdkToken, // 签发的 SDK Token，需提前准备
    },
};

window.fetch(url, requestInit).then(function(response) {
    return response.json();

}).then(function(roomJSON) {
    // 创建房间成功，获取描述房间信息的 roomJSON
    console.log(roomJSON);

}).catch(function(err) {
    // 失败了，打印出 Error 以便分析为何失败
    console.error(err);
});
```

这段如果执行成功，将创建一个实时互动房间。我们的服务端将以 JSON 的方式返回一个 object，以描述你刚刚创建好的房间信息。不出所料，这个 JSON 会包含如下信息。

```javascript
{
    "uuid": "dcfc7fb09f6511eabc8da545523f6422",
    "name": "",
    "teamUUID": "34YtcH_MEeqFMjt5vcNozQ",
    "isRecord": false,
    "isBan": false,
    "createdAt": "2020-05-26T15:30:43.706Z",
    "limit": 0
}
```

其中，最重要的字段是 `uuid`。

{% hint style="warning" %}
创建房间的操作建议在你的业务服务端上做，不要放在前端或客户端上做。此处为了完整演示，使用前端的 window.fetch 直接调用 Netless 服务端 API。请勿在正式的 Web 应用中模仿。
{% endhint %}

{% hint style="warning" %}
SDK Token 是你的公司和团队的重要资产，原则上它只能在业务服务器中产生并使用。**绝对不能写死在前端、客户端应用中！绝对不要通过网络传输给前端、客户端！**以防止他人通过反编译、抓包等途径窃取 SDK Token。一旦 SDK Token 泄漏，将带来严重的安全问题。
{% endhint %}

## 房间的标示与鉴权

你的账号可以创建无数个房间。每一个房间都有 `uuid` ，这是一个用于唯一标示的字符串。此外，如果要访问特定房间，需要签出对应房间的 Room Token。

你可以调用服务端 API，来基于 SDK Token 生成 Room Token。

```javascript
var uuid = "特定房间的 uuid";
var url = "https://shunt-api.netless.link/v5/tokens/rooms/" + uuid;
var requestInit = {
    method: "POST",
    headers: {
        "content-type": "application/json",
        "token": sdkToken, // 签发的 SDK Token，需提前准备
    },
    body: JSON.stringify({
        "lifespan": 0, // 表明 Room Token 永不失效
        "role": "admin", // 表明 Room Token 有 Admin 的权限
    }),
};
fetch(url, requestInit).then(function(response) {
    return response.json();

}).then(function(roomToken) {
    // 成功获取房间的 Room Token
    joinRoom(roomUUID, roomToken);

}).catch(function(err) {
    console.error(err);
});
```

Room Token 只能访问特定的房间，权限比 SDK Token 弱，因此可以根据业务逻辑分发给特定客户端。

{% hint style="warning" %}
签出 Room Token 需要调用 Netless 服务端 API。出于安全考虑，该操作不要在前端做。当前端需要 Room Token 时，应该调用业务服务器的 API，再由业务服务器调用 Netless 服务端的 API 签出 Room Token。以避免 SDK Token 泄漏到前端。
{% endhint %}

## 加入房间

当你创建了一个实时互动房间，并获取了它的 `uuid` 和 `roomToken` 之后，你就可以在前端加入房间了。

> 思考一下，uuid 和 roomToken 应该如何传递给前端。这往往取决于你的业务逻辑，你可以和团队中的产品经理交流，以设计一个合理的方式。
>
> 例如，先请求业务服务器的 API 读取房间列表（每一项中包含房间的 `uuid`）。当点击其中某一个房间时，读取项中的 `uuid` 。并发起一个 fetch 请求调用你的业务服务器 API，并在业务服务器使用 SDK Token 签出 Room Token。

在加入房间之前，你需要创建 `WhiteWebSDK` 实例。

```javascript
import { WhiteWebSDK } from "white-web-sdk";

var whiteWebSDK = new WhiteWebSDK({
    appIdentifier: appIdentify, // 从管理控制台获取 App Identifier
});
```

这个 `whiteWebSDK` 实例我们今后会多次用到。建议将其作为单例全局变量。然后，你可以通过如下代码加入房间。

```javascript
var joinRoomParams = {
    uuid: roomUUID,
    roomToken: roomToken,
};

whiteWebSdk.joinRoom(joinRoomParams).then(function(room) {
    // 加入房间成功，获取 room 对象

}).catch(function(err) {
    // 加入房间失败
    console.error(err);
});
```

加入房间成功后，我们拿到了 SDK 返回的 `room` 对象。这个对象很重要，所有涉及该房间的操作都基于该对象。

现在，我们希望在网页上将该白板展示出来。在此之前，你需要在网页的 Dom 树中准备白板占位符。

```markup
<div id="whiteboard" style="width: 100%; height: 100vh;"></div>
```

然后，通过如下代码，将白板展示在网页上。

```javascript
room.bindHtmlElement(document.getElementById("whiteboard"));
```

之后，你可以在网页上使用鼠标画出线条。致此，说明房间加入成功，白板也显示成功了。如果并非如此，你可能碰到了问题。

### 问题：我的网页无法画出线条，怎么办？

我们首先需要确定房间是否加入成功。你可以先通过浏览器的开发模式进入 Console 页面，来查看代码输出的日志。如果看到了报错信息（这些信息往往以醒目的红色标明），我们几乎可以确定房间加入失败了。

我们可以读一读这些报错信息。但这些报错无外乎是如下原因导致的：

* 你的网络有问题，前端代码无法访问 Netless 的服务器。
* **你输入了错误的 `uuid` 和 `roomToken`。**
* 你的企业账号欠费停服了。
* 你的 SDK 版本过低，应该尝试升级到最新版本。

相反，如果你没有发现任何报错信息，那只少意味着你成功加入房间了。排除了这一因素，我们需要开始怀疑是不是样式显示错误。

你可以通过浏览器的开发模式进入 Elements 页面，找到作为白板占位符的 `<div>`。看一看它的尺寸。如果它的宽高的任何一项是 `0px` ，则可以定位这是样式显示问题导致的。

你可以调整你的 Dom 中的样式安排，即调整 `<div>` 的 `style` 属性和 `class` 属性，直到白板占位符 `<div>` 的尺寸符合你的预想。这是前端工程师工作中一直在做的事情，相信对于你来说是轻车熟路了。

## 在 React 项目中展示互动白板

如果你使用 `react` 来管理网页视图，你无需设置白板占位符 `<div>` 。在加入房间成功后，你会拿到 `room` 对象。之后，你可以通过如下代码将互动白板展示出来。

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import React from "react";
import { RoomWhiteboard } from "white-react-sdk";

class App extends React.Component {

    render() {
        var style = {
            width: "100%", 
            height: "100vh",
        };
        return <RoomWhiteboard room={room} style={style}/>;
    }
}
```
{% endtab %}

{% tab title="TypeScript" %}
```typescript
import * as React from "react";
import { RoomWhiteboard } from "white-react-sdk";

class App extends React.Component {

    public render(): React.ReactNode {
        const style = {
            width: "100%", 
            height: "100vh",
        };
        return <RoomWhiteboard room={room} style={style}/>;
    }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Netless 互动白板为 React 项目提供了专门的 SDK：`white-react-sdk`。该 SDK 是可完全代替 `white-web-sdk` 的超集。
{% endhint %}

## 离开房间

当业务上不再使用互动白板，就应该离开房间了。Netless 互动白板**不会**自动离开房间，这一切需要你手动操作。出于如下理由，我们不应该遗忘「离开房间」操作。

* 不离开房间的话，浏览器将维持与 Netless 服务器的长连接。这将消耗前端用户设备的包括网络带宽在内的各种资源。
* Netless 会对没有离开房间的用户继续收费。维持不必要的长连接将导致你所在的团队或公司产生不必要的开支。

{% hint style="success" %}
如果用户直接关闭浏览器，或关闭当前网页 Tab，房间会自动释放，无需担心。
{% endhint %}

你可以通过如下代码主动离开房间。

```javascript
room.disconnect().then(function() {
    // 成功离开房间
}).catch(function(err) {
    // 离开房间失败，获得报错 err
});
```

如果你没有主动离开房间，将造成房间泄漏。总之，你不会希望发生这种事情。

{% hint style="info" %}
当你发现 Netless 给发给你的账单高于预期，这很可能是「房间泄漏」的重要标志。此时此刻，你可以排查应用的业务逻辑代码，或重构那些可能导致状态混乱的地方。

当你彻底修复「房间泄漏」问题，你会发现账单开始符合预期。
{% endhint %}

## 异常流程处理

一个稳健的应用程序，它的业务流程应该包含异常流程处理。你的 Web 应用最终将运行在千家万户的浏览器之上。千家万户的网络问题、DNS 问题、鉴权问题，总之各种问题一定会涌现出来。倘若不处理，上线即是灾难。**我们强烈建议你在设计业务逻辑之初就将异常流程考虑在内。**

Netless 互动白板的实时房间对异常流程的支持，有如下部分。

### 异步调用方法都会返回 Promise

例如，`joinRoom` 和 `disconnect` 都会返回一个 Promise 对象。如果你对 Promise 对象不熟悉，可以先阅读[《Promise - JavaScript \| MDN》](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

异步调用方法失败，则会抛出错误。你可以通过如下方式截获错误，并进入异常处理流程。

```javascript
room.disconnect().catch(function (err) {
    // 截获错误，进入异常处理流程
});
```

如果你的开发环境支持 `async` 、`await` 语法，你可以将代码写成如下形式。

```javascript
try {
    await room.disconnect();
} catch (err) {
    // 截获错误，进入异常处理流程
}
```

### 通过回调函数捕获错误

你可以通过 `joinRoom` 时，附带回调函数，来监听实时房间的异常。

```javascript
var joinRoomParams = {
    uuid: roomUUID,
    roomToken: roomToken,
};

whiteWebSDK.joinRoom(joinRoomParams, {

    onDisconnectWithError: function(err) {
        // 房间因为错误，和服务端断开连接
    },

    onKickedWithReason: function(err) {
        // 用户被踢出房间
    },
});
```


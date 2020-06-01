# 2.9.0 迁移指南

2.9.0 版本修改了某些 API 的方法签名，彻底删除了一些已过期 API。如果你从低版本升级上来，可能发现某些代码无法正常工作。本文将告诉你，如何从旧版本迁移到 2.9.0。

## 初始化 App Identifier

如果你是从 2.8.0 以下版本迁移到 2.9.0 的用户，需要在构造 `WhiteWebSdk` 的代码的初始化参数中加入 `appIdentifier`字段。

```javascript
var whiteWebSdk = new WhiteWebSdk({
    ...originalConfiguration, // 你原来的参数
    appIdentifier: "{{appIdentifier}}", // 你从控制台获取的
});
```

你可以进入 [Netless 管理控制台](https://console.netless.link/)，选择应用管理页面。在「我的项目」中，可以获取项目的 App Identifier。

![&#x4ECE;&#x7BA1;&#x7406;&#x63A7;&#x5236;&#x53F0;&#x83B7;&#x53D6; App Identifier](../.gitbook/assets/app-identifier-example.png)

## 使用 CameraBorder 实现视角限制业务

`room` 和 `player` 对象下的如下成员已经被删除。

* `zoomMaxScale`
* `zoomMinScale`
* `limitWidth`
* `limitHeight`

如果之前通过写过形似如下代码初始化视角限制。

```javascript
import { WhiteWebSdk } from "white-web-sdk";

var whiteWebSdk = new WhiteWebSdk({
    zoomMaxScale: 10.0,
    zoomMinScale: 0.1,
    limitWidth: 1024,
    limitHeight: 768,
});
```

你需要将构造参数中的 `zoomMaxScale`、`zoomMinScale`、`limitWidth`、`limitHeight` 删除，并在加入房间和开始回放时通过 `cameraBound` 代替。

```javascript
import { contentModeScale } from "white-web-sdk";

// 加入房间
whiteWebSdk.joinRoom({
    uuid: "房间 UUID",
    roomToken: "Room Token",
    cameraBound: {
        maxContentMode: contentModeScale(10.0),
        minContentMode: contentModeScale(0.1),
        width: 1024,
        height: 768,
    },
});

// 回放
whiteWebSdk.replay({
    room: "房间 UUID",
    cameraBound: {
        maxContentMode: contentModeScale(10.0),
        minContentMode: contentModeScale(0.1),
        width: 1024,
        height: 768,
    },
});
```

如果之前通过如下代码在中途修改 `room` 或 `player` 的视角限制。

```javascript
// 已过期代码
room.zoomMaxScale = 10.0;
room.zoomMinScale = 0.1;
room.limitWidth = 1024;
room.limitHeight = 768;
```

可以迁移到如下形式。

```javascript
import { contentModeScale } from "white-web-sdk";

room.setCameraBound({
    maxContentMode: contentModeScale(10.0),
    minContentMode: contentModeScale(0.1),
    width: 1024,
    height: 768,
});
```

## 放缩视角

如果你使用如下代码放缩视角。

```javascript
// 实时房间
room.zoomChange(2.0);

// 回放播放器
player.zoomChange(2.0);
```

应该替换成如下代码。

```javascript
// 实时房间
room.moveCamera({
    scale: 2.0,
    animationMode: "continuous",
});

// 回放播放器
player.moveCamera({
    scale: 2.0,
    animationMode: "continuous",
});
```

## 禁止设备操作

如果之前通过 `disableOperations` 禁止设备操作。

```javascript
// 加入房间时就禁止设备操作
whiteWebSdk.joinRoom({
    uuid: "房间 UUID",
    roomToken: "Room Token",
    disableOperations: true,
});

// 在加入房间中途禁止设备操作
room.disableOperations = true;
```

你会发现，我们已经删除了 `disableOperations` 这个成员。这个成员的功能被拆分到了 `disableCameraTransform`、`disableDeviceInputs` 两个新成员之中。

* `disableCameraTransform`：禁止用户通过设备操作改变视角
* `disableDeviceInputs`：禁止用户通过设备操作教具（写写画画）

你需要根据需求确定自己最终想要禁止什么，以决定如何迁移旧代码。如果你只想保证代码的行为和之前完全一致，可以用如下代码代替。

```javascript
// 加入房间时就禁止设备操作
whiteWebSdk.joinRoom({
    uuid: "房间 UUID",
    roomToken: "Room Token",
    disableCameraTransform: true,
    disableDeviceInputs: true,
});

// 在加入房间中途禁止设备操作
room.disableCameraTransform = true;
room.disableDeviceInputs = true;
```

## 回放时指定媒体文件

如果在回放的初始化参数中传入了 `audioUrl`，代码如下。

```javascript
whiteWebSdk.replay({
    room: "房间 UUID",
    audioUrl: "https://my-domain/assets/example.mp4",
});
```

应该替换成。

```javascript
whiteWebSdk.replay({
    room: "房间 UUID",
    mediaURL: "https://my-domain/assets/example.mp4",
});
```

## 白板容器尺寸改变

如果调用如下代码。

```javascript
var width = 1024;
var height = 768;

// 实时房间
room.setViewSize(width, height);

// 回放播放器
player.setViewSize(width, height);
```

应该替换成如下带代码。

```javascript
// 实时房间
room.refreshViewSize();

// 回放播放器
player.refreshViewSize();
```






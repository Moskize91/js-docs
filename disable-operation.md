---
description: 禁止设备操作、只读模式
---

# 禁止操作

到此章为止，我们假设你已经将 Netless 互动白板 SDK 安装并引入了项目，并且已经加入并获取实时房间实例 `room` 对象。如果没有，你可能跳过了之前的章节，强烈建议你先阅读[《安装》](https://developer.netless.link/javascript/installation)[《实时房间](https://developer.netless.link/javascript/realtime-room)》。

## 禁止设备操作

即便你一行代码不写，用户也可以通过自己的设备在白板上写写画画。例如，PC 端用户可以用鼠标操作，而移动端用户可以通过触摸屏操作。如果你希望禁止这种行为，你可以用如下代码来实现。

```javascript
room.disableDeviceInputs = true;
```

你也可以通过如下代码，取消禁止设备操作。

```javascript
room.disableDeviceInputs = false;
```

禁止设备操作仅仅禁止用户通过设备自主地输入操作，但并不禁止你通过代码改变房间的状态。例如，你可以主动调用 `setMemberState` 方法来修改房间状态。也可以插入图片。甚至插入 PPT。

## 只读模式

Netless 互动白板允许你以「只读模式」加入房间。你可以在加入房间时附带如下操作，来声明以只读模式加入。

```javascript
whiteWebSdk.joinRoom({
    uuid: "房间的 uuid",
    roomToken: "房间的 room token",
    isWritable: false, // 声明以只读模式加入房间，默认值为 true
});
```

你也可以通过调用如下方法，从互动模式切换到只读模式。

```javascript
room.setWritable(false).then(function() {
    // 切换成功
}).catch(function(err) {
    // 切换失败，报错 err
});
```

在只读模式下，你无法用设备在白板上写写画画，也无法用代码改变白板状态。你就像一个彻底的看客，无法对房间其他人产生任何影响。

你可以通过调用如下方法，从只读模式切回互动模式。

```javascript
room.setWritable(true).then(function() {
    // 切换成功
}).catch(function(err) {
    // 切换失败，报错 err
});
```

## 这两种模式如何选择

只读模式是一个重量级模式，它影响整个房间的行为，影响计费。Netless 互动白板的房间中，**互动模式的用户最高为 40 人**，如果超过上线，房间将阻止下一个互动模式的用户加入。然而只读模式的用户是没有任何限制的。

如果你的业务场景比较特殊，请合理分配只读模式和互动模式的名额。比如，某人的业务场景是大型公开课，有 5 个老师，50,000 学生。其中 5 个老师都可以操作白板，而学生永远没有权限操作白板，但可以观看老师的操作结果。

此时，这种业务场景可以将 5 个老师设为互动模式，50,000 个学生设为只读模式。如果不假思索的把老师学生都默认设为互动模式，则 Netless 的房间无法容纳这么多学生加入。那么，第 40 人之后加入的人统统都会被阻止。

而禁止设备操作只是一个轻量级模式，即便禁止了设备操作，当前用户依然属于互动模式。

> 此外，只读模式和互动模式的计费是不一样的。如果你不假思索地把所有用户一股脑设置成互动模式，你的团队和公司则需要支付一部分不必要的费用。


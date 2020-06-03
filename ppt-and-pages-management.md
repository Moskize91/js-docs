---
description: 插页、翻页、上传 PPT
---

# PPT 与页面管理

页面，在 Netless 互动白板中称之为场景。场景既可以用于实现多页应用的业务逻辑，如插页、换页、切页等操作，也可以作为 PPT 的承载基础。一个 PPT 可能包含多页，每一页都会对应 Netless 互动白板中的一个场景。我们可以通过切换场景来实现 PPT 切页功能。

到此章为止，我们假设你已经将 Netless 互动白板 SDK 安装并引入了项目，并且已经加入并获取实时房间实例 `room` 对象。如果没有，你可能跳过了之前的章节，强烈建议你先阅读[《安装》](https://developer.netless.link/javascript/installation)[《实时房间](https://developer.netless.link/javascript/realtime-room)》。

{% hint style="info" %}
本章教程只会让你把 PPT 和场景管理的相关内容涉猎一遍。如果你想深入了解相关内容，可以在阅读完本章后参考[《PPT》](https://developer.netless.link/documents/client/ppt)与[《多页白板与场景管理》](https://developer.netless.link/documents/client/multi-page-application-and-scene-management)。
{% endhint %}

## 场景的标示法则

Netless 互动白板有「场景」和「场景组」两个概念。场景组既可以包含场景，也可以包含场景。场景组是没有实体的，你无法切换到某个场景组，但你可以指定切换到某个场景。

每一个场景和场景组都有一个地址（用一个字符串标示）。地址可以作为场景（或场景组）在该房间内的唯一标示。我们还可以通过场景和场景组的地址来判断他们的隶属关系。例如，有如下场景。

* `/math/class-A`
* `/math/class-B`
* `/physical/class-C`

现已知场景组 `/math` ，则场景 `/math/class-A` 和 `/math/class-B` 属于场景组。而 `/physical/class-C` 则不属于场景组。

此外，任何刚刚新建的房间，默认都只包含一个场景，该场景的地址默认为 `/init`。

{% hint style="info" %}
如果你觉得场景标示法则有些晦涩，可以把场景想象成文件，场景组想象成文件夹，它们的地址想象成 Unix / Linux 中文件系统的地址。
{% endhint %}

利用场景标示法则，你可以更好地组织场景，以应对更复杂的业务场景。例如，你可以在同一个房间组织多个 PPT，并混杂多张草稿页。

这套规则也许有点复杂，但如果理解并掌握了它，你就能更好地应对复杂场景需求。如果你想了解更多，可以阅读[《多页白板与场景管理》](https://developer.netless.link/documents/client/multi-page-application-and-scene-management)。

## 插页、切页

在房间被创建后，我们得到一个空白的页面（空场景）。我们可以通过如下方法来读取当前场景的地址和索引。

```javascript
var sceneState = room.state.sceneState;
console.log("scene path:", sceneState.scenePath);
console.log("scene index:", sceneState.index);
```

这段代码执行后，Console 输出如下内容。

```c
scene path: /init
scene index: 0
```

不出所料，默认场景的地址为 `/init`，索引为 `0`。现在，我们插入一个页面（场景），并切换到该场景。

```javascript
room.putScenes("/", [{name: "new-page"}]);
room.setScenePath("/new-page");
```

这段代码第 1 行标示，在地址为 `"/"` 的场景组中插入名为 `"new-page"` 的新场景。根据命名可知，`"/init"` 属于 `"/"` 这个场景组。因此新插入的场景和 `"/init"` 同属同一个场景组。此外，我们还可以知道，新插入的场景的完整地址为 `"/new-page"`。因此，第 2 行代码使用这个完整地址切换到了新场景。

我们如果执行如下代码。

```javascript
var sceneState = room.state.sceneState;
console.log("scene path:", sceneState.scenePath);
console.log("scene index:", sceneState.index);
```

会发现输出变成了。

```c
scene path: /new-page
scene index: 1
```

看来我们已经成功切到新场景 `"/new-page"` 了。特别的，索引也从 0 变成了 1。因为新场景是排在 `"/init"` 之后的，自然应该是 1，这很合乎情理。

之后，我们还可以通过指定索引的方式切回 `"/init"` 场景。

```javascript
room.setSceneIndex(0);
```

我们如果执行如下代码。

```javascript
var sceneState = room.state.sceneState;
console.log("scene path:", sceneState.scenePath);
console.log("scene index:", sceneState.index);
```

会发现输出变回了。

```c
scene path: /init
scene index: 0
```

说明我们切回去了。

## 展示 PPT 页面

在插入场景时，互动白板 SDK 允许在场景底部带一张背景图片（通过 URL）。我们可以把一个 PPT 文件拆成多页，每一页转换成一张图片，并将这一组图片上传到互联网，最终得到一组 URL。并创建一组场景，这一组场景按顺序以特定的 URL 作为背景图片，最后把这组场景插入到房间中。

{% hint style="success" %}
Netless 提供 PPT 转化服务。既可以将 PPT 页转化成静态图片，也可以转化成保留动画的可互动 HTML 页面。更多信息可参考[《PPT》](https://developer.netless.link/documents/client/ppt)。
{% endhint %}

现在，不管怎么做的，总之我假设你得到了一组 PPT 分页转化而来的图片 URL 列表。你可以通过如下代码插入到房间中。

```javascript
var width = 1024; // ppt 的宽
var height = 768; // ppt 的高
var imageURLs = [ // ppt 分页图片 URL
    "https://my-domain/class/math/1.png",
    "https://my-domain/class/math/2.png",
    "https://my-domain/class/math/3.png",
    "https://my-domain/class/math/4.png",
    "https://my-domain/class/math/5.png",
    "https://my-domain/class/math/6.png",
];
var scenes = imageURLs.map(function(url, index) {
    return {
        name: "" + (index + 1),
        ppt: {
            src: url,
            width: width,
            height: height,
        },
    };
});

// 将 ppt 页插入场景
room.putScenes("/math", scenes);

// 切换到新插入 ppt 的第一页
room.setScenePath("/math/1");
```

## 预览与截图

你可以将某个场景（无论是否是当前场景）的预览填充到一个 `<div>` 占位符中。

```javascript
var scenePath = "/math/class-A";
var divElement = document.getElementById("preview-spaceholder");
var width = divElement.clientWidth;
var height = divElement.clientHeight;

room.scenePreview(scenePath, divElement, width, height);
```

## 清屏

你可以直接将当前场景清空。

```javascript
room.cleanCurrentScene();
```

该操作默认不会清理掉背景图片（根据业务，很可能是 PPT）。如果你希望连背景图片也要被清理掉。可以加上一个参数。

```javascript
room.cleanCurrentScene(false);
```


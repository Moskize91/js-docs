# SDK参数

## 初始化 API

#### TypeScript 方法签名

```typescript
//WhiteWebSdk.d.ts
constructor(params: WhiteWebSdkConfiguration = {})
```

#### 示例代码

```typescript
//初始化 SDK
var whiteWebSdk = new WhiteWebSdk({
    appIdentifier: "{{appIdentifier}}"
    preloadDynamicPPT: false, // 可选,是否预先加载动态 PPT 中的图片，会显著提升用户体验，降低翻页的图片加载时长
    deviceType: "touch", // 可选, touch or desktop , 默认会根据运行环境进行推断
    // ...更多可选参数配置
});
```

## 参数：

#### TypeScript 签名

```typescript
// WhiteWebSdk.d.ts
export type WhiteWebSdkConfiguration = {
    appIdentifier: string;
    deviceType?: DeviceType;
    screenType?: ScreenType;
    renderEngine?: RenderEngine;
    fonts?: UserFonts;
    handToolKey?: string;
    preloadDynamicPPT?: boolean;
    loggerOptions?: LoggerOptions;
    reconnectionOptions?: Partial<ReconnectionOptions> | false;
    onlyCallbackRemoteStateModify?: boolean;
    plugins?: Plugins;
    urlInterrupter?: (url: string)=>string;
    onWhiteSetupFailed?: (error: Error)=>void;
    zoomMaxScale?: number;
    zoomMinScale?: number;
    limitWidth?: number;
    limitHeight?: number;
};
```

{% hint style="warning" %}
所有参数均为可选，部分已有默认值。加粗为常用配置项目
{% endhint %}

#### **urlInterrupter**: 图片替换

```typescript
// 传入一个插入图片/ppt 时的原始地址，返回一个任意修改后的地址
urlInterrupter?: (url: string) => string;
```

{% hint style="warning" %}
在插入图片和创建新场景背景图时，sdk 会调用该 API，此时可以修改最终显示的url。 如果没有需要，请不要传入该参数。目前在绘制时，会频繁调用该 API。
{% endhint %}

#### **deviceType**: 设备类型

```typescript
// 值：`desktop`|`touch`|`surface`。
// 根据传入值，依次接受`mouse`事件，`touch`事件；传入`surface`时，则会同时接收`touch`,`mouse`事件。

export enum DeviceType {
    Desktop = "desktop",//默认值
    Touch = "touch",    //移动端
    Surface = "surface",//同时监听所有移动事件
}
```

#### **renderEngine**：渲染模式

```typescript
// 默认值是 RenderEngine.SVG。

export enum RenderEngine {
    /** 以 svg 的形式渲染 */
    SVG = "svg",
    /** 以 canvas 的形式渲染 */
    Canvas = "canvas",
}
```

#### fonts: ppt 映射字体

```typescript
类型结构：`{key: url}`

动态 ppt 需要的自定义字体映射，`key`为动态 ppt 所用的字体名称,
`url`为字典所在网络地址。
```

#### **handToolKey**: 抓手工具快捷键

```typescript
类型：`string`
设置后，用户同时按住该快捷键与鼠标，即可移动整个白板。
可以输入`KeyboardEvent`键盘事件可以出发的`key`属性。推荐传入空格键(`" "`)
```

#### preloadDynamicPPT: 动态 ppt 预加载

```typescript
默认`false`，类型：`boolean`

是否预先加载动态 PPT 中的图片，选择 true,
会在第一页时，就加载所有图片，从而保证翻页时，能够立即显示图片。
```

{% hint style="warning" %}
预加载进度回调，可以在初始化 room player 时，进行配置。可以查看[房间参数](https://developer.netless.link/docs/javascript/parameters/js-room)与[回放参数](https://developer.netless.link/docs/javascript/parameters/js-player)中 onPPTLoadProgress 配置。
{% endhint %}

#### loggerOptions: 日志上报配置

```typescript
// 默认值

{
    //是否禁用上传，默认上传
    disableReportLog: false,
    //上传日志的等级，默认 info
    reportLevelMask: "info",
    //打印日志的等级，默认 info
    printLevelMask: "info";
}
```

```typescript
// 允许修改的值

{
    disableReportLog?: boolean,
    reportLevelMask?: "debug" | "info" | "warn" | "error",
    printLevelMask?: "debug" | "info" | "warn" | "error";
}
```

#### onlyCallbackRemoteStateModify: 状态回调

```typescript
默认`true`,类型：`boolean`，只对`room`有效。
`room`本地修改的内容，是否在`onRoomStateChange`中回调。
默认本地主动修改的状态，不会在`onRoomStateChange`回调中出现。
```

#### zoomMaxScale:放大上限

用户可以放到的最大比例，默认不限制。 开发者仍然可以使用代码进行放大。


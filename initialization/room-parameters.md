# 房间参数

`room`与`player`实际上，都是内部`displayer`的子类。`TypeScript`签名中，以`///Displayer.d.ts`开头的方法签名，`room`和`player`均可使用。

我们将用户实时使用，并对外同步的房间，称为 **实时房间**（`对应类为 room`）；

### 初始化 API

#### TypeScript 方法签名

```typescript
//WhiteWebSdk.d.ts
public joinRoom(
params: JoinRoomParams,
callbacks: RoomCallbacks = {}): Promise<Room>
```

#### 示例代码

```typescript
whiteWebSdk.joinRoom({
    uuid: json.msg.room.uuid,
    roomToken: json.msg.roomToken,
},
// callback 本身为可选参数，可不传。
{
    onRoomStateChanged: modifyState => {
        console.log(modifyState);
    },
    onPhaseChanged: phase => {
        console.log(phase);
    }
).then(function(room) {
    //实时房间实例
    window.room = room;
}).catch(function(err) {
    //初始化房间失败
    console.log(e);
})
```

### JoinRoomParams 参数说明：

#### TypeScript 签名

```typescript
export type JoinRoomParams = {
    readonly uuid: string;
    readonly roomToken: string;
    readonly userPayload?: any;
    readonly isWritable?: boolean;
    readonly disableDeviceInputs?: boolean;
    readonly disableBezier?: boolean;
    readonly cursorAdapter?: CursorAdapter;
    readonly cameraBound?: CameraBound;
    readonly disableEraseImage?: boolean;
    readonly disableOperations?: boolean;
};
```

{% hint style="warning" %}
除`uuid`,`roomToken`外，其他均为可选参数。右侧目录中加粗字段，为常用设置。
{% endhint %}

#### **uuid**\(必须\): string

```typescript
房间标识，同一个房间的人，可以进行互动。
```

#### **roomToken**\(必须\): string

```typescript
房间鉴权信息。
```

#### **userPayload**: 用户信息

```typescript
可以为任意内容，会在 room.state.roomMembers 中体现。
SDK 会将其作为用户的信息，完整传递，不作处理。
```

#### **isWritable**: 只读模式 / 可写模式

```typescript
是否以可写模式进入房间（否则为只读模式）。
可写模式进入房间后，可以急性：操作教具、修改房间相关状态等一切将自己的信息同步给房间其他人的操作。
只读模式进入房间后，仅仅只能接收其他人同步的信息，不能操作教具、修改房间状态。
以只读模式进入房间的人无法被其他人察觉，也无法出现在房间成员列表中。
默认是值是 true
```




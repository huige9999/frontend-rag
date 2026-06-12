# 07 - Geolocation API

## 一、基本概念

Geolocation API 允许网页获取用户的地理位置信息（经纬度），常用于地图定位、天气查询、附近搜索等场景。

**使用前提：**
- 需要用户授权（浏览器会弹出定位权限提示）
- 仅在 HTTPS 或 localhost 下可用
- 依赖设备硬件支持（GPS / WiFi / IP 定位）

## 二、获取当前位置 — getCurrentPosition

```js
navigator.geolocation.getCurrentPosition(
  (position) => {
    // 成功回调
    const { longitude, latitude, accuracy } = position.coords;
    console.log(`经度：${longitude}`);
    console.log(`纬度：${latitude}`);
    console.log(`精确度：${accuracy}`);
  },
  (error) => {
    // 错误回调
    console.log(error);
  }
);
```

**参数说明：**

| 参数 | 说明 |
|------|------|
| 第1个参数 | 成功回调，接收 `Position` 对象 |
| 第2个参数 | 错误回调，接收 `PositionError` 对象 |
| 第3个参数（可选） | 配置对象，如 `{ enableHighAccuracy: true }` |

## 三、position.coords 属性一览

| 属性 | 类型 | 说明 |
|------|------|------|
| `latitude` | Number | 纬度，以度为单位 |
| `longitude` | Number | 经度，以度为单位 |
| `altitude` | Number / null | 海拔高度（米），需要额外传感器支持 |
| `accuracy` | Number | 经纬度精确度（米），值越小越精确 |
| `altitudeAccuracy` | Number / null | 海拔精确度（米） |
| `heading` | Number / null | 设备方向（度），与地磁传感器相关 |
| `speed` | Number / null | 设备速度（米/秒），通常与 GPS 相关 |

> 并非所有属性在每个设备上都可用，使用时应做好空值判断。

## 四、持续监听位置变化

```js
const watchId = navigator.geolocation.watchPosition(
  (position) => {
    console.log(position.coords.latitude, position.coords.longitude);
  },
  (error) => console.log(error),
  { enableHighAccuracy: true, maximumAge: 0, timeout: 5000 }
);

// 不再需要监听时
navigator.geolocation.clearWatch(watchId);
```

**可选配置项：**

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `enableHighAccuracy` | 是否请求高精度定位（更耗电） | `false` |
| `timeout` | 获取位置的超时时间（毫秒） | `Infinity` |
| `maximumAge` | 可接受的缓存位置最大年龄（毫秒） | `0` |

## 五、错误处理

`PositionError` 对象包含以下错误码：

| 错误码 | 常量 | 说明 |
|--------|------|------|
| 1 | `PERMISSION_DENIED` | 用户拒绝了定位权限 |
| 2 | `POSITION_UNAVAILABLE` | 无法获取位置信息 |
| 3 | `TIMEOUT` | 获取位置超时 |

## 六、API 速查表

| API | 说明 |
|-----|------|
| `getCurrentPosition(success, error?, options?)` | 获取当前位置（一次性） |
| `watchPosition(success, error?, options?)` | 持续监听位置变化，返回 watchId |
| `clearWatch(watchId)` | 停止监听 |

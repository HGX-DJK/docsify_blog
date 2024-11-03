# cesium中的坐标系

Cesium 支持两种坐标系：**3D笛卡尔坐标系** 和 **经纬度坐标系**  

## Cesium提供了很多坐标系互相转换的大类

1、经纬度转空间直角

```js
const cartesian3 = Cesium.Cartesian3.fromDegrees(lng, lat, height)
```

2、经纬度转地理坐标（弧度）

```js
const radians = Cesium.Math.toRadians(degrees)
```

3、地理坐标（弧度）转经纬度

```js
const degrees = Cesium.Math.toDegrees(radians)
```

4、空间直角转经纬度

```js
// 先将3D笛卡尔坐标转为地理坐标（弧度）
const cartographic = Cesium.Cartographic.fromCartesian(cartesian3);
// 再将地理坐标（弧度）转为经纬度
const lat = Cesium.Math.toDegrees(cartographic.latitude);
const lng = Cesium.Math.toDegrees(cartographic.longitude);
const height = cartographic.height;
```

5、屏幕坐标转经纬度

```js
// 监听点击事件，拾取坐标
  const handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
  handler.setInputAction((e) => {
    const clickPosition = viewer.scene.camera.pickEllipsoid(e.position);
    const randiansPos = Cesium.Cartographic.fromCartesian(clickPosition);
    console.log(
      "经度：" +
        Cesium.Math.toDegrees(randiansPos.longitude) +
        ", 纬度：" +
        Cesium.Math.toDegrees(randiansPos.latitude)
    );
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK);
```

6、屏幕坐标转空间直角坐标

```js
 var cartesian3 = viewer.scene.globe.pick(viewer.camera.getPickRay(windowPostion),  viewer.scene);
```

7、世界坐标转屏幕坐标

```js
windowPostion = Cesium.SceneTransforms.wgs84ToWindowCoordinates(viewer.scene, cartesian3);
```

---
title: maps
date: 2017-12-18 10:34:45
tags:
---

地图相关

###  获取当前经纬度

浏览器`navigator`对象

```
navigator.geolocation.getCurrentPosition(success,error,options)
```

具体例子

```
var options = {
  enableHighAccuracy: true,
  timeout: 5000,
  maximumAge: 0
};

function success(pos) {
  var crd = pos.coords;

  console.log('Your current position is:');
  console.log('Latitude : ' + crd.latitude);
  console.log('Longitude: ' + crd.longitude);
  console.log('More or less ' + crd.accuracy + ' meters.');
};

function error(err) {
  console.warn('ERROR(' + err.code + '): ' + err.message);
};

navigator.geolocation.getCurrentPosition(success, error, options);
```


###  高德地图相关操作

#### 新建地图

`<script src="//webapi.amap.com/maps?v=1.3&key=您申请的key值"></script>`

    var map = new AMap.Map('container',{
        resizeEnable: true,
        zoom: 10,   //  1-18  手机上为19
        center: [116.480983, 40.0958]
    });

JavaScript API提供了丰富的覆盖物，比如Marker点标记、Polyline折线、Polygon多边形、Circle圆等。以Marker为例， 我们创建一个最简单的Marker，并将它添加到地图上

两种方式set

    var marker = new AMap.Marker({
        position: [116.480983, 39.989628],//marker所在的位置
        map:map//1. 创建时直接赋予map属性
    });
    //2. 也可以在创建完成后通过setMap方法执行地图对象
    marker.setMap(map);


####  JavaScript API UI组件库

UI组件库是基于 JavaScript API 基本功能接口与服务接口开发的一套组件库，封装了大量实用的组件，侧重于帮助开发者快速实现地图上附加功能开发，同时满足灵活定制的个性化展示需求


UI组件库基于高德地图JavaScript API，侧重于帮助开发者快速实现地图上UI组件的个性化展示。 举例来说，Javascript API本身提供标准的Marker（即点标注）作为地图的基本功能，而UI组件库则提供更多颜色、样式的Marker(如下图)，帮助开发者快速实现具备丰富效果的地图应用。

`<script src="//webapi.amap.com/ui/1.0/main.js"></script>`

这样引用

    AMapUI.loadUI([
        'overlay/SimpleMarker',//SimpleMarker
        'overlay/SimpleInfoWindow',//SimpleInfoWindow
        ],
    function(SimpleMarker, SimpleInfoWindow) {
        //....引用加载的UI....
    });

#### 插件

高德地图JavaScript API在核心功能之外提供了丰富的控件、服务插件以及工具插件，比如工具条、比例尺、路线规划、高级信息窗体等等，详细清单见下方总览，在使用这些接口之前需要使用AMap.plugin方法将对应的功能加载下来


#### 坐标与地址


####  基础类

`AMap.Pixel`  像素坐标，确定地图上的一个像素点。`AMap.Pixel(x:Number,y:Number)`

`AMap.Size`     地物对象的像素尺寸 `AMap.Size(width:Number,height:Number)`

`AMap.LngLat`    构造一个地理坐标对象，lng、lat分别代表经度、纬度值 `AMap.LngLat(lng:Number,lat:Number)`

    var lnglat = new AMap.LngLat(116.368904, 39.923423);
    lnglat.distance([116.387271, 39.922501])


`AMap.Bounds(southWest:LngLat,                 northEast:LngLat)`  矩形范围的构造函数.参数southWest、northEast分别代表地物对象西南角经纬度和东北角经纬度值


####  定位

```
mapObj = new AMap.Map('iCenter');
mapObj.plugin('AMap.Geolocation', function () {
    geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,//是否使用高精度定位，默认:true
        timeout: 10000,          //超过10秒后停止定位，默认：无穷大
        maximumAge: 0,           //定位结果缓存0毫秒，默认：0
        convert: true,           //自动偏移坐标，偏移后的坐标为高德坐标，默认：true
        showButton: true,        //显示定位按钮，默认：true
        buttonPosition: 'LB',    //定位按钮停靠位置，默认：'LB'，左下角
        buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
        showMarker: true,        //定位成功后在定位到的位置显示点标记，默认：true
        showCircle: true,        //定位成功后用圆圈表示定位精度范围，默认：true
        panToLocation: true,     //定位成功后将定位到的位置作为地图中心点，默认：true
        zoomToAccuracy:true      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
    });
    mapObj.addControl(geolocation);
    geolocation.getCurrentPosition();
    AMap.event.addListener(geolocation, 'complete', onComplete);//返回定位信息
    AMap.event.addListener(geolocation, 'error', onError);      //返回定位出错信息
});
```

注意这个得到的是GPS定位，是有偏差的，你得把GPS定位转化为高德坐标,key换成自己的

`http://restapi.amap.com/v3/assistant/coordinate/convert?key=f7978xxxxx353a4901d7ae71f96fc5e&locations=104.054637,30.588663&coordsys=gps`




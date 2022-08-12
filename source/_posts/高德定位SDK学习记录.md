---
title: 高德定位SDK学习记录
date: 2022-08-12 10:24:24
tags: Java
categories: 
    - Java
    - Android
---

看了高德定位SDK的部分demo代码，进行一下代码的学习记录



主要的流程：

首先创建一个 **AMapLocationClient**

```java
//初始化client
try {
	locationClient = new AMapLocationClient(this.getApplicationContext());
	locationOption = getDefaultOption();
	//设置定位参数
	locationClient.setLocationOption(locationOption);
	// 设置定位监听
	locationClient.setLocationListener(locationListener);
} catch (Exception e) {
	e.printStackTrace();
}
```

这里需要对于创建出来的locationClient对象进行定位参数的设置，用到的是 **AMapLocationClientOption** 这个类，然后设置定位监听器

定位监听的设置：

```java
/**
 * 定位监听
 */
AMapLocationListener locationListener = new AMapLocationListener() {
	@Override
	public void onLocationChanged(AMapLocation location) {
		if (null != location) {

			StringBuffer sb = new StringBuffer();
			//errCode等于0代表定位成功，其他的为定位失败，具体的可以参照官网定位错误码说明
			if(location.getErrorCode() == 0){
				sb.append("定位成功" + "\n");
				sb.append("定位类型: " + location.getLocationType() + "\n");
				sb.append("经    度    : " + location.getLongitude() + "\n");
    ......
  }
}
```

这里的 **AMapLocationListener** 是用于定位监听的监听器，在 onLocationChanged 方法的参数为 **AMapLocation** 类对象，是返回的定位结果，这里对于定位结果进行处理

而需要获得定位结果我们必须先启动定位：

这里调用 locationClient.startLocation() 进行定位的开启，之后我们才会在监听器监听到定位结果的变化

```java
/**
 * 开始定位
 * 
 * @since 2.8.0
 * @author hongming.wang
 *
 */
private void startLocation(){
	try {
		//根据控件的选择，重新设置定位参数
		resetOption();
		// 设置定位参数
		locationClient.setLocationOption(locationOption);
		// 启动定位
		locationClient.startLocation();
	} catch (Exception e) {
		e.printStackTrace();
	}

}
```

对于前面提到的定位参数，这里直接列出一些默认值：

```java
/**
 * 默认的定位参数
 * @since 2.8.0
 * @author hongming.wang
 *
 */
private AMapLocationClientOption getDefaultOption(){
    AMapLocationClientOption mOption = new AMapLocationClientOption();
    mOption.setLocationMode(AMapLocationMode.Hight_Accuracy);//可选，设置定位模式，可选的模式有高精度、仅设备、仅网络。默认为高精度模式
    mOption.setGpsFirst(false);//可选，设置是否gps优先，只在高精度模式下有效。默认关闭
    mOption.setHttpTimeOut(30000);//可选，设置网络请求超时时间。默认为30秒。在仅设备模式下无效
    mOption.setInterval(2000);//可选，设置定位间隔。默认为2秒
    mOption.setNeedAddress(true);//可选，设置是否返回逆地理地址信息。默认是true
    mOption.setOnceLocation(false);//可选，设置是否单次定位。默认是false
    mOption.setOnceLocationLatest(false);//可选，设置是否等待wifi刷新，默认为false.如果设置为true,会自动变为单次定位，持续定位时不要使用
    AMapLocationClientOption.setLocationProtocol(AMapLocationProtocol.HTTP);//可选， 设置网络请求的协议。可选HTTP或者HTTPS。默认为HTTP
    mOption.setSensorEnable(false);//可选，设置是否使用传感器。默认是false
    mOption.setWifiScan(true); //可选，设置是否开启wifi扫描。默认为true，如果设置为false会同时停止主动刷新，停止以后完全依赖于系统刷新，定位位置可能存在误差
    mOption.setLocationCacheEnable(true); //可选，设置是否使用缓存定位，默认为true
    mOption.setGeoLanguage(AMapLocationClientOption.GeoLanguage.DEFAULT);//可选，设置逆地理信息的语言，默认值为默认语言（根据所在地区选择语言）
    return mOption;
}
```



上面分析的是正常开始定位的流程

在场景定位的情况下可以不设置定位参数直接设置定位场景来快速设置好定位参数

如下是签到场景的设置：

```java
AMapLocationClientOption option = new AMapLocationClientOption();
/**
 * 设置签到场景，相当于设置为：
 * option.setLocationMode(AMapLocationClientOption.AMapLocationMode.Hight_Accuracy);
 * option.setOnceLocation(true);
 * option.setOnceLocationLatest(true);
 * option.setMockEnable(false);
 * option.setWifiScan(true);
 * option.setGpsFirst(false);
 * 其他属性均为模式属性。
 * 如果要改变其中的属性，请在在设置定位场景之后进行
 */
option.setLocationPurpose(AMapLocationClientOption.AMapLocationPurpose.SignIn);
```

若是需要启动或关闭后台定位，相关方法：

```java
//启动后台定位
locationClient.enableBackgroundLocation(2001, buildNotification());
//关闭后台定位
locationClient.disableBackgroundLocation(true);
```



坐标转换：

```java
//初始化坐标转换类
CoordinateConverter converter = new CoordinateConverter(
		getApplicationContext());
/**
 * 设置坐标来源,这里使用百度坐标作为示例
 * 可选的来源包括：
 * <li>CoordType.BAIDU ： 百度坐标
 * <li>CoordType.MAPBAR ： 图吧坐标
 * <li>CoordType.MAPABC ： 图盟坐标
 * <li>CoordType.SOSOMAP ： 搜搜坐标
 * <li>CoordType.ALIYUN ： 阿里云坐标
 * <li>CoordType.GOOGLE ： 谷歌坐标
 * <li>CoordType.GPS ： GPS坐标
 */
converter.from(CoordType.BAIDU);
//设置需要转换的坐标
converter.coord(examplePoint);
//转换成高德坐标
DPoint destPoint = converter.convert();
```

要进行坐标转换需要创建 **CoordinateConverter** 类对象，设置原坐标的来源，设置需要转换的坐标，调用 convert() 来进行坐标转换得到 destPoint



获取最后一次位置：

```java
AMapLocation location = locationClient.getLastKnownLocation();
```

这里比较简单，直接调用 locationClient 的 **getLastKnownLocation()** 方法，会返回历史定位里的上一次定位结果



地理围栏功能：

这里看的不是特别的思路清晰，拿圆形地理围栏的demo为例：

```java
mMapView = (MapView) findViewById(R.id.map);
mMapView.onCreate(savedInstanceState);
...
mAMap = mMapView.getMap();
mAMap.getUiSettings().setRotateGesturesEnabled(false);
mAMap.moveCamera(CameraUpdateFactory.zoomBy(6));
setUpMap();
```

地图组件 **mAMap** 是由 **mMapView** 得到的，这里设置了禁用旋转手势

调用了 setUpMap() 对于地图进行了一下设置，具体如下：

```java
/**
 * 设置一些amap的属性
 */
private void setUpMap() {
    mAMap.setOnMapClickListener(this);
    mAMap.setLocationSource(this);// 设置定位监听
    mAMap.getUiSettings().setMyLocationButtonEnabled(true);// 设置默认定位按钮是否显示
    // 自定义系统定位蓝点
    MyLocationStyle myLocationStyle = new MyLocationStyle();
    // 自定义定位蓝点图标
    myLocationStyle.myLocationIcon(
        BitmapDescriptorFactory.fromResource(R.drawable.gps_point));
    // 自定义精度范围的圆形边框颜色
    myLocationStyle.strokeColor(Color.argb(0, 0, 0, 0));
    // 自定义精度范围的圆形边框宽度
    myLocationStyle.strokeWidth(0);
    // 设置圆形的填充颜色
    myLocationStyle.radiusFillColor(Color.argb(0, 0, 0, 0));
    // 将自定义的 myLocationStyle 对象添加到地图上
    mAMap.setMyLocationStyle(myLocationStyle);
    mAMap.setMyLocationEnabled(true);// 设置为true表示显示定位层并可触发定位，false表示隐藏定位层并不可触发定位，默认是false
    // 设置定位的类型为定位模式 ，可以由定位、跟随或地图根据面向方向旋转几种
    mAMap.setMyLocationType(AMap.LOCATION_TYPE_LOCATE);
}
```

下面这部分有点没完全搞懂，记录下：

```java
IntentFilter filter = new IntentFilter();
filter.addAction(GEOFENCE_BROADCAST_ACTION);
registerReceiver(mGeoFenceReceiver, filter);

/**
 * 创建pendingIntent
 */
fenceClient.createPendingIntent(GEOFENCE_BROADCAST_ACTION);
fenceClient.setGeoFenceListener(this);
/**
 * 设置地理围栏的触发行为,默认为进入
 */
fenceClient.setActivateAction(GeoFenceClient.GEOFENCE_IN);
```

当点击地图时，会触发：

```java
@Override
public void onMapClick(LatLng latLng) {
    markerOption.icon(ICON_YELLOW);
    centerLatLng = latLng;
    addCenterMarker(centerLatLng);
    tvGuide.setBackgroundColor(getResources().getColor(R.color.gary));
    tvGuide.setText("选中的坐标：" + centerLatLng.longitude + ","
        + centerLatLng.latitude);
}
```

这里会读取到经纬度，并调用 addCenterMarker(centerLatLng) 添加标记点

添加标记点只是在地图里选点而已

添加围栏需要按按钮，这里直接看到相应方法：

```java
/**
 * 添加圆形围栏
 * 
 * @since 3.2.0
 * @author hongming.wang
 *
 */
private void addRoundFence() {
	String customId = etCustomId.getText().toString();
	String radiusStr = etRadius.getText().toString();
	if (null == centerLatLng
			|| TextUtils.isEmpty(radiusStr)) {
		Toast.makeText(getApplicationContext(), "参数不全", Toast.LENGTH_SHORT)
				.show();
		return;
	}

	DPoint centerPoint = new DPoint(centerLatLng.latitude,
			centerLatLng.longitude);
	fenceRadius = Float.parseFloat(radiusStr);
	fenceClient.addGeoFence(centerPoint, fenceRadius, customId);
}
```

一旦添加围栏会触发方法：

```java
@Override
public void onGeoFenceCreateFinished(final List<GeoFence> geoFenceList,
		int errorCode, String customId) {
	Message msg = Message.obtain();
	if (errorCode == GeoFence.ADDGEOFENCE_SUCCESS) {
		fenceList.addAll(geoFenceList);
		msg.obj = customId;
		msg.what = 0;
	} else {
		msg.arg1 = errorCode;
		msg.what = 1;
	}
	handler.sendMessage(msg);
}
```

当添加了围栏触发上面这个方法，并发送message

```java
case 0 :
	StringBuffer sb = new StringBuffer();
	sb.append("添加围栏成功");
	String customId = (String)msg.obj;
	if(!TextUtils.isEmpty(customId)){
		sb.append("customId: ").append(customId);
	}
	Toast.makeText(getApplicationContext(), sb.toString(),
			Toast.LENGTH_SHORT).show();
	drawFence2Map();
	break;
```

在 handler 里面会进行处理，调用到 drawFence2Map() 方法

方法主要执行的是画围栏以及将围栏加入列表的逻辑，这里就不往里看了

根据自身的位置和围栏范围触发广播，可以知道自身位置和围栏范围的状态，如是否进入了围栏或走出了围栏，这个广播有两个情况会触发，一个是添加围栏时可能触发，一个是位置和围栏范围的关系发生变化时可能触发

```java
/**
 * 接收触发围栏后的广播,当添加围栏成功之后，会立即对所有围栏状态进行一次侦测，如果当前状态与用户设置的触发行为相符将会立即触发一次围栏广播；
 * 只有当触发围栏之后才会收到广播,对于同一触发行为只会发送一次广播不会重复发送，除非位置和围栏的关系再次发生了改变。
 */
private BroadcastReceiver mGeoFenceReceiver = new BroadcastReceiver() {
	@Override
	public void onReceive(Context context, Intent intent) {
		// 接收广播
		if (intent.getAction().equals(GEOFENCE_BROADCAST_ACTION)) {
			Bundle bundle = intent.getExtras();
			String customId = bundle
					.getString(GeoFence.BUNDLE_KEY_CUSTOMID);
			String fenceId = bundle.getString(GeoFence.BUNDLE_KEY_FENCEID);
			//status标识的是当前的围栏状态，不是围栏行为
			int status = bundle.getInt(GeoFence.BUNDLE_KEY_FENCESTATUS);
			StringBuffer sb = new StringBuffer();
			switch (status) {
				case GeoFence.STATUS_LOCFAIL :
					sb.append("定位失败");
					break;
				case GeoFence.STATUS_IN :
					sb.append("进入围栏 ");
					break;
				case GeoFence.STATUS_OUT :
					sb.append("离开围栏 ");
					break;
				case GeoFence.STATUS_STAYED :
					sb.append("停留在围栏内 ");
					break;
				default :
					break;
			}
			if(status != GeoFence.STATUS_LOCFAIL){
				if(!TextUtils.isEmpty(customId)){
					sb.append(" customId: " + customId);
				}
				sb.append(" fenceId: " + fenceId);
			}
			String str = sb.toString();
			Message msg = Message.obtain();
			msg.obj = str;
			msg.what = 2;
			handler.sendMessage(msg);
		}
	}
};
```
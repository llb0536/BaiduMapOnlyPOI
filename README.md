# BaiduMapOnlyPOI
[![Version](https://img.shields.io/cocoapods/v/BaiduMapOnlyPOI.svg?style=flat)](http://cocoadocs.org/docsets/BaiduMapOnlyPOI)
[![License](https://img.shields.io/cocoapods/l/BaiduMapOnlyPOI.svg?style=flat)](http://cocoadocs.org/docsets/BaiduMapOnlyPOI)
[![Platform](https://img.shields.io/cocoapods/p/BaiduMapOnlyPOI.svg?style=flat)](http://cocoadocs.org/docsets/BaiduMapOnlyPOI)

## Quick start
`BaiduMapOnlyPOI` 允许用户通过CoreLocation获取坐标，然后使用Baidu地图获取反向信息

`BaiduMapOnlyPOI` 支持 Swift
`BaiduMapOnlyPOI` 支持 虚拟机 & 真机，提交审核时使用请替换成真机framework

`AppDelegate.swift`添加：
```swift
    let baiduManager:BMKMapManager = BMKMapManager()
    var ret:Bool = baiduManager.start("your_key", generalDelegate: nil)
    if (!ret) {
        println("manager start failed!")
    }else{
        println("manager start succ!")
    }    
```
`your_class.swift`添加
```
class City: NSObject,CLLocationManagerDelegate,BMKGeoCodeSearchDelegate{
    let locationManager:CLLocationManager = CLLocationManager()
    let searcher:BMKGeoCodeSearch = BMKGeoCodeSearch()
    override init(){

        super.init()
        locationManager.delegate = self
        searcher.delegate = self

        locationManager.desiredAccuracy = kCLLocationAccuracyBest
        locationManager.distanceFilter = kCLLocationAccuracyThreeKilometers
        if is_ios8() {
            locationManager.requestAlwaysAuthorization()
        }
        locationManager.startUpdatingLocation()
    }
    func locationManager(manager: CLLocationManager!, didUpdateLocations locations: [AnyObject]!){
        self.locationManager.stopUpdatingLocation();
        var location:CLLocation = locations.last as! CLLocation

        if location.horizontalAccuracy > 0 {
            var pt:CLLocationCoordinate2D = location.coordinate
            var reverseGeoCodeSearchOption:BMKReverseGeoCodeOption = BMKReverseGeoCodeOption.alloc()
            reverseGeoCodeSearchOption.reverseGeoPoint = pt
            var flag:Bool = searcher.reverseGeoCode(reverseGeoCodeSearchOption)

            if(flag)
            {
              println("反geo检索发送成功");
            }
            else
            {
              println("反geo检索发送失败");
            }
    }

}

```

回调：
```
    func onGetReverseGeoCodeResult(searcher:BMKGeoCodeSearch,#result:BMKReverseGeoCodeResult,errorCode error:BMKSearchErrorCode){
        if error.value == 0 {
            let addr = result.addressDetail
            self.city_name = addr.city
            self.district = addr.district
            self.province = addr.province
            self.level2 = addr.city
          }else {
            println("抱歉，未找到结果")
          }
    }
```


`BaiduMapOnlyPOI` 支持 [CocoaPods](http://cocoapods.org).  添加下面的配置到 `Podfile`:

```ruby
pod 'BaiduMapOnlyPOI', :git => 'https://github.com/llb0536/BaiduMapOnlyPOI', :tag => '2.7.0
```

### 感谢
Thank qzs21. Original project forked from [qzs21/BaiduMapAPI](https://github.com/qzs21/BaiduMapAPI)


### 百度地图 iOS API 包含如下功能：
-------------------
* 基础地图：包括基本矢量地图、卫星图、实时路况图和各种地图覆盖物，此外还包括各种与地图相关的操作和事件监听；
* 检索功能：包括POI检索，Place详情检索，公交信息查询，路线规划，地理编码/反地理编码，在线建议查询，短串分享等；
* LBS云检索：包括LBS云检索（周边、区域、城市内、详情）；
* 定位功能：获取当前位置信息；
* 计算工具：包括测距（两点之间距离）、坐标转换、调起百度地图导航等功能；


--------------------------------------------------------------------------------------

### 当前版本为v2.7.0，较上一个版本（v2.6.0）的更新内容如下：


* 自当前版本起，百度地图iOS SDK推出 .framework形式的开发包。此种类型的开发包配置简单、使用方便，欢迎开发者选用！
* 【 新 增 】
```
基础地图
    1. 增加地图缩放等级到20级（10米）；
    2. 新增地理坐标与OpenGL坐标转换接口：
        BMKMapView新增接口：
        -(CGPoint)glPointForMapPoint:(BMKMapPoint)mapPoint;//将BMKMapPoint转换为OpenGL ES可以直接使用的坐标
        -(CGPoint *)glPointsForMapPoints:(BMKMapPoint *)mapPoints count:(NSUInteger)count;// 批量将BMKMapPoint转换为OpenGL ES可以直接使用的坐标
    3. 开放区域截图能力：
        BMKMapView新增接口：
        -(UIImage*) takeSnapshot:(CGRect)rect;// 获得地图区域区域截图
检索功能
    1. 开放驾车线路规划，返回多条线路的能力：
        BMKDrivingRouteResult中，routes数组有多条数据，支持检索结果为多条线路
    2. 驾车线路规划结果中，新增路况信息字段：
        BMKDrivingRoutePlanOption新增属性：
        ///驾车检索获取路线每一个step的路况，默认使用BMK_DRIVING_REQUEST_TRAFFICE_TYPE_NONE
        @property (nonatomic) BMKDrivingRequestTrafficType drivingRequestTrafficType;
        BMKDrivingStep新增属性：
        ///路段是否有路况信息
        @property (nonatomic) BOOL hasTrafficsInfo;
        ///路段的路况信息，成员为NSNumber。0：无数据；1：畅通；2：缓慢；3：拥堵
        @property (nonatomic, strong) NSArray* traffics;
    3.废弃接口：
        BMKDrivingRouteLine中，废弃属性：isSupportTraffic
计算工具
    1. 新增点与圆、多边形位置关系判断方法：
        工具类BMKGeometry.h中新增接口：
        //判断点是否在圆内
        UIKIT_EXTERN BOOL BMKCircleContainsPoint(BMKMapPoint point, BMKMapPoint center, double radius);
        UIKIT_EXTERN BOOL BMKCircleContainsCoordinate(CLLocationCoordinate2D point, CLLocationCoordinate2D center, double radius);
        //判断点是否在多边形内
        UIKIT_EXTERN BOOL BMKPolygonContainsPoint(BMKMapPoint point, BMKMapPoint *polygon, NSUInteger count);
        UIKIT_EXTERN BOOL BMKPolygonContainsCoordinate(CLLocationCoordinate2D point, CLLocationCoordinate2D *polygon, NSUInteger count);
    2. 新增获取折线外某点到这线上距离最近的点：
        工具类BMKGeometry.h中新增接口：
        UIKIT_EXTERN BMKMapPoint BMKGetNearestMapPointFromPolyline(BMKMapPoint point, BMKMapPoint* polyline, NSUInteger count);
    3、新增计算地理矩形区域的面积
        工具类BMKGeometry.h中新增接口：
        UIKIT_EXTERN double BMKAreaBetweenCoordinates(CLLocationCoordinate2D leftTop, CLLocationCoordinate2D rightBottom);
```
【 优 化 】
```
    1. 减少首次启动SDK时的数据流量；
    2. 检索协议优化升级；
    3. 优化Annotation拖拽方法（长按后开始拖拽）；
```
【 修 复 】
```
    1. 修复在线地图和离线地图穿插使用时，地图内存不释放的bug；
    2. 修复云检索过程中偶现崩溃的bug；
    3. 修复地图在autolayout布局下无效的bug；
    4. 修复BMKAnnotationView重叠的bug；
```

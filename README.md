# BaiduMapOnlyPOI
[![Version](https://img.shields.io/cocoapods/v/BaiduMapOnlyPOI.svg?style=flat)](http://cocoadocs.org/docsets/BaiduMapOnlyPOI)
[![License](https://img.shields.io/cocoapods/l/BaiduMapOnlyPOI.svg?style=flat)](http://cocoadocs.org/docsets/BaiduMapOnlyPOI)
[![Platform](https://img.shields.io/cocoapods/p/BaiduMapOnlyPOI.svg?style=flat)](http://cocoadocs.org/docsets/BaiduMapOnlyPOI)

## Quick start

`BaiduMapOnlyPOI` 支持 [CocoaPods](http://cocoapods.org).  添加下面的配置到 `Podfile`:

```ruby
pod 'BaiduMapOnlyPOI', '~> 2.7.1'
```
### 本项目的百度地图类库 iOS API 包含如下功能：
检索功能：包括POI检索，Place详情检索，公交信息查询，路线规划，地理编码/反地理编码，在线建议查询，短串分享等；

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
class YourLocationClass: NSObject,CLLocationManagerDelegate,BMKGeoCodeSearchDelegate{
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
          }else {
            println("抱歉，未找到结果")
          }
    }
```

### 感谢
Thank qzs21. Original project forked from [qzs21/BaiduMapAPI](https://github.com/qzs21/BaiduMapAPI)


### 当前版本为v2.7.0，较上一个版本（v2.6.0）的更新内容如下：


* 自当前版本起，百度地图iOS SDK推出 .framework形式的开发包。此种类型的开发包配置简单、使用方便，欢迎开发者选用！
* 【 新 增 】
```
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
```

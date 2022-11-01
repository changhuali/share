# RN iOS UI 组件桥接

## 如何创建 UI 组件

- 原生端定义

1. 定义一个继承`MKMapView`的类`MapView`

```objective-c
@interface MapView : MKMapView

@property (nonatomic, copy) RCTBubblingEventBlock onRegionChange;

@end
```

2. 定义一个继承`RCTViewManager`的类`MapViewManager`并用`RCT_EXPORT_MODULE`宏导出该类

```objective-c
@interface MapViewManager : RCTViewManager

@end

@implementation MapViewManager

RCT_EXPORT_MODULE()

@end
```

3. 为`MapViewManager`定义实例方法`- (UIView *)view`

```objective-c
- (UIView *)view
{
  UIView *view = [[UIView alloc] init];
  return view;
}
```

- js 端使用

```js
import { requireNativeComponent } from 'react-native'

const MapView = requireNativeComponent('MapView')

export default MapView
```

## 如何给组件定义属性

### 无自定义逻辑

- 原生端定义

1. 通过`RCT_EXPORT_VIEW_PROPERTY`宏导出属性

- 第一个参数为属性名
- 第二个参数为属性类型名，该类型必须是`RCTConvert`类的一个静态方法名，rn 会利用这个静态方法对 js 侧的数据进行转换

```objective-c
RCT_EXPORT_VIEW_PROPERTY(zoomEnabled, BOOL)
```

- js 端使用

```jsx
<MapView zoomEnabled={false} />
```

### 有自定义逻辑

- 原生端定义

1. 通过`RCT_CUSTOM_VIEW_PROPERTY`宏导出属性
   方法内可以使用`json` `view` `defaultView` 三个参数

- json -> `js` 侧的数据
- view -> 当前 `view` 实例
- defaultView -> 利用 `MKMapView` 默认实例

```objective-c
RCT_CUSTOM_VIEW_PROPERTY(region, MKCoordinateRegion, MKMapView)
{
  // 编写各种自定义逻辑
  [view setRegion:json ? [RCTConvert MKCoordinateRegion:json] animated:YES];
}
```

## 如何接收组件事件

- 原生端定义

1. 通过`RCT_EXPORT_VIEW_PROPERTY`宏导出属性，属性名必须以`on`为前缀(否则该属性失效)，类型固定为`RCTBubblingEventBlock`

```objective-c
RCT_EXPORT_VIEW_PROPERTY(onRegionChange, RCTBubblingEventBlock)
```

- js 端使用

```jsx
<MapView
  onRegionChange={evt => {
    console.log(evt.nativeEvent)
  }}
/>
```

## 如何给组件定义实例方法

- 原生端定义

```objective-c
RCT_EXPORT_METHOD(setCenterCoordinate:(nonnull NSNumber*)reactTag coordinate: (CLLocationCoordinate2D)coordinate animated: (BOOL)animated) {
    [self.bridge.uiManager addUIBlock:^(RCTUIManager *uiManager, NSDictionary<NSNumber *,UIView *> *viewRegistry) {
      RNTMapView *view = (RNTMapView *)viewRegistry[reactTag];
      if (!view || ![view isKindOfClass:[RNTMapView class]]) {
          RCTLogError(@"Cannot find RNTMapView with tag #%@", reactTag);
          return;
      }
      [view setCenterCoordinate: coordinate animated: animated];
    }];
}
```

- js 端使用

```jsx
UIManager.dispatchViewManagerCommand(
  findNodeHandle(mapRef.current),
  UIManager.getViewManagerConfig('MapView').Commands.setCenterCoordinate,
  [coordinate, animated],
)
```

## 原生端如何接收 js 侧传递的 UI 内容

- 通过 children 传递的内容
  rn 会自动将 children 插入到 native 侧的节点中
- 需调用方法生成内容
  不支持

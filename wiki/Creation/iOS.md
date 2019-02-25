
## Macro

- **RCTBridgeModule**: RCTBridgeModule 是一个 protocol，它为注册 bridge 模块 RCTBridgeModule `@protocol RCTBridgeModule` 提供了一个接口

- **RCT_EXPORT_MODULE(js_name)**: 在 class implementation 时使用 bridge 注册你的模块。参数 js_name 是可选的，用作 JS 模块的名称，若不定义，将会默认使用 Objective-C 的 class 名

- **RCT_EXPORT_METHOD(method)\RCT_REMAP_METHOD(, method)**: bridge 模块亦可定义方法，这些方法可以作为 `NativeModules.ModuleName.methodName` 输出到 JavaScript。

```objectivec
RCT_EXPORT_METHOD(funcName:(NSString *)onlyString
                    secondName:(NSInteger)argInteger)
  { ... }
```

上面的例子暴露到 JavaScript 是 `NativeModules.ModuleName.funcName`

## 创建原生模块包

我们需要在项目中添加两个文件：头文件和源文件。



```objectivec
- MyNativePackage.h

#import "RCTBridgeModule.h"

@interface MyNativePackage : NSObject <RCTBridgeModule>
@end

- MyNativePackage.m

#import "MyNativePackage.h"

@implementation MyNativePackage

RCT_EXPORT_MODULE();

@end
```

## 创建模块方法



```objectivec
RCT_EXPORT_METHOD(Show:(RCTResponseSenderBlock)callback) {
}
```

- JavaScript 中引入模块方法

```javascript
import { NativeModules } from 'react-native'

const MyNativeModule = NativeModules.MyNativeModule;
MyNativeModule.Show(() => {})
```

## 创建原生 View 组建

- 创建 view 方法，返回你的原声组件

```objectivec
- (UIView *)view {
    //Initialize & return your native component view
}
```

- 创建原生 prop 方法

```objectivec
RCT_CUSTOM_VIEW_PROPERTY(prop, DATA_TYPE_OF_PROP, YOUR_NATIVE_COMPONENT_CLASS) {
}
```


- 在 JavaScript 中使用

```javascript
import { requireNativeComponent } from "react-native"

const MyNativeComponent = requireNativeComponent("RNNativeComponent", RNNativeComponent, {
  nativeOnly: { }
})

<MyNativeComponent prop={this.props.prop}>
```

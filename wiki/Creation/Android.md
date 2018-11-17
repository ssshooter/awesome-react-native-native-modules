
## 如何创建一个原生模块包

- 通过继承 ReactPackage 为你的原生模块包创建 Java 类，可以这么写：

- 覆盖 `createNativeModules` 和 `createViewManagers` 方法

```java

public class MyNativePackage implements ReactPackage {
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    }
}

```

- 在 `createNativeModules` 方法中添加原生模块

```java
public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    List<NativeModule> modules = new ArrayList<>();
    modules.add(new MyNativeModule(reactContext));

    return modules;
}
```

- 在 `createViewManagers` 方法中添加原生 UI 组件

```java
public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    List<ViewManager> components = new ArrayList<>();
    components.add(new RNNativeComponent());

    return components;
}
```

## 创建原生模块

- 先通过继承 ReactContextBaseJavaModule 创建 MyNativeModule 类。

```java
public class MyNativeModule extends ReactContextBaseJavaModule {
    public MyNativeModule(ReactApplicationContext reactContext) {
        super(reactContext);
    }
}
```

- 为了让 React Native 在 NativeModules 中找到我们的模块，我们还需要覆盖 getName 方法。

```java
@Override
public String getName() {
    return "MyNativeModule";
}
```

- 现在我们已经拥有一个可以导入到 JavaScript 代码的原生模块，现在可以向其中加入功能。

- 暴露模块方法：定义一个接受设置参数、成功回调和失败回调的 `Show` 方法。

```java
public class MyNativeModule extends ReactContextBaseJavaModule {
    @ReactMethod
    public void Show(ReadableMap config, Callback successCallback, Callback cancelCallback) {
        Activity currentActivity = getCurrentActivity();
    
        if (currentActivity == null) {
            cancelCallback.invoke("Activity doesn't exist");
            return;
        }
    }
}
```

- 在 JavaScript 中调用模块方法

```javascript
import { NativeModules } from "react-native";

const { MyNativeModule } = NativeModules;

MyNativeModule.Show(
    {}, //Config Parameters
    () => {}, //Success Callback
    () => {} //Cancel Callback
)

```

> **注意**:
> - 模块方法只提供静态引用，不包含于 react 树中。

## 创建原生 UI 组件

- 如果你需要在 react 树中渲染一个原声 UI 组件

- 创建一个继承 ReactNative ViewGroupManager 的 Java 类

```java
public class RNNativeComponent extends ViewGroupManager<ViewGroup> {
    public static final String REACT_CLASS = "RNNativeComponent";
}
```

- 覆盖 `getName` 方法：

```java
@Override 
public String getName() {
    return REACT_CLASS;
} 
```

- 覆盖 `createViewInstance` 方法，返回你的自定义原生组件

```java
@Override 
  protected FrameLayout createViewInstance(final ThemedReactContext reactContext) {
      return //用 FrameLayout 包裹的自定义原生组件
  }
```

- 创建原生 prop 方法

```java
  @ReactProp(name = "prop_name") 
  public void setPropName(NativeComponent nativeComponent, propDataType prop) {
  }
```


- JavaScript 中使用

```javascript
import { requireNativeComponent } from "react-native"

const MyNativeComponent = requireNativeComponent("RNNativeComponent", RNNativeComponent, {
  nativeOnly: { }
})

<MyNativeComponent prop={this.props.prop}>
```

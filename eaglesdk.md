# EagleSDK

## SDK的架构
```
├── AppFrame 
├── Catagory
├── Core
├── EGAutoNavItem
├── EGBind
├── EGChainKit
├── EGEncryption
├── EGHex
├── EGTabBarController
├── EagleSDK
├── Eaglekit
├── EventBus
├── GuideView
├── MVVM
├── ModalMenu
├── NavigationBarManager
├── Networking
├── Pop
├── RootNavigationController
├── Router
├── SanboxFile
├── SearchController
├── Session
├── URL
├── WebKit
└── WebViewJSBridge
```

## Core
core的组成：  

```
├── EGAppDelegateProxy.h
├── EGAppDelegateProxy.m
├── EGComponent.h
├── EGComponent.m
├── EGComponentDefine.h
├── EGDom.h
├── EGDom.m
├── EGMacros.h
├── EGModuleManager.h
├── EGModuleManager.m
├── EGNode.h
├── EGNode.m
├── EGProvider.h
├── EGProvider.m
├── EGRouterManager+Module.h
├── EGRouterManager+Module.m
├── EGSDKLoader.h
├── EGSDKLoader.m
├── NSObject+Service.h
├── NSObject+Service.m
├── UIViewController+EGComponent.h
└── UIViewController+EGComponent.m
```
* EGAppDelegateProxy是一个NSProxy的子类，用来拦截UIApplicationDelegate协议方法。
* EGComponent是组件化的基类，所有的Component都必须继承它。
* EGModuleManager封装了模块管理功能。
* EGProvider是模块对外提供调用的统一入口，模块之间的解耦就是通过它进行runtime的动态调用。
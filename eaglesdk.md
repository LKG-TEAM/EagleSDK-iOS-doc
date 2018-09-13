# EagleSDK

## SDK的第三方依赖
1、EGIconfont:通过Cocoapods自动添加依赖。  
2、

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
├── EGAppDelegateProxy
├── EGComponent
├── EGComponentDefine.h
├── EGDom
├── EGMacros.h
├── EGModuleManager
├── EGNode
├── EGProvider
├── EGRouterManager+Module
├── EGSDKLoader
├── NSObject+Service
└── UIViewController+EGComponent
```
* EGAppDelegateProxy是一个NSProxy的子类，用来拦截UIApplicationDelegate协议方法。
* EGComponent是组件化的基类，所有的Component都必须继承它。
* EGModuleManager封装了模块管理功能。
* EGProvider是模块对外提供调用的统一入口，模块之间的解耦就是通过它进行runtime的动态调用。
* EGSDKLoader是App启动时加载SDK的启动类。

### 常用API
- EGSDKLoader

```ObjectiveC
+(void)loadSDKWithOptions:(NSDictionary *)launchOptions debug:(BOOL)isDebug;
```
用来在App启动时加载EagleSDK，使用场景：

>```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
   self.window=[[UIWindow alloc]initWithFrame:[UIScreen mainScreen].bounds];
    [self.window makeKeyAndVisible];
    [[UIApplication sharedApplication]setFontName:@"LMSPDemo"];
    [UIApplication sharedApplication].preloadON=NO;
#if DEBUG
    BOOL debug=YES;
#else
    BOOL debug=NO;
#endif
    [EGSDKLoader loadSDKWithOptions:launchOptions debug:debug];
    return YES;
}
```



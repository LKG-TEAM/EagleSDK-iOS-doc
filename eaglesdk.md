# EagleSDK

## SDK的第三方依赖
1、EGIconfont:通过Cocoapods自动添加依赖。  
2、因为EagleSDK的使用形式是动态库，所以将`Bugly`、`SVProgressHUD`、`IQKeyboardManager`、`SVGKit`这些第三方库摘除了依赖关系，需要手动添加到应用或者模块开发工程中。

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


# Core
## core的组成：  

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

## 常用Class和API

- EGMacros宏定义

```
EGLog(...)							//控制台打印
eg_weakify( x )						//weak引用
eg_strongify( x )					//strong引用
EGDEBUG								//判断是否是Debug模式
EGSettings_ValueOf(...)  			//读取平台配置的settings.json文件
WebModule_BaseURL(webModuleName)	//获取web模块的baseURL
```
---
- EGSDKLoader

```ObjectiveC
+(void)loadSDKWithOptions:(NSDictionary *)launchOptions debug:(BOOL)isDebug;
```

>用来在App启动时加载EagleSDK，启动时的进行了下面的一系列操作：  

>- 1、设置SVProgressHUD的样式。
>- 2、启动Bugly。
>- 3、启动引导页并。
>- 4、WebviewPool初始化。
>- 5、开始监听网络状态。
>
>使用场景：

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
	
---
- EGComponent

```
#pragma mark -- Component的生命周期
- (void)componentInit;
- (void)viewWillAppear:(BOOL)animated __attribute__((objc_requires_super));
- (void)viewDidAppear:(BOOL)animated __attribute__((objc_requires_super));
- (void)viewWillDisappear:(BOOL)animated __attribute__((objc_requires_super));
- (void)viewDidDisappear:(BOOL)animated __attribute__((objc_requires_super));
- (void)removeFromSuperviewComponent __attribute__((objc_requires_super));

#pragma mark -- 链式调用
- (NSArray *(^)(void))done;
- (EGComponent *(^)(NSString *componentId))getComponentByComponentId;
- (EGComponent *(^)(NSString *))eg_id;
- (EGComponent *(^)(NSString *eg_id))getComponentById;
- (EGComponent *(^)(NSString *))eg_tag;
- (NSArray *(^)(NSString *eg_tag))getComponentsByTag;
- (NSArray *(^)(NSString *className))getComponentsByClassName;
```

>Component的生命周期与Controller的生命周期进行了绑定。 
  
>- Component的初始化会调用`- (void)componentInit`。



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
### 常用类	
[EGMacros](#macros)		
[EGSDKLoader](#sdkloader)	
[EGModuleManager](#modulemanager)
[EGComponent](#component)	
[EGProvider](#provider)		
[EGRouterManager](#router)

---

### EGMacros宏定义<span id="macros"></span>

```
EGLog(...)							//控制台打印
eg_weakify( x )						//weak引用
eg_strongify( x )					//strong引用
EGDEBUG								//判断是否是Debug模式
EGSettings_ValueOf(...)  			//读取平台配置的settings.json文件
WebModule_BaseURL(webModuleName)	//获取web模块的baseURL
```
---



### EGSDKLoader<span id="sdkloader"></span>

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
### EGModuleManager <span id="modulemanager"></span>


---

### EGComponent<span id="component"></span>

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


- Component的生命周期与Controller的生命周期进行了绑定。 
- Component的初始化会调用`- (void)componentInit`。  
- Component添加子Component，使用`addComponent`链式调用方法，ViewController也提供了`addComponent`的链式调用方法，这个是对Component的该方法的进一步封装:

>```
>self.addComponent(component.className,Flex flex,EGSize size,params)
>```

- Component访问ViewController，Component持有一个presentVC的弱引用的属性,使用该属性访问父容器的控制器。

>```
@property (nonatomic, weak)UIViewController *presentVC;
>```

- Component的布局方式：
   
&emsp;&emsp;1、Flex布局方向:

>```
>typedef NS_ENUM(NSUInteger, Flex) {     
    FlexCloum  = 0, // 纵向排列  
    FlexRow			// 横向排列
};
>```

&emsp;&emsp;2、EGSize布局尺寸，百分比和Size只能选用其中一种方式来设定Component的尺寸,`usePercent `设为YES使用百分比布局时，`width`和`height`设置不起作用；使用固定值布局时，`horizontal `和`vertical `设置不起作用:
>```
struct EGSize {
    BOOL usePercent;		// YES -- 使用百分比布局，反之使用固定值布局
    CGFloat horizontal;		// 取值范围:0~1.0
    CGFloat vertical;		// 取值范围:0~1.0
    CGFloat width;
    CGFloat height;
};
>```

- 获取子Component。	

&emsp;&emsp;1、获取子Component的集合，Component的add、insert、remove的链式操作完成后可以调用done方法，获取子Component的数组。	

>```
>-(NSArray *(^)(void))done;
>```

&emsp;&emsp;2、获取特定的子Component:
>```
-(EGComponent *(^)(NSString *componentId))getComponentByComponentId;
-(EGComponent *(^)(NSString *eg_id))getComponentById;
-(NSArray *(^)(NSString *eg_tag))getComponentsByTag;
-(NSArray *(^)(NSString *className))getComponentsByClassName;
>```

---
### EGProvider<span id="provider"></span>
```
//获取Web模块的BaseURL
+(NSString *)getBaseURLWithModuleName:(NSString *)moduleName;

//模块注册UIApplicationDelegate的OpenURL代理方法的回调
+(void)registOpenURL:(EGProviderOpenURLActionType)type action:(EGProviderOpenURLAction)actionBlock;


//动态修改ViewController.json内容(开发使用)
+(void)updateVCJSONWithClass:(Class)vcClazz withValue:(id)value forKeyPath:(NSString *)keyPath;

```
- 设计EGProvider类的目的是对外提供类似Service层的服务。不使用子类继承，通过添加分类的方式扩展EGProvider提供的服务。分类中的方法使用类方法，调用时，如果有依赖，可以直接调用，没有依赖时，可以通过runtime进行动态调用。

---
### EGRouterManager<span id="router"></span>
```
+ (void)navigateToModule:(EGURL *)url options:(id)options;
+ (void)navigateToModule:(EGURL *)url options:(id)options callback:(EGCallback)callback;
+ (void)startAppWithModuleRouter:(NSString *)moduleRouter;
```
- Core中的方法是添加了EGRouterManager对模块路由的支持。

>1.通过路由跳转

>```
>+ (void)navigateToModule:(EGURL *)url options:(id)options;
>```
>2.需要模块回调结果的路由跳转

>```
>+ (void)navigateToModule:(EGURL *)url options:(id)options callback:(EGCallback)callback;
>```

- 模块的Demo工程需要加载模块，通过下面方法可以在App启动模块

>```
>- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
    self.window=[[UIWindow alloc]initWithFrame:[UIScreen mainScreen].bounds];
    [self.window makeKeyWindow];
    [EGRouterManager startAppWithModuleRouter:EGModuleRouter(LMSPApplication)];
    return YES;
}
>```

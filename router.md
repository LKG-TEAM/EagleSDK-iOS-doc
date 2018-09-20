# Router
## Router的组成
```
├── EGProviderRouter
├── EGRouter
├── EGRouterManager+Blocks
└── EGRouterManager
```

## 常用Class和API
- [EGRouterManager](#manager)	
- [EGProviderRouter](#provider)

### EGRouterManager<span id="manager"></span>
```
+ (void)start;
+ (void)navigateTo:(NSString *)router;
+ (void)navigateTo:(NSString *)router hideBottomWhenPushed:(BOOL)hidden;
+ (void)navigateTo:(NSString *)router options:(id)options;
+ (void)navigateTo:(NSString *)router callback:(EGCallback)callback;
+ (void)navigateTo:(NSString *)route options:(id)options callback:(EGCallback)callback;

//获取顶层的视图控制器
- (UIViewController *)topViewController;

//获取mainWindow，不要使用[UIApplication sharedApplication].keyWindow来获取。
+ (UIWindow *)mainWindow;
```
- `start`方法在`EGSDKLoader`启动SDK时调用。
- 使用`navigateTo`进行路由跳转。

### EGProviderRouter<span id="provider"></span>
```
-(void)addRouter:(NSString *)router withSelector:(SEL)sel;
```
- 通过路由的方式提供EGProvider中方法调用。[参考EGModuleExport协议](eaglesdk_core?id=modulemanager)
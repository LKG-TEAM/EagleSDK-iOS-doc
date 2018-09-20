# Webkit

## Webkit的组成
```
├── EGInternalWebViewController
├── EGURLProtocol
├── EGWebComponentBaseViewModel
├── EGWebComponentHistory
├── EGWebViewController
├── EGWebviewPool
├── NSObject+WebComponent
├── NSURLProtocol+WKWebVIew
├── UIApplication+Preload
├── UIBarButtonItem+NativeNavBar
├── UIViewController+NativeNavBar
├── WKProcessPool+SharedProcessPool
├── WKWebView+Pool
├── WebComponent
└── WebComponentSettingViewController
```

## 常用Class和API
[WebComponent](#webcomponent)		
[EGWebComponentBaseViewModel](#basemodel)    
[WebComponentSettingViewController](#setting)    
[NSObject+WebComponent](#jscall)		

### WebComponent<span id="webcomponent"></span>
```
-(void)addTarget:(NSObject<WebComponentDelegate> *)webDelegate andIdentifier:(NSString *)identifier;
-(void)addHandlers:(NSArray *)handlers withTarget:(id<WebComponentDelegate>)delegate;
-(void)callJSHandler:(NSString *)handler from:(id)target options:(id)options complete:(TopicBlock)completeBlock;
-(void)goBackWithData:(id)data;
-(void)goBack;
```

#### WebComponentDelegate protocol
```
/**
 js Call native 事件处理
 @param handler handler名称
 @param data 传入数据
 @param callback js回调
 */
 @required
-(void)navtiveCall:(NSString *)handler data:(id)data callBack:(TopicResultBlock)callback;

/**
 native Call js 结果回调
 @param handler handler名称
 @param responseData 返回数据
 */
 @optional
-(void)jsCallback:(NSString *)handler response:(id)responseData;

/**
 url拦截调用
 @param handler handler名称
 @param msg 信息
 */
 @optional
-(void)urlCall:(NSString *)handler params:(NSArray *)params msg:(id)msg;

```
- `WebComponentDelegate`是处理js与原生交互的协议。

#### Webcomponet protocol
```
@property(nonatomic,strong)NSURL *loadURL;
@property(nonatomic,strong)NSArray *handlers;
-(void)setupNavi:(NSString *)routePath option:(NSDictionary *)option;
```
- `Webcomponet`协议继承自`WebComponentDelegate`协议。
- `-(void)setupNavi:(NSString *)routePath option:(NSDictionary *)option`方法是为Web页面使用原生跳转定制导航栏样式的协议方法。

### EGWebComponentBaseViewModel <span id="basemodel"></span>
```
-(void)jsCallNativeForUndefinedHandler:(NSString *)handler data:(id)data callBack:(JSCallBack)callback;
```
#### EGWebComponentInternalHandler
- `EGWebComponentInternalHandler`是提供WebComponent中js与原生交互中的内部系统方法。([参考WebModule](webmodule?id=js与原生交互))

### WebComponentSettingViewController<span id="setting"></span>
- Debug模式下，用来设置WebComponent的配置信息。

### NSObject+WebComponent<span id="jscall"></span>
```
/**
 调用web中js handler，当只有一个webcomponent的时候快捷方法
 @param handler handler名称
 @param options 传递参数
 */
-(void)jsCall:(NSString *)handler options:(id)options;

/**
 调用web中js handler
 @param handler handler名称
 @param options 传递参数
 @param identifier 响应webcomponent的识别id
 */
-(void)jsCall:(NSString *)handler options:(id)options withIdentifier:(NSString *)identifier;
```
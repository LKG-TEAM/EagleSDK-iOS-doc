[webMap.plist]:./assets/webmodule_s1.png

## 说明
1、模块工程都是基于EagleSDK进行开发。所以dependency.default中默认配置EagleSDK。    
2、WebModule是用来专门加载应用中本地或者远程Web内容，并提供原生交互功能的部分的模块化封装。

## 创建WebModule工程
* WebModule的工程结构是基于NativeModule工程，所以相关操作可以[参考NativeModule](/nativemodule)。

## 创建ViewModel
需要调用内置原生交互功能，所以ViewModel创建时要继承`EGWebComponentBaseViewModel`基类。

## 加载本地文件
本地H5工程默认地址是静态库文件夹下`Assets/www`文件夹，其他位置暂时不支持。一个WebModule只能对应一个本地H5工程。

## 配置文件
- 工程里面除了ViewController的json配置文件外，还提供了webMap.plist文件来保存WebModule的加载配置。	
>Example
>![webMap.plist]
- isRemote表示是否加载远程地址，NO时加载本地文件，默认是加载文件是`Assets/www/index.html`
- env表示当前环境，dev为debug模式，product为release模式。

## js与原生交互
- js与原生通过桥接的方式进行交互，并通过事件总线进行封装。js通过`EagleJS`库调用原生功能。([EagleJS的参考文档](http://eagle-js.eagledoc.cn))
	
### js调用原生	
* _内置原生交互_	
	* EGWebComponentBaseViewModel实现了以下内置的原生交互提供使用，可以通过js直接进行调用。	
		>```ObjectiveC
		#pragma mark -- Internal JS Call Native Methods
		#define EGWGetSystemInfoHandler     @"_getSystemInfo_"    //获取系统信息
		#define EGWGetNetworkTypeHandler    @"_getNetworkType_"   //获取网络状态
		#define EGWGetAppVersionHandler     @"_getAppVersion_"    //获取应用信息
		#define EGWGenerateQRCodeHandler    @"_generateQRCode_"   //生成二维码
		#define EGWOpenQRCodeHandler        @"_openQRCode_"       //扫描二维码
		#define EGWOpenAlbumHandler         @"_openAlbum_"        //打开相册
		#define EGWOpenCameraHandler        @"_openCamera_"       //获取相机
		#define EGWGetLocationInfoHandler   @"_getLocationInfo_"  //获取位置信息
		#define EGWCloseWindowHandler       @"_closeWindow_"      //关闭窗口
		#define EGWOpenWindowHandler        @"_openWindow_"       //新建窗口
		#define EGWSetNavBarStyleHandler    @"_setNavBarStyle_"   //设置导航栏样式
		#define EGWSetOptionMenuHandler     @"_setOptionMenu_"    //设置下拉菜单
		#define EGWFetchDataHandler         @"_fetchData_"        //发送原生网络请求
		#define EGWGoBackHandler            @"_goBack_"           //WKWebView的返回
		#define EGWOnHistroyChangeHandler   @"_onHistoryChange_"  //WKWebView的历史记录变化
		#define EGWLogHandler               @"_log_"              //调用原生控制台打印
		/* -----以下功能是提供给web页面使用原生跳转功能----- */
		#define EGSetNavigationBarHandler   @"_setNavigationBar_" //设置导航栏
		#define EGHistoryCallbackHandler    @"_historyCallback_"  //History返回时callback回调
		>```
	* 内置的原生交互方法都必须加`_`的前缀和后缀。
* _自定义原生交互_
	* 自定义原生交互方法可以是类方法也可以是实例方法，但是要遵守的2个约定：
	
		1.方法名称定义为：`(methodName)Handler`，没有入参。      
		2.方法返回值类型必须是：`JSCallNativeBlock`或者`void(^)(NSString *handler,id data,JSCallBack callback)`类型。
	* EGProvider全局原生交互	

	在Module工程定义一个EGProvider的分类，遵守上述约定使用类方法定义交互方法。    
		 
		>Example     		
		>```ObjectiveC
		  +(JSCallNativeBlock)industryDataHandler{
    			return ^(NSString *handler,id data,JSCallBack callback){
        			SEL getData=NSSelectorFromString(@"getDictWithBo:callback:"); //api -LMSPCommonService
        			if ([EGProvider respondsToSelector:getData]) {
            			[EGProvider performSelector:getData withObject:@"Mhome.SelectAllIndustry" withObject:^(id data){
                			callback(data);
           	 			}];
        			} else {
            			callback(nil);
        			}
    			};
		}
		  >```

	* ViewModel自定义原生交互		

	ViewModel中使用实例方法定义交互方法。   
	
		>Example     		
		  >```ObjectiveC
		  -(JSCallNativeBlock)industryDataHandler{
    			return ^(NSString *handler,id data,JSCallBack callback){
        			SEL getData=NSSelectorFromString(@"getDictWithBo:callback:"); //api -LMSPCommonService
        			if ([EGProvider respondsToSelector:getData]) {
            			[EGProvider performSelector:getData withObject:@"Mhome.SelectAllIndustry" withObject:^(id data){
                			callback(data);
           	 			}];
        			} else {
            			callback(nil);
        			}
    			};
		}
		  >```
		  
		* 方法返回block类型中参数：	
			- handler为js调用原生的方法名称。	
			- data为js调用时传入的参数。	
			- callback为js需要原生执行完成后将结果返回的回调入口。
		* EGProvider和ViewModel中自定义原生交互方法的区别：  
			- 1.EGProvider使用类方法，ViewModel使用实例方法。  
			- 2.调用优先级不同，存在同名的方法时，EGProvider的原生交互方法调用优先于ViewModel中的交互方法。  
			- 3.模块除了封装内部功能，提供统一入口外，有些还对外提供功能接口，像一些底层模块如地图模块、网络模块,都是通过创建EGProvider分类提供API，使用时通过runtime进行动态调用实现模块之间的解耦。所以EGProvider提供的原生交互方法可以实现全局原生交互。而ViewModel的作用范围只限于绑定的Controller中。
		
### 原生调用js
* 通过`NSObject+WebComponent.h`中API进行调用：
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
* 不需要结果回调的调用	
	使用上面API中方法即可
	* handler为js在EagleJS中注册的js方法名称。
	* options为传入的参数。
* 需要结果回调的调用
	- 1.正常通过上面API调用js。
	- 2.结果是通过`WebComponentDelegate`协议方法:	    `-(void)jsCallback:(NSString *)handler response:(id)responseData` 	   进行返回。
		- 由于使用代理模式处理回调结果，所以调用js可以在ViewModel外进行，但是返回结果必须在ViewModel中进行。
		- ViewModel的基类遵守了`WebComponent`协议，而`WebComponent`协议是继承了`WebComponentDelegate `协议，所以在ViewModel中可以直接实现该协议方法即可。
		- 所以调用js后的回调都是通过相同的协议方法处理，所以在实现方法时要通过判断handler来进行对应处理responseData。

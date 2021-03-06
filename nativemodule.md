[打开模块工程]: ./assets/module_s1.png
[头文件内容]: ./assets/module_s2.png
[模块定义类]: ./assets/module_s3.png


## 说明
1、模块工程都是基于EagleSDK进行开发。所以dependency.default中默认配置EagleSDK。    
2、模块工程开发完成后以静态库的形式进行发布。参考模块化的思想，每个模块都提供了Demo工程，为开发时提供各自测试开发环境，方便模块单独进行开发和升级。



## 创建NativeModule工程

### 创建工程
* 从平台创建模块工程，使用Xcode打开静态库Demo工程,开发的工程目录在 `Development Pods`这个文件夹下面。  

***
<div align=center>
![打开模块工程]
</div>


## Module的声明  
>_模块的代码文件都必须保证在静态库文件夹下面的`Classes`文件夹内,否则代码不会被编译。资源文件建议放在`Assets`文件夹中，创建静态库的时候以bundle方式打包。_ 

#### 1.静态库头文件  
* 头文件的目的是暴露静态库的外部接口,所以只要创建一个`.h`的头文件，
***
<div align=center>
![头文件内容] 
</div>

>1、头文件里面的内容就是将模块内创建的`.h`文件`import`。  
>2、为加快编译速度工程配置使用了预编译功能，头文件中的内容会进行预编译，这个配置在`podspec`文件中。

#### 2.静态库模块声明类  
* 通过Cocoa Touch Class创建一个类,**命名必须遵守`(ModuleName)_Module`的规范**，一个模块只能有一个模块声明类。
* **模块的定义类必须继承`EGModule`基类并且遵守`EGModuleExport`协议。**

***
<div align=center>
![模块定义类]  
</div>

* 在声明类的`implementation`中，需要调用宏`RegistModule()`，进行模块注册。

* 实现协议方法，`EGModuleExport`协议中提供了模块声明应该实现的接口  

```
//定义模块启动的主入口路由地址
+(NSString *)mainRoute;

//对于多入口的模块，提供一个入口地址的数组
+(NSArray *)exportDefault;

//在模块开发的Demo工程中启动模块需要加载的控制器
+(UIViewController *)exportAppLoader;

//模块在启动时需要注入的参数
+(void)moduleInitializeWithOptions:(NSDictionary *)launchOptions mode:(id)mode;
```

>Example
>
>```
>#import "LMSPCommonWeb_Module.h"
>#import "LMSPCommonWebViewController.h"
>@implementation LMSPCommonWeb_Module
>RegistModule()
>NSString * const LMSPCommonWeb=@"LMSPCommonWeb";
>+(NSString *)mainRoute{
    return LMSPCommonWeb_LMSPCommonWebViewController_URL;
}
>+(NSArray *)exportDefault{
    return @[LMSPCommonWeb_LMSPCommonWebViewController_URL];
}
>+(UIViewController *)exportAppLoader{
    EGRootNavigationController *navi=[[EGRootNavigationController alloc]initWithRootViewController:[[LMSPCommonWebViewController alloc]init]];
    return navi;
}
>+ (void)moduleInitializeWithOptions:(NSDictionary *)launchOptions mode:(id)mode{
    [super moduleInitializeWithOptions:launchOptions mode:mode];
    NSMutableDictionary *dict=[NSMutableDictionary dictionaryWithDictionary:[UIApplication sharedApplication].appParams];
    [dict setValuesForKeysWithDictionary:@{@"configurations": @{
                                                   @"login": @{
                                                           @"loginUrl": @"eagle://lmsplogin/lmsploginnativemodule",
                                                           @"isLoginAlways": @(YES)
                                                           }}}];
    [UIApplication sharedApplication].appParams=[NSDictionary dictionaryWithDictionary:dict];
}
>@end
>```


<span id="macros"></span>
#### 3.静态库模块宏和常量定义文件

* 定义的方式有2种：

1、只使用`.h`文件进行定义  
>Example
>
>```
>#ifndef LMSPCommonUI_macros_h
>#define LMSPCommonUI_macros_h
>
>#ifndef EG_EXTERN
>#define EG_EXTERN extern
>#endif
>
>#pragma mark -- macros
>#define LMSPCommonUI_Xibs   @"LMSPCommonUI_Xibs"
>#define LMSPCommonUI_Bundle [NSBundle bundleWithPath:[[NSBundle bundleForClass:[LMSPCommonUI_Module class]] pathForResource:(LMSPCommonUI_Xibs) ofType:@"bundle"]]
>
>#pragma mark -- keys
>EG_EXTERN NSString * const LMSPCommonUI;
>EG_EXTERN NSString * const LMSPCommonUI_LMSPCommonUIViewController_URL;
>#define LMSPCommonUI_Xibs   @"LMSPCommonUI_Xibs"
>typedef void(^LMSPCommonUICompleteBlock)(NSString *option);
>
>#pragma mark -- LMSPAlertMenu
>typedef LMSPCommonUICompleteBlock LMSPAlertMenuCompleteBlock;
>
>#pragma mark -- LMSPApproveMenu
>typedef LMSPCommonUICompleteBlock LMSPApproveMenuCompleteBlock;
>
>#pragma mark -- LMSPSegmentHeader
>typedef NS_ENUM(NSInteger,LMSPSegmentHeaderActive) {
    LMSPSegmentHeaderLeft=1000,
    LMSPSegmentHeaderRight=1001
};
>typedef void(^SubscribeBlock)(NSString *channel);
>
>#pragma mark -- themes
>#define LMSP_Button_Selected_Color  @"#333333"
>#define LMSP_Button_UnSelected_Color  @"#999999"

>#endif /* LMSPCommonUI_macros_h */
>```
> * 适合定义宏，以及类型定义,常量定义的赋值需要在其他类的实现中进行。

2、使用`.h`和`.m`文件进行定义  
在模块中使用了路由，为了集中管理配置路由，推荐使用这种方式来定义宏和常量。
* 在`.h`文件中定义常量和宏

>Example
>
>```
>#import <Foundation/Foundation.h>

>#ifndef EG_EXTERN
>#define EG_EXTERN extern
>#endif

>#pragma mark -- keys
EG_EXTERN NSString * const IBMessage;
EG_EXTERN NSString * const IBMessage_Module_URL;
EG_EXTERN NSString * const IBMessage_IBMessageViewController_URL;
EG_EXTERN NSString * const IBMessage_LMSPAlreadyToDoViewController_URL;     //待办已办
EG_EXTERN NSString * const IBMessage_LMSPVoteViewController_URL;            //投票
EG_EXTERN NSString * const IBMessage_LMSPAlreadyToReadViewController_URL;   //待阅已阅
EG_EXTERN NSString * const IBMessage_LMSPNoticeViewController_URL;          //待阅已阅
EG_EXTERN NSString * const IBMessage_LMSPMyFlowViewController_URL;          //我的流程
EG_EXTERN NSString * const IBMessage_LMSPFlowDetailViewController_URL;      //流程详情
EG_EXTERN NSString * const IBMessage_LMSPApproveInputViewController_URL;    //审批输入
EG_EXTERN NSString * const IBMessage_LMSPAccessoryListViewController_URL;   //附件列表
EG_EXTERN NSString * const IBMessage_LMSPContactListViewController_URL;     //联系人列表
EG_EXTERN NSString * const IBMessage_LMSPSearchResultViewController_URL;     //联系人列表
EG_EXTERN NSString * const IBMessage_LMSPCommunicateViewController_URL;     //沟通列表
EG_EXTERN NSString * const IBMessage_LMSPCommunicateDetailViewController_URL;//沟通详情


>#ifndef IBMessage_Xibs
>#define IBMessage_Xibs  @"IBMessage_Xibs"
>#endif

>#ifndef HexColor
>#define HexColor(x)     ([UIColor colorWithHexString:(x)])
>#endif

>#ifndef EmptyVal
>#define EmptyVal(x)         emptyStr((x))
>#endif

>static inline NSString *emptyStr(id target){
    if (target!=nil&&![target isKindOfClass:[NSNull class]]) {
        if ([target isKindOfClass:[NSString class]]) {
            NSString *str=(NSString *)target;
            return str.length>0?str:@" ";
        }
        return @" ";
    }else{
        return @" ";
    }
}

>```

* 在`.m`文件中对常量进行赋值  

> Example
> 
> ```
> #import "IBMessage_Constant.h"

>NSString * const IBMessage=                                     @"IBMessage";
NSString * const IBMessage_Module_URL                           =@"eagle://ibmessage/ibmessagenativemodule";
NSString * const IBMessage_IBMessageViewController_URL          =@"eagle://ibmessage/ibmessagenativemodule/home";
NSString * const IBMessage_LMSPAlreadyToDoViewController_URL    =@"eagle://ibmessage/ibmessagenativemodule/alreadytodo";     //待办已办
NSString * const IBMessage_LMSPVoteViewController_URL           =@"eagle://ibmessage/ibmessagenativemodule/vote";            //投票
NSString * const IBMessage_LMSPAlreadyToReadViewController_URL  =@"eagle://ibmessage/ibmessagenativemodule/alreadytoread";   //待阅已阅
NSString * const IBMessage_LMSPNoticeViewController_URL         =@"eagle://ibmessage/ibmessagenativemodule/notice";          //待阅已阅
NSString * const IBMessage_LMSPMyFlowViewController_URL         =@"eagle://ibmessage/ibmessagenativemodule/myflow";          //我的流程
NSString * const IBMessage_LMSPFlowDetailViewController_URL     =@"eagle://ibmessage/ibmessagenativemodule/flowdetail";      //流程详情
NSString * const IBMessage_LMSPApproveInputViewController_URL   =@"eagle://ibmessage/ibmessagenativemodule/approveinput";    //审批输入
NSString * const IBMessage_LMSPAccessoryListViewController_URL  =@"eagle://ibmessage/ibmessagenativemodule/accessorylist";   //附件列表
NSString * const IBMessage_LMSPContactListViewController_URL    =@"eagle://ibmessage/ibmessagenativemodule/contactlist";     //联系人列表
NSString * const IBMessage_LMSPSearchResultViewController_URL   =@"eagle://ibmessage/ibmessagenativemodule/searchresult";    //搜索结果
NSString * const IBMessage_LMSPCommunicateViewController_URL    =@"eagle://ibmessage/ibmessagenativemodule/communicate";     //沟通列表
NSString * const IBMessage_LMSPCommunicateDetailViewController_URL=@"eagle://ibmessage/ibmessagenativemodule/communicate/detail";//沟通详情
> ```

## 创建ViewController
1、在`Controllers`文件夹内创建Controller类。在模块的常量定义文件中定义该Controller的路由地址。[参考这里](#macros) 
>不推荐在Controller的`.m`中定义路由地址，路由地址必须唯一。
 
2、在Controller的`.m`文件中进行模块的路由绑定。
> * 利用宏`moduleMapRoute`将模块和Controller的路由进行绑定  
	`moduleMapRoute(IBMessage, IBMessage_IBMessageViewController_URL)`  
> * `IBMessage`是定义的模块名称，`IBMessage_IBMessageViewController_URL `是定义的Controller的路由地址，路由地址的scheme必须是eagle。  

3、在Controller中定义导航栏样式。
>*不建议在控制器中添加其他代码逻辑，使得控制器过重。*


## 创建Component
1、在`Components`文件夹内创建Component类。Component必须继承`EGComponent`的基类
 
2、ViewController中页面级组件的拼装。
在`Configurations`文件夹中创建与ViewController同名的json文件。  
>*ViewController.json*

```json
{
    "components": [{
            "IBMessageComponent": {
                "size": {
                    "usePercent": true,
                    "horizontal": 1,
                    "vertical": 1,
                    "width": 0,
                    "height": 0,
                    "width-full": true
                },
                "flex": "FlexCloum",
                "eg_id": "IBMessageComponent",
                "eg_tag": "IBMessageComponent",
                "dataSource":{
                  
                   }
            }
        }
    ],
    "viewcontroller": "IBMessageViewController",
}
```  
3、在`-(void)componentInit`的生命周期方法中，初始化组件内部的view和相关数据加载。  
4、在启动后，EagleSDK会根据json配置信息自动加载相应组件。  
5、Component与ViewModel的解耦方式为:Component提供一个与Component同名的protocol，定义ViewModel必须提供的数据源属性和相关方法。Component使用时通过runtime进行动态调用，ViewModel与组件通信时可以通过协议方法将组件传入，不能传入时可以通过事件总线进行通信，最后通过组件直接调用组件内部方法更新组件。    

* Example

>```ObjectiveC
@class IBMessageComponent;
@protocol IBMessageComponent<NSObject>
@property(nonatomic,strong)NSMutableArray *topDataSource;
@property(nonatomic,strong)NSMutableArray *bottomChatDataSource;
-(void)refreshMyCount:(IBMessageComponent *)component;
@end
@interface IBMessageComponent : EGComponent
-(void)reloadData;
@end
>```

6、Component可以在加载时通过json配置的dataSource，可以通过``这个协议方法实现配置方式的数据注入      

```ObjectiveC
@protocol EGComponentJSONDataSourceInjectable<NSObject>

/**
 需要支持json注入数据源的component需要实现该方法
 
 @param jsonDataSource json数据
 */
-(void)injectJSONDataSource:(id)jsonDataSource;

@end
```

## 创建ViewModel

1、在`ViewModels`文件夹中创建ViewModel类，继承EGViewModel基类。  
2、ViewModel的作用是提供数据源和获取数据的相关方法，对于Controller中配置了一个或者多个组件时，ViewModel要遵守所有组件的协议。
  
* Example

>```ObjectiveC
@interface IBMessageViewModel : EGViewModel<EGViewModel,IBMessageComponent>
@property(nonatomic,strong)NSMutableArray *topDataSource;
@property(nonatomic,strong)NSMutableArray *bottomChatDataSource;
-(void)refreshMyCount:(IBMessageComponent *)component;
@end
>```

3、通过宏`BindViewModelToController `将ViewModel与Controller进行绑定。  
>* `BindViewModelToController(IBMessage, @"IBMessageViewController")`  
>*其中`IBMessage`为模块名称，`@"IBMessageViewController"`为Controller的类名*

4、ViewModel只是提供数据，所以没有设计相关生命周期，具体ViewModel的绑定流程参考指南中ViewModel的绑定机制。另外，ViewModel在设计时采用了去Model化的理念，所以数据都是字典或者数组的形式。

## 创建View 

1、在`Views`文件夹中创建View类。  
2、考虑代码复用，建议使用一个UI的静态库，创建需要复用的View，然后NativeModule添加对UI静态库的依赖。  
3、使用xib创建的View，xib文件作为一种资源，需要在静态库打包的时候以bundle的形式保存在静态库。([参考静态库](/framework))  
4、Component或者其他需要加载View中xib的情况，需要通过获取xib所在bundle来进行加载。  



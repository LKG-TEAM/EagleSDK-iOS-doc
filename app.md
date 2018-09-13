# 应用

## 创建应用工程
通过平台创建应用工程，添加相关模块的依赖。

### 应用的种类
由平台创建的应用，支持3种App框架。    

- 1.常规Tabbar样式
- 2.Tabbar带➕按钮样式
- 3.侧滑菜单样式
- 4.单模块样式

## 配置文件
配置文件存放在工程的Resources文件夹下，`guide.bundle`是引导页，`app.json`是应用配置信息，`bindMap.plist`是配置Controoler和ViewModel的绑定。
### app.json

>Example
>
>```json
>{
    "nativeapp":
    {
        "urls": [
            "eagle://ib/ibmessage/ibmessagenativemodule?icon=71e&title=消息&color=#9b9b9b&selectColor=#000000&extraPath=#message&#a=1"  ,
      		"eagle://ib/lmspcommonweb/lmspcommonwebwebmodule?icon=721&title=项目&color=#9b9b9b&selectColor=#000000&extraPath=#/project&#a=1"  ,
      		"eagle://ib/lmspapplication/lmspapplicationnativemodule?icon=722&title=应用&color=#9b9b9b&selectColor=#000000&extraPath=#app&#a=1"  ,
      		"eagle://ib/lmspmine/lmspminenativemodule?icon=725&title=我的&color=#9b9b9b&selectColor=#000000&extraPath=#mine&#a=1"
        ],
        "type": 2,
        "extra":
        {
            "type": "add",
            "style":
            {
                "subType": "round",
                "title": "扫码•支付",
                "backgroundColor": "#FED037",
                "foregroundColor": "#ffffff",
                "image": "return_product_icon"
            },
            "extraParams": [
            {
                "name": "测试1",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            },
            {
                "name": "测试2",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            },
            {
                "name": "测试3",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            },
            {
                "name": "测试4",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            },
            {
                "name": "测试5",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            },
            {
                "name": "测试6",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            },
            {
                "name": "测试7",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            },
            {
                "name": "测试8",
                "icon": "gaealogor",
                "url": "eagle://common/eglogin/egloginnativemodule"
            }]
        }
    },
    "webapp": null,
    "configurations":
    {
        "baseURL": "http://192.168.2.51:8090/ibf",
        "bugly":
        {
            "appId": "544b983bbc"
        },
        "shareSDK":
        {
            "umeng": "5ad04358f29d984ab7000078",
            "platforms":
            {
                "weibo":
                {
                    "appKey": "214686774",
                    "appSecret": "",
                    "redirectURL": "https://api.weibo.com/oauth2/default.html"
                },
                "wechat":
                {
                    "appKey": "wx419c24f499ccbdc8",
                    "appSecret": "0fa604d68a9b954aece11420d043788b",
                    "redirectURL": ""
                },
                "tencent":
                {
                    "appKey": "1105878485",
                    "appSecret": "",
                    "redirectURL": ""
                }
            }
        },
        "guide": [
            "闪屏1.png",
            "闪屏2.png",
            "闪屏3.png",
            "闪屏4.png"
        ],
        "login":
        {
            "loginUrl": "eagle://lmsplogin/lmsploginnativemodule",
            "isLoginAlways": false,
            "loginIcon": "gaealogor",
            "loginCopyRight": "string",
            "loginTitle": "loginTitle",
            "api":
            {
                "env": "dev",
                "dev":
                {
                    "login": "https://www.baidu.com",
                    "verify": "https://www.baidu.com"
                },
                "product":
                {
                    "login": "https://www.baidu.com",
                    "verify": "https://www.baidu.com"
                }
            }
        }
    }
}

>```

### 字段含义

| 键名  | 类型  | 父级 | 说明 |
|:-- 	| :--: | :--: | :-- |
| nativeapp     	| object 			|    -     |表示是原生应用|
| urls     		| array 			| nativeapp |Tabbar样式中tabbarItem的路由|
| type     		| number 			| nativeapp |app框架样式:<br/>-1:单模块样式，0:侧滑菜单样式，1:常规Tabbar样式，2:Tabbar带➕按钮样式|
| extra     		| object 			| nativeapp |Tabbar带➕按钮样式下，弹出菜单配置信息|
| style     		| object 			| extra     |Tabbar带➕按钮样式下，➕按钮样式|
| subType     	| object 			| style     |'round':圆形按钮，'':默认方形按钮|
| title	     	| object 			| string    |圆形按钮下的标题|
|backgroundColor	| object 			| style     |背景色|
|foregroundColor	| object 			| style     |➕颜色|
| image 			| object 			| style     |按钮背景图片|
| extraParams 	| array 			| extra     |Tabbar中➕按钮弹出菜单配置信息|
| name 			| string 			| extraParams|菜单Item显示标题|
| icon 			| string 			| extraParams|菜单Item显示图标|
| url 				| string 			| extraParams |菜单Item跳转的路由|
| webapp      	| object        	|      -      |表示是Web应用|
| configurations	| object        	|     -     |其他配置信息|
| baseURL			| string        	| configurations |项目的baseURL|
| bugly			| object        	| configurations |项目的baseURL|
| appId			| string        	| bugly |Bugly的appId信息|
| shareSDK		| object        	| configurations |友盟分享SDK的配置信息|
| umeng			| string        	| shareSDK |友盟分享SDK的appId|
| platforms		| object        	| shareSDK |分享平台的配置信息|
| weibo			| object        	| platforms |微博的配置信息|
| wechat			| object        	| platforms |微信的配置信息|
| tencent			| object        	| platforms |腾讯的配置信息|
| appKey			| string        	| weibo，wechat，tencent | appKey |
| appSecret		| string        	| weibo，wechat，tencent | appSecret |
| redirectURL		| string        	| weibo，wechat，tencent | redirectURL |
| guide			| array        	| configurations | 引导页加载 |
| login 			| object        	|  configurations  |登录模块配置信息|
| isLoginAlways 	| boolean 		|  login  |是否在启动时进行登录|


## 模块联调
参考静态库中[Cocoapods的使用](/framework?id=cocoapods的使用)

## 证书配置
请从平台上传和管理证书。

## 打包发布
请使用平台编译打包和发布。
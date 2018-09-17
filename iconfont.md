[font-family]:./assets/iconfont_s1.png
[font-name]:./assets/iconfont_s2.png

# 如何使用图标库
## 关于图标库
* 图标库使用iconfont作为图标来源，通过图标库的静态库在使用时动态懒加载字体库。作为字体库使用，**必须要用font-family，并且是唯一的，否则后加载的会覆盖前面的字体。**所以建议在创建iconfont的时候，有必要设置一个唯一的font-family。

![font-family]

* 应用工程或者静态库Demo工程引入图标库，在PrefixHeader.pch中使用@import将图标库引入。

* 引入图标库后需要修改图标库的fontName
	* 方式一:通过在`application:didFinishLaunchingWithOptions:`方法里面设置fontName。
		>```
		>- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    		self.window=[[UIWindow alloc]initWithFrame:[UIScreen mainScreen].bounds];
    		[self.window makeKeyAndVisible];
    		[[UIApplication sharedApplication]setFontName:@"LMSPDemo"];
    		[UIApplication sharedApplication].preloadON=NO;
    		[LMSPDemo setFontName:@"am-icon_lmsp"];
		#if DEBUG
    		BOOL debug=YES;
		#else
		    BOOL debug=NO;
		#endif
    		[EGSDKLoader loadSDKWithOptions:launchOptions debug:debug];
   			return YES;
}
		>```
		
	* 方式二:修改静态库的同名类`.m`文件，设置fontName。
	>![font-name]

## 使用常规代码

- 图标库提供了一个含有图标库名称的宏`(图标库名称)InfoMake(text, imageSize, imageColor)`用来创建图标的UIImage对象。**text必须使用@"\U0000XXXX"这样的格式.**
	- 1.使用宏
			
		>示例代码(图标库名称是`LMSPDemo`)
		>
		>```
UIImage *img=[UIImage iconWithInfo:LMSPDemoInfoMake(@"\U0000e699", 25, [UIColor redColor])];
		>```

	- 2.使用分类方法
		>示例代码(图标库名称是`LMSPDemo`)
		>
		>```
UIImage *img=[LMSPDemo iconFontFromName:@"&#xe699" color:@"#00ff00" size:25];
		>```

## 快捷使用
- 我们提供了一个更便捷的方法使用图标库，使用url的方式提供iconfont的font、size和color信息，比如`iconfont://&#xe699/25/redColor`。url的scheme必须使用iconfont，color支持RGB的Hex字符串(使用时不要添加`#`)和`UIColor`的颜色类方法。

>示例代码
>```
UIImage *img=[UIImage iconWithInfo:@"iconfont://&#xe705/18/blackColor" fontName:[UIApplication sharedApplication].fontName];
>```

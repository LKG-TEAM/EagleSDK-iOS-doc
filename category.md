# 分类方法
## 组成
```
├── EGComponent+Xib
├── EGModule+Resource
├── EGURL+Extension
├── NSInvocation+Proxy
├── NSObject+EGMediator
├── NSString+DateHelper
├── NSString+EGUtil
├── NSString+SafeBase64
├── UIApplication+App
├── UIApplication+Config
├── UIButton+IconFont
├── UIButton+Radius
├── UIButton+RoundCorner
├── UIImage+Color
├── UIImage+Font
├── UIImageView+Radius
├── UILabel+Radius
├── UINavigationItem+LeftItem
├── UIView+BlankPage
├── UIViewController+BlankPage
└── UIViewController+Hud
```

## 常用Class和API
- [EGComponent+Xib](#xib)
- [EGModule+Resource](#resource)
- [EGURL+Extension](#extension)
- [NSObject+EGMediator](#mediator)
- [NSString+DateHelper](#datehelper)
- [NSString+EGUtil](#util)
- [NSString+SafeBase64](#safebase64)
- [UIApplication+App](#app)
- [UIApplication+Config](#config)
- [UIButton+IconFont](#iconfont)
- [UIButton+Radius](#radius)
- [UIButton+RoundCorner](#roundcorner)
- [UIImage+Color](#color)
- [UIImage+Font](#font)
- [UIImageView+Radius](#radius)
- [UILabel+Radius](#labelradius)
- [UINavigationItem+LeftItem](#leftitem)
- [UIView+BlankPage](#blankpage)
- [UIViewController+BlankPage](#vcblankpage)
- [UIViewController+Hud](#hud)

### EGComponent+Xib<span id="xib"></span>
```
//加载模块中Component同名xib资源
-(UIView *)loadNibFor:(Class)clazzName inModule:(NSString *)moduleName;
//加载模块中xib资源
-(UIView *)loadNibWithName:(NSString *)nibName forClass:(Class)clazzName inModule:(NSString *)moduleName;

```
### EGModule+Resource<span id="resource"></span>
```
//模块根据类名加载本模块中的xib资源
-(UIView *)loadFromNibFor:(Class)clazzName;
```
### EGURL+Extension<span id="extension"></span>
```
//EGURL路径添加参数
-(void)addParams:(NSDictionary *)params;
//根据路由获取视图控制器的名称
-(NSString *)getViewControllerName;
```
### NSObject+EGMediator<span id="mediator"></span>
```
/**
 触发target的方法
 @param actionStr 方法字符串
 */
- (BOOL)eg_performSelector:(id)target actionStr:(NSString *)actionStr withObject:(id)object;
```
### NSString+DateHelper<span id="datehelper"></span>
```
/**
    时间转换		格式
    超过两天以上   yyyy-MM-dd HH:mm:ss
    昨天          昨天HH:mm:ss
    今天          x小时前
 @param timeStamp 时间戳，单位:秒
 @return 描述字符串
 */
+ (NSString *)stringWithTimeStamp:(NSTimeInterval)timeStamp;
```
### NSString+EGUtil<span id="util"></span>
```
/**
 计算字符串宽度(指定高度)
 @param str        传入的字符串
 @param height     传入的高度
 @param font       字体
 @param fontSize   fontSize
 @return           字符串宽度
 */
+ (CGFloat)eg_getStrWidth:(NSString *)str height:(CGFloat)height font:(UIFont *)font fontSize:(CGFloat)fontSize;

/**
 计算字符串高度(指定宽度)
 @param str         传入的字符串
 @param width       传入的宽度
 @param font        字体
 @param fontSize    fontSize
 @return            字符串高度
 */
+ (CGFloat)eg_getStrHeight:(NSString *)str width:(CGFloat)width font:(UIFont *)font fontSize:(CGFloat)fontSize;

/**
 计算字符串尺寸
 @param str         传入的字符串
 @param font        字体
 @param fontSize    fontSize
 @return            字符串尺寸
 */
+ (CGSize)eg_getStrSize:(NSString *)str font:(UIFont *)font fontSize:(CGFloat)fontSize;

// 将数组形式的字段转换成keypath形式
+ (NSString *)makeKeyPath:(id)keys;
```
### NSString+SafeBase64<span id="safebase64"></span>
```
// 使用Base64转码，对‘+’、‘=’、‘/’特殊字符进行替换
-(NSString *)encodeSafeBase64;
// 使用Base64解码
-(NSString *)decodeSafeBase64;
```
### UIApplication+App<span id="app"></span>
```
// App版本检查
-(void)versionCheck;
```

### UIApplication+Config<span id="config"></span>
```
/**   Params from app.json   */
@property(nonatomic,strong)NSDictionary *appParams;
/**   Params from settings.json   */
@property(nonatomic,strong)NSDictionary *settingParams;
/**   Fontname import   */
@property(nonatomic,copy)NSString *fontName;

@property (nonatomic, strong) NSDictionary *urlParams;

/**   Confiuration Params from app.json   */
-(NSDictionary *)configurations;

/**
 Read Settings.json with keyPath
 @param keyPath keyPath
 @return value
 */
-(id)valueForKeyPathInSettings:(NSString *)keyPath;
```
### UIButton+IconFont<span id="iconfont"></span>
```
-(void)setIconFont:(NSString *)iconfont forState:(UIControlState)state;
```

### UIButton+Radius<span id="radius"></span>
```
-(void)eg_setRadius:(CGFloat)radius;
```
### UIButton+RoundCorner<span id="roundcorner"></span>
```
-(void)eg_setRadius:(CGFloat)radius;
-(void)eg_setRoundCorner;
```
### UIImage+Color<span id="color"></span>
```
+(instancetype)imageWithColor:(UIColor *)color;
+(instancetype)imageWithColor:(UIColor *)color andSize:(CGSize)size;
+ (UIImage*) imageWithColo:(UIColor*)color andHeight:(CGFloat)height;
```
### UIImage+Font<span id="font"></span>
```
+ (UIImage *)iconWithInfo:(NSString *)info fontName:(NSString *)fontName;
```
### UIImageView+Radius<span id="radius"></span>
```
-(void)eg_setRadius:(CGFloat)radius;
-(void)eg_setRoundCorner;
```
### UILabel+Radius<span id="labelradius"></span>
```
-(void)eg_setRadius:(CGFloat)radius;
-(void)eg_setRoundCorner;
```
### UINavigationItem+LeftItem<span id="leftitem"></span>
```
@property(nonatomic,weak)UIViewController *containerVC;
-(void)eg_setLeftBarButtonItem:(UIBarButtonItem *)leftItem;
```
### UIView+BlankPage<span id="blankpage"></span>
```
- (void)configBlankPage:(EGBlankPageType)blankPageType hasData:(BOOL)hasData hasError:(BOOL)hasError reloadButtonBlock:(void(^)(id sender))block customButtonBlock:(void(^)(UIButton *sender))customButtonBlock;
-(void)setBlankPageWithStatusCode:(NSInteger)statusCode hasData:(BOOL)hasData hasError:(BOOL)hasError reloadButtonBlock:(void(^)(id sender))block customButtonBlock:(void(^)(UIButton *sender))customButtonBlock;
```
### UIViewController+BlankPage<span id="vcblankpage"></span>
```
- (void)configBlankPage:(EGBlankPageType)blankPageType hasData:(BOOL)hasData hasError:(BOOL)hasError reloadButtonBlock:(void(^)(id sender))block customButtonBlock:(void(^)(UIButton *sender))customButtonBlock;
-(void)setBlankPageWithStatusCode:(NSInteger)statusCode hasData:(BOOL)hasData hasError:(BOOL)hasError reloadButtonBlock:(void(^)(id sender))block customButtonBlock:(void(^)(UIButton *sender))customButtonBlock;
```
### UIViewController+Hud<span id="hud"></span>
```
-(void)showHUDWithMsg:(NSString *)msg;
-(void)showHUDWithMsg:(NSString *)msg duration:(CGFloat)duration;

-(void)showInfoHUDWithMsg:(NSString *)msg;
-(void)showInfoHUDWithMsg:(NSString *)msg duration:(CGFloat)duration;

-(void)hideHUDWithComplete:(void(^)(void))completeBlk;
-(void)hideHUD;

-(void)showError:(NSString *)msg;
-(void)showSucces:(NSString *)msg;


+(void)showHUDWithMsg:(NSString *)msg;
+(void)showHUDWithMsg:(NSString *)msg duration:(CGFloat)duration;

+(void)showInfoHUDWithMsg:(NSString *)msg;
+(void)showInfoHUDWithMsg:(NSString *)msg duration:(CGFloat)duration;

+(void)hideHUDWithComplete:(void(^)(void))completeBlk;
+(void)hideHUD;

+(void)showError:(NSString *)msg;
+(void)showSucces:(NSString *)msg;
```


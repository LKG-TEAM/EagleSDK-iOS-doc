# EGUserSession
## API
```
+(instancetype)currentSession;

//从当前登录中获取用户信息
-(NSDictionary *)getUserFromSession;

//从NSUserDefaults中获取用户信息
-(NSDictionary *)getUserInfo;

-(void)saveUserInfo:(NSDictionary *)userInfo;

-(id)getCookieFromSession;

//网络请求成功后保存返回的Cookie
-(void)saveCookie:(EGBaseRequest *)request;

+(void)clearSession;
```

## Channel
```
//保存完Cookie后发布
#define EGUserSession_Cookie_Channel  @"EGUserSession_Cookie_Channel"
```

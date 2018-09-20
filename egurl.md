# EGURL

## EGURL的属性
```
@property(nonatomic,assign)EGURLScheme schemeType;
@property(nonatomic,copy)NSString *scheme;
@property(nonatomic,copy)NSString *host;
@property(nonatomic,copy)NSString *path;
@property(nonatomic,strong)NSArray *pathComponents;
@property(nonatomic,strong)NSDictionary *query;
@property(nonatomic,copy)NSString *queryString;
@property(nonatomic,copy)NSString *relativePath;
@property(nonatomic,copy)NSString *absolutePath;

/** Is a hash path. */
@property(nonatomic,assign)BOOL isHashURL;
/** Value that a hash path carries. */
@property(nonatomic,copy)NSString *hashPathValue;
```

## 常用API
```
-(instancetype)initWithString:(NSString *)urlString;
-(NSString *)queryObj:(NSString *)key;

/**
 Module name with lowercase.
 @return Module name.
 */
-(NSString *)moduleName;
```

### Catagory
```
/** 路由添加参数. */
-(void)addParams:(NSDictionary *)params;

/**
 通过路由查找对应的控制器
 @return Controller name.
 */
-(NSString *)getViewControllerName;
```

## 注意点
- 哈希路由的约定:`/#!/`。
- 模块路由的约定:
	- NativeModule：以`nativemodule`为path的后缀。
	- WebModule：以`webmodule`为path的后缀。
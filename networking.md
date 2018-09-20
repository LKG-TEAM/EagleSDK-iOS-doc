# Networking

## 组成
```
├── EGBaseRequest
├── EGBatchRequest
├── EGBatchRequestAgent
├── EGChainRequest
├── EGChainRequestAgent
├── EGHttpSessionManager
├── EGNetwork
├── EGNetworkAgent
├── EGNetworkConfig
├── EGNetworkManager
├── EGNetworkPrivate
└── EGRequest
```

## 常用Class和API
- [EGBaseRequest](#baser)		
- [EGBatchRequest](#batchr)		
- [EGChainRequest](#chainr)		
- [EGHttpSessionManager](#session)		
- [EGNetworkManager](#netm)		
- [EGRequest](#egr)

### EGBaseRequest<span id="baser"></span>
```
///  Convenience method to start the request with block callbacks.
- (void)startWithCompletionBlockWithSuccess:(nullable EGRequestCompletionBlock)success
                                    failure:(nullable EGRequestCompletionBlock)failure;
```
- 使用时创建子类，重写父类方法。

### EGBatchRequest<span id="batchr"></span>
```
///  Creates a `EGBatchRequest` with a bunch of requests.
///  @param requestArray requests useds to create batch request.
- (instancetype)initWithRequestArray:(NSArray<EGRequest *> *)requestArray;

///  Convenience method to start the batch request with block callbacks.
- (void)startWithCompletionBlockWithSuccess:(nullable void (^)(EGBatchRequest *batchRequest))success
                                    failure:(nullable void (^)(EGBatchRequest *batchRequest))failure;
```		
- 并发请求，每个EGRequest的请求可以使用自己的代理回调。

### EGChainRequest<span id="chainr"></span>
```
///  Start the chain request, adding first request in the chain to request queue.
- (void)start;

///  Stop the chain request. Remaining request in chain will be cancelled.
- (void)stop;

///  Add request to request chain.
///  @param request  The request to be chained.
///  @param callback The finish callback
- (void)addRequest:(EGBaseRequest *)request callback:(nullable EGChainCallback)callback;
```
- 用来管理相互有依赖的网络请求。
		
### EGHttpSessionManager<span id="session"></span>
```
+ (instancetype)manager;

- (void)GET:(NSString *)URLString parameters:(nullable id)parameters
   progress:(nullable void (^)(NSProgress *downloadProgress))downloadProgress
    success:(nullable void (^)(NSURLSessionTask *task, id _Nullable responseObject))success
    failure:(nullable void (^)(NSURLSessionTask * _Nullable task, NSError *error))failure;

- (void)POST:(NSString *)URLString parameters:(nullable id)parameters
    progress:(nullable void (^)(NSProgress *uploadProgress))uploadProgress
     success:(nullable void (^)(NSURLSessionTask *task, id _Nullable responseObject))success
     failure:(nullable void (^)(NSURLSessionTask * _Nullable task, NSError *error))failure;

//Silent request without svprogress show.
- (void)silentGET:(NSString *)URLString parameters:(nullable id)parameters
         progress:(nullable void (^)(NSProgress *downloadProgress))downloadProgress
          success:(nullable void (^)(NSURLSessionTask *task, id _Nullable responseObject))success
          failure:(nullable void (^)(NSURLSessionTask * _Nullable task, NSError *error))failure;

- (void)silentPOST:(NSString *)URLString parameters:(nullable id)parameters
          progress:(nullable void (^)(NSProgress *uploadProgress))uploadProgress
           success:(nullable void (^)(NSURLSessionTask *task, id _Nullable responseObject))success
           failure:(nullable void (^)(NSURLSessionTask * _Nullable task, NSError *error))failure;

- (EGHttpSessionManager *(^)(EGRequestSerializerType type))requestSerializerType;
- (EGHttpSessionManager *(^)(EGResponseSerializerType type))responseSerializerType;
- (EGHttpSessionManager *(^)(NSDictionary<NSString *,NSString *> *requestHeaderFieldValue))requestHeaderFieldValueDictionary;
```
- 封装了发送网络请求的快捷方法，silent请求时不显示提示。
- 可以设置请求的类型，响应的类型，请求头。
		
### EGNetworkManager<span id="netm"></span>
```
@property (readonly, nonatomic, assign) EGNetworkReachabilityStatus networkReachabilityStatus;

+(instancetype)manager;
-(void)listenNetwork;
```
- 监听网络状态的变化。
- Channel

>```
//网络状态变化时发布
#define EGNetworkReachabilityStatus_Change_Channel @"com.linkstec.egnetworkmanager.egnetworkreachabilityStatus"
>```

- 网络状态代码

>```
typedef NS_ENUM(NSInteger, EGNetworkReachabilityStatus) {
    EGNetworkReachabilityStatusUnknown          = -1,
    EGNetworkReachabilityStatusNotReachable     = 0,
    EGNetworkReachabilityStatusReachableViaWWAN = 1,
    EGNetworkReachabilityStatusReachableViaWiFi = 2,
};
>```
		
### EGRequest<span id="egr"></span>

- 继承自`EGBaseRequest`，添加了本地缓存(下载的请求不会缓存)。
- 使用时创建子类。
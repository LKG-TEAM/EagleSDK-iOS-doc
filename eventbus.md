# EventBus

## EventBus的组成
```
├── EGEvent
├── EGEventBus
├── EGEventContext
├── EGEventDispatcher
├── EGEventExcuter
└── NSObject+EventBus
```

## 常用Class和API

### 常用类		
[EGEvent](#event)	
[EGEventBus](#eventbus)		
[EGEventContext](#eventcontext)			
[EGEventDispatcher](#eventdispatch)		
[EGEventExcuter](#eventexcuter)		
[NSObject+EventBus](#eventobj)

### EGEvent <span id="event"></span>
```
@property(nonatomic,copy)NSString *topic;
/**
 事件订阅者
 */
@property(nonatomic,strong)NSArray *subscribeList;
/**
 事件订阅者与方法索引
 */
@property(nonatomic,strong)NSDictionary *selectorMap;
/**
 block索引
 */
@property(nonatomic,strong)NSDictionary *blkMap;
@property(nonatomic,assign)BOOL isResultNeed;
@property(nonatomic,assign)BOOL isAsync;
@property(nonatomic,copy)CallResult callback;
@property(nonatomic,strong,readonly)id options;
-(instancetype)initWithTopic:(NSString *)topic selectorMap:(NSDictionary *)selectorMap blkMap:(NSDictionary *)blkMap options:(id)options;

```
- Event是对产生的一个事件的封装，它包括事件主题、订阅者列表、订阅者方法索引、订阅者block索引、事件携带信息。

### EGEventBus <span id="eventbus"></span>
```
+(instancetype)shared;

-(void)registManyToOneWithTarget:(id)target withAction:(SEL)action onTopic:(NSString *)topic;
-(void)registManyToOneWithTarget:(id)target withBlock:(TopicBlock)blk onTopic:(NSString *)topic;
-(void)registManyToOneWithTarget:(id)target withResultBlock:(TopicResultBlock)blk onTopic:(NSString *)topic;
-(void)registManyToOneWithTarget:(id)target withAsyncResultBlock:(AsyncCallbackBlock)blk onTopic:(NSString *)topic;

-(void)publishTopic:(NSString *)topic withOptions:(id)options;

-(void)publishTopic:(NSString *)topic from:(id)eventSource options:(id)options withResultHandler:(SEL)resultHandler;
-(void)publishTopic:(NSString *)topic from:(id)eventSource options:(id)options withResultBlock:(void(^)(id result))resultBlock;

-(void)unmount:(id)target onTopic:(NSString *)topic;
-(void)unmount:(id)target;

-(void)setDebug:(BOOL)debug;
-(void)print;
-(void)printTopic:(NSString *)topic;
```

- 事件总线使用单例模式。
- 发布一个主题和订阅事件主题，根据需不需要结果回调分为两种方式。
- EGEventBus中订阅者索引中对对象的引用可以自动释放，不需要手动处理，如果要取消订阅使用`unmount`方法。
- `setDebug `方法会在使用事件总线时将索引表进行打印提供参考。

### EGEventContext <span id="eventcontext"></span>
```
@property(nonatomic,copy)NSString *topic;
@property(nonatomic,weak)id target;
@property(nonatomic,assign)NSArray *actions;
@property(nonatomic,strong)NSArray *blkList;

@property(nonatomic,weak)EGEvent *event;
-(instancetype)initWithEvent:(EGEvent *)event;
```
- Context是对事件订阅者的单体封装,是一个事件主题中一个订阅者的执行上下文的封装。

### EGEventDispatcher <span id="eventdispatch"></span>
```
+(instancetype)shareInstance;
-(void)dispatchEvent:(EGEvent *)event;
```
- 事件进行分发，使用单例模式。内部提供了一个并发的事件执行队列。

### EGEventExcuter <span id="eventexcuter"></span>
```
@property(nonatomic,strong)EGEvent *event;
@property(nonatomic,strong)NSArray *targetList;
@property(nonatomic,copy)CallResult callback;
-(instancetype)initWithEvent:(EGEvent *)event withQueue:(dispatch_queue_t)excuteQueue;
-(void)excute;
```
- 负责事件在执行队列中的执行。

### NSObject+EventBus <span id="eventobj"></span>
```
/**
 事件订阅
 @param topic 事件主题
 @param handler 事件处理方法
 */
-(void)subscribe:(NSString *)topic withHandler:(SEL)handler;

/**
 事件订阅
 @param topic 事件主题
 @param topicBlock 事件处理block
 */
-(void)subscribe:(NSString *)topic withBlock:(TopicBlock)topicBlock;

/**
 事件订阅
 @param topic 事件主题
 @param topicResultBlock 事件处理block，带结果回调
 */
-(void)subscribe:(NSString *)topic withResultBlock:(TopicResultBlock)topicResultBlock;

/**
 事件订阅
 @param topic 事件主题
 @param asyncCallbackBlock 事件处理block，带异步结果回调
 */
-(void)subscribe:(NSString *)topic withAsyncResultBlock:(AsyncCallbackBlock)asyncCallbackBlock;

/**
 事件广播（一对多）
 @param topic 事件主题
 @param options 事件内容
 */
-(void)post:(NSString *)topic withOptions:(id)options;

/**
 事件广播（一对一），有结果回调
 @param topic 事件主题
 @param options 事件内容
 @param resultHandler 结果回调
 */
-(void)post:(NSString *)topic options:(id)options withResultHandler:(SEL)resultHandler;

/**
 事件广播（一对一），有结果回调
 @param topic 事件主题
 @param options 事件内容
 @param resultBlock 结果回调Block
 */
-(void)post:(NSString *)topic options:(id)options withResultBlock:(void(^)(id result))resultBlock;

/**
 取消事件订阅
 @param topic 事件主题
 */
-(void)unsubscribeForTopic:(NSString *)topic;

/**
 取消全部事件订阅
 */
-(void)unsubscribeAll;

-(void)setEventBusDebug:(BOOL)enable;

-(void)debug;
-(void)debugTopic:(NSString *)topic;
```

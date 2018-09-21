# 沙盒文件操作
## SandboxFile的组成
```
├── EGFileDefines
├── EGFileManager
├── EGFileWrapper
└── EGSanboxFile
```

## 常用Class和API
- [EGFileDefines](#define)		 
- [EGFileManager](#manager)		
- [EGFileWrapper](#wrapper)		
- [EGSanboxFile](#file)		

### EGFileDefines<span id="define"><span>
```
// 文件类型
typedef NS_ENUM(NSInteger,EGFileType) {
    EGFileTypeRegular=1,
    EGFileTypeDirectory,
    EGFileTypeSymbolicLink,
    EGFileTypeSocket,
    EGFileTypeCharacterSpecial,
    EGFileTypeBlockSpecial,
    EGFileTypeUnknown
};

//沙盒文件夹
typedef NS_ENUM(NSUInteger, EGDirectoryType){
    EGDirectoryTypeHome=1,
    EGDirectoryTypeDocument,
    EGDirectoryTypeCaches,
    EGDirectoryTypeLibrary,
    EGDirectoryTypeTmp,
    EGDirectoryTypeDefault   //present somethings under main directories
};

//文件扩展类型
typedef NS_ENUM(NSInteger,EGFileKindType) {
    EGFileKindTypeImage=1,
    EGFileKindTypeVideo,
    EGFileKindTypeAudio,
    EGFileKindTypeJS,
    EGFileKindTypeHtml,
    EGFileKindTypeCss,
    EGFileKindTypeDoc,
    EGFileKindTypeExcel,
    EGFileKindTypePdf,
    EGFileKindTypeOther,
    EGFileKindTypeHasNoSuffix
};
```

### EGFileManager <span id="manager"><span>
```
+(instancetype)manager;

#pragma mark  Sandbox Directory Path
-(NSString *)getDirPathWithType:(EGDirectoryType)type;
/** Home */
-(NSString *)getHomeDirectoryPath;

/** Document */
-(NSString *)getDocumentDirectoryPath;

/** Caches */
-(NSString *)getCacheDirectoryPath;

/** Library */
-(NSString *)getLibDirectoryPath;

/** tmp */
-(NSString *)getTmpDirectoryPath;

#pragma mark File Check
-(BOOL)fileExistsAtPath:(NSString *)filePath;

#pragma mark List Directory files
-(void)contentsOfDirectory:(NSString *)dirPath withResultHandler:(ResultHandler)resultHandler;
/**
 List files and directories under relative path if it is a directory ,it will throw an error if it's a file or not exist.
 @param dirPath Relative path.
 @param rootDir Root dir
 @param resultHandler Result callback with success or error.
 */
-(void)contentsOfDirectory:(NSString *)dirPath relateToRootDir:(EGDirectoryType)rootDir withResultHandler:(ResultHandler)resultHandler;

/**
 Convince methods to list files and directories relate to a special root directory if it is a directory ,it will throw an error if it's a file or not exist.
 @param dirPath Relative path.
 @param resultHandler Result callback with success or error.
 */
-(void)contentsOfDirectoryRelativeToDocuments:(NSString *)dirPath withResultHandler:(ResultHandler)resultHandler;
-(void)contentsOfDirectoryRelativeToLibrary:(NSString *)dirPath withResultHandler:(ResultHandler)resultHandler;
-(void)contentsOfDirectoryRelativeToCaches:(NSString *)dirPath withResultHandler:(ResultHandler)resultHandler;
-(void)contentsOfDirectoryRelativeToTmp:(NSString *)dirPath withResultHandler:(ResultHandler)resultHandler;

#pragma mark Add a file or a directory
-(void)addFileAsync:(NSString *)fileName data:(NSData *)data toRootPath:(NSString *)rootPath withResultHandler:(AddFileResultHandler)resultHandler;
-(void)addFileAsync:(NSString *)fileName data:(NSData *)data toRootURL:(NSURL *)rootURL withResultHandler:(AddFileResultHandler)resultHandler;
-(void)addFileAsync:(NSString *)fileName data:(NSData *)data toRootFileWrapper:(EGFileWrapper *)fileWrapper withResultHandler:(AddFileResultHandler)resultHandler;

/**
 Add a directory to a absolute path.
@param dirName Directory name
 @param rootPath Super directory path
 @return Result
 */
-(BOOL)addDirectory:(NSString *)dirName toRootPath:(NSString *)rootPath;
-(BOOL)addDirectory:(NSString *)dirName toRootURL:(NSURL *)rootURL;
-(BOOL)addDirectory:(NSString *)dirName toRootFileWrapper:(EGFileWrapper *)fileWrapper;

-(BOOL)addFile:(NSString *)fileName data:(NSData *)data toRootPath:(NSString *)rootPath;
-(BOOL)addFile:(NSString *)fileName data:(NSData *)data toRootURL:(NSURL *)rootURL;
-(BOOL)addFile:(NSString *)fileName data:(NSData *)data toRootFileWrapper:(EGFileWrapper *)fileWrapper;

#pragma mark Remove a file or a directory
-(BOOL)deleteItemAtPath:(NSString *)itemPath;
-(BOOL)deleteItemAtURL:(NSURL *)itemURL;
-(BOOL)deleteItemFileWrapper:(EGFileWrapper *)fileWrapper;

/**
 Use a directory path to delete a directory
@param dirPath Directory path
 @return Result
 */
-(BOOL)deleteDirAtPath:(NSString *)dirPath;

/**
 Use a URL to delete a directory
 @param dirURL Directory URL
 @return Result
 */
-(BOOL)deleteDirAtURL:(NSURL *)dirURL;

/**
 Use a filewrapper to delete a directory
 @param fileWrapper Filewrapper
 @return Result
 */
-(BOOL)deleteDirFileWrapper:(EGFileWrapper *)fileWrapper;

/**
 Use a file path to delete a file
 @param filePath File Path
 @return Result
 */
-(BOOL)deleteFileAtPath:(NSString *)filePath;

/**
 Use a URL to delete a file
 @param fileURL File URL
 @return Result
 */
-(BOOL)deleteFileAtURL:(NSURL *)fileURL;

/**
 Use a filewrapper to delete a file
 @param fileWrapper Filewrapper
 @return Result
 */
-(BOOL)deleteFileWrapper:(EGFileWrapper *)fileWrapper;

#pragma mark Debug option
-(void)enableDebug:(BOOL)enable;
```


### EGFileWrapper <span id="wrapper"><span>
```
/** file name */
@property(nonatomic,copy)NSString *name;
/** file absolute path */
@property(nonatomic,copy)NSString *absolutePath;
/** file relative path */
@property(nonatomic,copy)NSString *relativePath;
/** file root path. Like:Documents,Caches,tmp,Library */
@property(nonatomic,assign)EGDirectoryType rootDirType;
/** file suffix */
@property(nonatomic,copy)NSString *suffix;
/** file URL path */
@property(nonatomic,strong)NSURL *urlPath;
/** file is a directory? */
@property(nonatomic,assign)BOOL isDirectory;
/** file's kind */
@property(nonatomic,assign)EGFileKindType kindType;
/** file's parentDir */
@property(nonatomic,strong)NSURL *parentDirPath;

#pragma mark meta
/** file create date */
@property(nonatomic,strong)NSDate *createDate;
/** file last modification date */
@property(nonatomic,strong)NSDate *lastModificationDate;
/** file system type */
@property(nonatomic,assign)EGFileType fileType;
/** file size. (Unit:byte) */
@property(nonatomic,assign)int size;
/** file system attributes */
@property(nonatomic,strong)NSDictionary *fileAttributes;

/**
 Convert a NSFileWrapper to EGFileWrapper
 @param fileWrapper Filewrapper
 */
-(void)convertFileWrapper:(NSFileWrapper *)fileWrapper;

-(instancetype)initWithURL:(NSURL *)fileURL;
```

### EGSanboxFile <span id="file"><span>
```
+ (instancetype)shared;

/**
 获取文件大小
 @param fileName 文件名称
 @return 文件大小
 */
+ (unsigned long long)getFileSize:(NSString *)fileName;

/**
 文件解压
 @param fileName      zip文件名称
 @param toFilename    zip文件解压后的目标文件夹名称
 @param progress 	  已解压大小、文件总大小block
 */
+ (void)unzipFileName:(NSString *)fileName toFilename:(NSString *)toFilename progress:(unzipProgress)progress completedHandler:(unzipCompletedHandler)completedHandler;

/**
 压缩文件
 @param fileName      压缩后的目标文件名称
 @param directoryName 要压缩的文件夹名称
 */
+ (void)createZipFileName:(NSString *)fileName withContentsOfDirectoryName:(NSString *)directoryName completedHandler:(createZipCompletedHandler)completedHandler;

/** 一些debug信息 */
+ (void)debug;
```
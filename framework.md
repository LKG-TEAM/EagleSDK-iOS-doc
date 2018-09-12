# 静态库的使用

## Cocoapods
我们使用的Cocoapods进行依赖管理。		
NativeModule和WebModule是以静态库的形式发布和使用，所以在开发过程中和发布时要进行静态库的配置。配置文件就是工程目录下的`podspec`文件。(请使用平台创建工程里面的`podspec`文件)

>Example	

>```ruby
>require 'pathname'
class Importer
def self.importDefault(spec)
begin
arr_temp = IO.readlines('/Users/linkage/.jenkins/workspace/pack-ios-framework/dependency.default').fetch(0).split("~")
rescue Exception => e
arr_temp = IO.readlines('/Users/guxinsheng/WorkStation/project_dev/IBMessage/dependency.default').fetch(0).split("~")
end
print arr_temp
arr_temp.each do |i|
    if !(i.chomp.empty?)
      arr = i.split('@')
      if arr.size == 1
        spec.dependency arr.fetch(0).chomp
      elsif arr.size == 2
        spec.dependency arr.fetch(0), arr.fetch(1).chomp
      end
    end
  end
end
end
Pod::Spec.new do |s|
s.name='IBMessage'
s.version='1.0.0'
s.summary='A short description of IBMessage.'
s.description      = <<-DESC
TODO: Add long description of the pod here.
DESC
s.homepage         = 'http://192.168.2.94:10080/module/ios/ib/IBMessage'
s.license          = { :type => 'MIT', :file => 'LICENSE' }
s.author           = { 'guxs1129@163.com' => 'guxs@linkstec.com' }
s.source = {:git => 'ssh://git@192.168.2.94:10022/module/ios/ib/IBMessage.git',:branch=>'master'}
s.ios.deployment_target = '8.0'
s.resource_bundles = {
    'IBMessage_Xibs' => ['IBMessage/Classes/**/*.xib'],
    'IBMessage_configurations' =>['IBMessage/Classes/Configurations/*.json']
}
s.source_files = 'IBMessage/Classes/**/*.{h,m}','IBMessage/Classes/*.{h,m}'
s.prefix_header_contents = '#import "IBMessage.h"'
s.frameworks = 'UIKit', 'MapKit' ,'WebKit', 'SystemConfiguration', 'MobileCoreServices','AVFoundation','CoreLocation','Photos','AssetsLibrary','JavaScriptCore','CoreMedia'
s.vendored_frameworks='$(PODS_ROOT)/EagleSDK/EagleSDK.framework'
s.libraries = 'z'
Importer.importDefault(s)
# s.static_framework = true
s.dependency 'LMSPCommonUI'
s.dependency 'PYSearch'
s.dependency 'MJRefresh'
s.dependency 'LMSPSearch'
s.dependency 'LMSPLogin'
s.pod_target_xcconfig = {
'FRAMEWORK_SEARCH_PATHS' => '$(inherited) $(PODS_ROOT)/EagleSDK/EagleSDK.framework',
'USER_HEADER_SEARCH_PATHS' => '$(inherited) $(PODS_ROOT)/Headers/**',
'RUNPATH_SEARCH_PATHS' => '$(BUILT_PRODUCTS_DIR)/EagleSDK/EagleSDK.framework'
# 'OTHER_LDFLAGS'          => '$(inherited) -undefined dynamic_lookup'
}
end
>```

>请参考[Podspec Syntax Reference](https://guides.cocoapods.org/syntax/podspec.html)

## 资源打包
需要添加资源打包的功能时：		
```
s.resource_bundles = {
    'IBMessage_Xibs' => ['IBMessage/Classes/**/*.xib'],
    'IBMessage_configurations' =>['IBMessage/Classes/Configurations/*.json']
}
```

- Controller的json配置文件，约定以`(ModuleName)_configurations`命名。
- WebModule的本地H5工程，约定以`(ModuleName)_www`命名。

## 添加依赖

### 系统依赖
- 系统的framework，使用`s.frameworks`进行添加，比如`AVFoundation.framework`,`UIKit.framework`,`WebKit.framework`之类。
- 系统的lib,使用`s.libraries`进行添加，比如`libz.tbd`,`libc++.tbd`,`libsqlite3.tbd`。

### 第三方依赖
- 平台管理的依赖，在`dependency.default`文件中进行管理，需要修改请通过平台进行修改。
- 其他第三方依赖，通过`s.dependency`进行添加。

### 版本管理和发布
- 请使用平台进行静态库版本管理和发布。

**其他参数请不要修改，防止静态库发布后Cocoapods不能正确管理。**



## Cocoapods的使用
- NativeModule和WebModule都是基于Cocoapods创建的静态库工程脚手架，所以在开发过程中会经常使用pod命令。 
  
	-  静态库中增删文件后，需要运行`pod install`来重新安装开发的静态库。   
	- 当依赖的第三方静态库版本发生变化时，需要运行`pod update`来安装新的版本。  
	- 需要在工程中调试平台创建的静态库时，首先将`denpendency.default`中静态库依赖删除，然后在`Podfile`中引入静态库的本地地址，最后`pod install`即可将静态库引入可编辑的状态。尤其是在应用工程进行多模块联调可以通过此方法实现。

- 缓存的问题
	- 清理Cocoapods缓存：运行`rm -rf ~/Library/Caches/Cocoapods`
	- 清理工程缓存：先将`Podfile`中`importDefault`注释后运行`pod install`，然后取消注释在运行`pod install`即可清理工程中的静态库缓存。


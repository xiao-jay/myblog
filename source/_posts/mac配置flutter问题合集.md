---
title: mac配置flutter问题合集
excerpt: 我在配置flutter遇到的问题
tags: [flutter]
categories: flutter
banner_img: /img/壁纸.jpg
---

#### 1、/Users/jie/Desktop/flutter/lemon_flutter_app/ios/Pods/Pods.xcodeproj: warning: The iOS Simulator deployment target 'IPHONEOS_DEPLOYMENT_TARGET' is set to 8.0, but the range of supported deployment target versions is 9.0 to 15.2.99. (in target 'Toast' from project 'Pods')

```
cd ios 
sudo rm -rf ~/Library/Caches/CocoaPods   
rm -rf Podfile.lock 
rm -rf Pods      
rm -rf ~/Library/Developer/Xcode/DerivedData/*
pod deintegrate  
pod setup  
pod install   
```

2、Failed to package /Users/jie/Desktop/flutter/lemon_flutter_app/ios/...

Command PhaseScriptExecution failed with a nonzero exit code

```
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" embed
```

3、zsh: command not found: flatter

```
vim ~/.zshrc
// 在最后一行添加
source ~/.zshrc.pre-oh-my-zsh
source ~/.bath_profile
// wq 保存退出重新打开终端即可
```

4、rsync: link_stat "/Users/jie/Library/Developer/Xcode/DerivedData/Runner-bujxbaguzpyukvbumfgytsffnqlc/Build/Products/Debug-iphonesimulator/App.framework" failed: No such file or directory (2)

/bin/sh "/Users/jie/sdk/flutter/packages/flutter_tools/bin/xcode_backend.sh" embed_and_thin

5、ARC Semantic Issue (Xcode): No known class method for selector 'updatePrivacyShow:privacyInfo:'/Users/jie/sdk/flutter/.pubcache/hosted/pub.dartlang.org/amap_flutter_location3.0.0/ios/Classes/AMapFlutterLocationPlugin.m:92:29

```
cd ios 
sudo rm -rf ~/Library/Caches/CocoaPods   
rm -rf Podfile.lock 
rm -rf Pods      
rm -rf ~/Library/Developer/Xcode/DerivedData/*
pod deintegrate  
pod setup  
pod install   
```



6、Could not find module 'WebView' for target 'x86_64-apple-ios-simulator'; found: arm64, arm64-apple-ios-simulator, at: /Users/jie/Lib

在execuded archs 加入 x86_64

7、Error (Xcode): Framework not found tencent_kit

重新clone 项目

8、 line 132: ARCHS[@]: unbound variable

去掉用vscode打开 runner.xcodeproj  里面的EXCLUDED_ARCHS,VALID_ARCHS

9、Target Integrity (Xcode): The linked framework 'Pods_Runner.framework' is missing one or more architectures required by this target: arm64.

Runner->target->EXCLUDED_ARCHS->any ios simulate add arm64

10、Parse Issue (Xcode): Module 'amap_flutter_location' not found
/Users/jie/Desktop/flutter/lemon_flutter_app/ios/Runner/GeneratedPluginRegistrant.m:11:8

Encountered error while building for device.

把Xode 里面的Runner的ios版本改成高版本

11、TMS-90683: Missing Purpose String in Info.plist. Your app's code references one or more APIs that access sensitive user data. The app's Info.plist file should contain a NSCameraUsageDescription key with a user-facing purpose string explaining clearly and completely why your app needs the data. Starting Spring 2019, all apps submitted to the App Store that access user data are required to include a purpose string. If you're using external libraries or SDKs, they may reference APIs that require a purpose string. While your app might not use these APIs, a purpose string is still required. You can contact the developer of the library or SDK and request they release a version of their code that doesn't contain the APIs. Learn more (https://developer.apple.com/documentation/uikit/core_app/protecting_the_user_s_privacy).

在info.plist文件里面加入

```
<key>NSCameraUsageDescription</key>
	<string>照片权限</string>
	<key>NSPhotoLibraryUsageDescriptionn</key>
    <string>相机权限</string>
```

12、ios真机调试无法打开“iproxy”，因为无法验证开发者。

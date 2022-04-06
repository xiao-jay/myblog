---
title: mac自动push
excerpt: 定时push到github
tags: [自动化博客]
categories: 自动化博客
---



### 1、编写脚本

```
vim upload.sh
```

```
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
<key>Label</key>
<string>upload.github.code.plist</string>
<key>ProgramArguments</key>
<array>
<string>/Users/jie/Desktop/myblog/upload.sh</string> //脚本位置
</array>
<key>StartCalendarInterval</key>
<dict>
        <key>Minute</key>
        <integer>0</integer>   //每天18点执行一次
        <key>Hour</key>
        <integer>18</integer>
</dict>
<key>KeepAlive</key>
<false/>
<key>RunAtLoad</key>
<true/>
</dict>
</plist>
```

```
#!/bin/bash
cd /Users/jie/Desktop/myblog
git add .
git commit -m "update"
git push
```

给权限

```
chmod 755 upload.sh
```

### 2、建立plist

```
cd /Library/LaunchDaemons/
sudo vim upload.github.code.plist
```

```
plutil -lint upload.github.code.plist 检查是否编写错误
chmod 600 upload.github.code.plist  给权限
sudo launchctl load upload.github.code.plist 加载脚本
sudo launchctl start upload.github.code.plist 开始脚本
```




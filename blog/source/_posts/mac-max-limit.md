---
title: 永久修改 mac mac files/proc 限制
date: 2019-10-11 14:19:49
tags: ['mac','limit']
categories: os
---

mac 升级后的某一天，本地跑程序时发现提示 `open too many files`, 竟然提示打开文件数过多。

使用`ulimit -n` 查看，最大的文件数限制为 256 ，这明显太小了。 使用`launchctl limit` 查看,结果如下:

```
~/codes/backupAnyThing/blog(master*) » launchctl limit
	cpu         unlimited      unlimited
	filesize    unlimited      unlimited
	data        unlimited      unlimited
	stack       8388608        67104768
	core        0              unlimited
	rss         unlimited      unlimited
	memlock     unlimited      unlimited
	maxproc     1064           1064
	maxfiles    256            256
```

解决方案大致上有三种：

- 命令行中执行 `ulimit -n xxx`, 但是仅对当前 session 生效，新开 session 或者重启后会恢复为系统值 

- 在 .bashrc / .zshrc 等脚本中加上 `ulimit -n xxxx`, 这样每次新开 session 即可自动设置最大文件描述符显示

- 加入到 launchd 服务中, 永久修改


这里记录下第三种方案的步骤

创建两个 plist文件，民间名分别为 `limit.maxproc.plist`, `limit.maxfiles.plist`, 内容如下:

file: limit.maxfiles.plist
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
  <plist version="1.0">
    <dict>
      <key>Label</key>
        <string>limit.maxfiles</string>
      <key>ProgramArguments</key>
        <array>
          <string>launchctl</string>
          <string>limit</string>
          <string>maxfiles</string>
          <string>200000</string>
          <string>200000</string>
        </array>
      <key>RunAtLoad</key>
        <true/>
      <key>ServiceIPC</key>
        <false/>
    </dict>
  </plist>
  ```

file: limit.maxproc.plist

```
  <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple/DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
  <plist version="1.0">
    <dict>
      <key>Label</key>
        <string>limit.maxproc</string>
      <key>ProgramArguments</key>
        <array>
          <string>launchctl</string>
          <string>limit</string>
          <string>maxproc</string>
          <string>20480</string>
          <string>20480</string>
        </array>
      <key>RunAtLoad</key>
        <true />
      <key>ServiceIPC</key>
        <false />
    </dict>
  </plist>
  ```

将以上两个文件移动到 `/Library/LaunchDaemons` 目录下，确保两个文件的权限如下：

```
-rw-r--r--  1 root  wheel   592B Sep 29 16:39 limit.maxfiles.plist
-rw-r--r--  1 root  wheel   589B Sep 29 16:40 limit.maxproc.plist
```

重启系统，重启后自动生效。

注: 如果只需要修改 `max files`, 仅添加 `limit.maxfiles.plist` 文件即可。
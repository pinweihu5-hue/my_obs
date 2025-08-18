- [GitHub - 9001/copyparty: Portable file server with accelerated resumable uploads, dedup, WebDAV, FTP, TFTP, zeroconf, media indexer, thumbnails++ all in one file, no deps](https://github.com/9001/copyparty?tab=readme-ov-file)

[[arc - browser]]


2025_08_08 
current way to install apple not verifyed app
![[IMG-mac_idx-20250810084840426.png|763]]










chrome go up and down setting
![[IMG-mac_idx-20250810084840947.png]]



cleanShotX shotkey bk
![[IMG-mac_idx-20250810084841575.png]]


---
- [ ] [jordanbaird/Ice: Powerful menu bar manager for macOS](https://github.com/jordanbaird/Ice?utm_source=hackernewsletter&utm_medium=email&utm_term=show_hn)
- [ ] tmux alternative [shell-pool/shpool: Think tmux, then aim... lower](https://github.com/shell-pool/shpool?utm_source=hackernewsletter&utm_medium=email&utm_term=code)
- [ ] [LocalSend：與附近的裝置分享檔案](https://localsend.org/zh-TW)
- [ ] window mgmt [MrKai77/Loop: Window management made elegant.](https://github.com/MrKai77/Loop)


# full screen display menubar or not
![[IMG-mac_idx-20250810084841887.png|628]]



student discount app in mac -> [All Discounts | Student App Centre](https://www.studentappcentre.com/App)

## 移除 `com.apple.quarantine` 擴展屬性
```sh



# 會繞過 macOS 的安全檢查機制。因此，在執行這個命令之前，確保你信任該應用程式的來源並確定它是安全的。
# 如果你不確定應用程式的來源或安全性，建議不要移除這個標記，以免增加系統受惡意軟件侵害的風險。
sudo xattr -d -p com.apple.quarantine /Applications/Swiftcord.app


sudo xattr -d -p com.apple.quarantine /Applications/AirBattery.app

sudo codesign --force --deep --sign - /Applications/AirBattery.app

```


# 證書過期
第一步，执行 sudo codesign --force --deep --sign - /Applications/V2rayU.app
(use root permission 強行簽署一個空白簽名在app上，遞迴deep, 這個 app以下所有內容)
第二步，在应用程序中找到 V2rayU，右键，显示简介，勾选覆盖恶意软件保护
第三步，打开软件
第四部，执行 sudo codesign --force --deep --sign - ~/.V2rayU/V2rayUTool 和 sudo codesign --force --deep --sign - ~/.V2rayU/v2ray-core/v2ray
然后就能正常运行了。



[[raycast]]

[lihaoyun6/AirBattery: Get the battery usage of all your devices on your Mac and put them on the Dock / Menu Bar / Widget! && 在Mac上获取你所有设备的电量信息并显示在Dock / 状态栏 / 小组件上!](https://github.com/lihaoyun6/AirBattery)


[GitHub - lihaoyun6/QuickRecorder: A lightweight screen recorder based on ScreenCapture Kit for macOS / 基于 ScreenCapture Kit 的轻量化多功能 macOS 录屏工具](https://github.com/lihaoyun6/QuickRecorder)


[TRex | Magic OCR app for macOS](https://trex.ameba.co/)

replace bartender -> [jordanbaird/Ice: Powerful menu bar manager for macOS](https://github.com/jordanbaird/Ice)

[GitHub - nikitabobko/AeroSpace: AeroSpace is an i3-like tiling window manager for macOS](https://github.com/nikitabobko/AeroSpace)


- [GitHub - jhspetersson/fselect: Find files with SQL-like queries](https://github.com/jhspetersson/fselect?tab=readme-ov-file)
	- use sql in cli

example
```sh

┌──────────────────────────────────────────────────────────────────────────────────────>
│~/Downloads via  v2.6.10
at 14:15:08 ➜
└─> fselect path, size from . where is_video



fselect path from . where modified gte 2024-06-01

```

類似套件 [GitHub - laktak/zfind: search for files (even inside tar/zip/7z/rar) using a SQL-WHERE filter](https://github.com/laktak/zfind)


# mac local bin
`~/.local/bin`
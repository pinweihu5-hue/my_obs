



2025_01_11_05_30

目前解法，使用[Ctrl2cap - Sysinternals | Microsoft Learn](https://learn.microsoft.com/en-us/sysinternals/downloads/ctrl2cap)




---


相關 ref:
- [keyboard layout - Map capslock to control in Windows 10 - Super User](https://superuser.com/questions/949385/map-capslock-to-control-in-windows-10)
- [試著把 Caps Lock 當成 Ctrl 使用吧 — Jimmy Lin’s Blog](https://jmln.tw/blog/2020-02-05-caps-lock-as-ctrl.html)
- [Remap Caps Lock to Control on Windows 10 · GitHub](https://gist.github.com/joshschmelzle/5e88dabc71014d7427ff01bca3fed33d)


---


在一些特定情境，類似 advance setting, set path
caplock 會異常，導致我需要去停止autohotkey然後重新使用capslock 變回小寫
我用下面的方法，間接  disable capklock, let's see if it work

possible solution:
- use autohotkey v2
	- need to learn it, several syntax changes
- use other way to remap cpas lock to ctrl, not use autohotkey
- 在 DDDDD


目前會壞掉的情境 -> 如果用 [Ctrl2cap - Sysinternals | Microsoft Learn](https://learn.microsoft.com/en-us/sysinternals/downloads/ctrl2cap) -> 下面三個都可以解掉喔!!!
- 設定 advance system setting 下的環境變數
- use f3 and I want to copy the pic to paste
- window terminal preview 的 複製貼上也會壞掉


app to show keyboard shortcut on screen
[GitHub - mulaRahul/keyviz: Keyviz is a free and open-source tool to visualize your keystrokes ⌨️ and 🖱️ mouse actions in real-time.](https://github.com/mulaRahul/keyviz)



ref:
[AutoHotkey script to change Caps Lock to Control on hold and Escape on press and release · GitHub](https://gist.github.com/volks73/1e889e01ad0a736159a5d56268a300a8)





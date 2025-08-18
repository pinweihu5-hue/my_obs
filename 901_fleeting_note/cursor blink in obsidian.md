---  
alias:  
creation_date: 2024-05-25 11:38  
tags: 
related: [[obsidan plugin]], [[tool optimize]]
---  



目前好像沒有關掉 cursor blinking 在 obs 的作法
然後 cursor blinking 跟我目前的 obs 設置有衝突，因此會瘋狂 blinking (偶發)


最後是找到用下面的 cli 用 mac 層級直接關掉所有應用程序的 cursor blinkg 的動畫



```bash

# in order
# 原本游標閃爍的週期是 0.5 秒(500 毫秒)左右,這條命令將其設置為 10 秒(10000 毫秒)。
# 這條命令設置游標閃爍時"亮"的時間長度為 10 秒。
# 這條命令設置游標閃爍時"暗"的時間長度為 10 秒

defaults write -g NSTextInsertionPointBlinkPeriod -float 10000
defaults write -g NSTextInsertionPointBlinkPeriodOn -float 10000
defaults write -g NSTextInsertionPointBlinkPeriodOff -float 10000



# read current config
defaults read -g NSTextInsertionPointBlinkPeriod
defaults read -g NSTextInsertionPointBlinkPeriodOn
defaults read -g NSTextInsertionPointBlinkPeriodOff
```



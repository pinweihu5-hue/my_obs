---  
alias:  
creation_date: 2024-06-03 10:07  
tags: 
related: 
- [[vim_idx]]
- [[vscode_idx]]




---  

# 設定


改設定
```
 j /Users/re4388/.config/nvim-kickstart-modulenvim_kickstart_module
 code . 
```


try it no using vscode, debug purpose
```

nvim_kickstart_module

```


如果有裝套件調整，新增，需要用 terminal 打開才會生效 
`nvim_kickstart_module








# vscode config I may need:


use H, L to go two side
have a review on vim binding
use :vsplit
:new
:only


- [x] add some cheat key binding
	- https://github.com/re4388/dotfiles/blob/main/nvim-ver/wsl/vim-IDE-ver/nvim/bk/keymap_noUse.vim#L129



# 支援程度
# vscode marco 的目前限制

目前下面兩種都支援


```

 ## macro 用法

  • 啟動: qa
  • 停止: q
  • 在 ex mode 上使用 -> :12,31 norm @a

 ## dot 用法

  • use visual mode to select first:
  • :'<, '> norm .


```

## window 管理


不可以用 shift -> 因為衝到大小寫
用 cmd -> 原生鍵, 建議不要
用 ctrl -> 手不舒服

用 cmd

step1
vscode 下, cmd  hjkl -> shfit ctrl   hjkl
用 shift ctrl hjkl 來 map  Focus XXX Editor Group


```sh

ex mode:
:vsp
:sp

use cmd + h/l to switch




```

window switch
vscode 需要用下面的 workaround
- View: Focus Left Editor Group
	- ~~use kara to bind cmd+H -> ^+shift+H, then use Shift+^+H to bind vscode~~
	- use Shift+H to bind vscode
- View: Focus Right Editor Group
	- Shift+l to bind vscode
	- use `:xxx` to go to line number


# 套件


vscode jmp 
![[IMG-vscode_vim-20241003104934072.png]]

use alt + / to trigger
then hit 2 char keyboard
and then screen will show up
then type(1 or 2 char) to jump to there



jumpy
![[IMG-vscode_vim-20241003104948938.png]]
跟 neovim 套件 not compatible -> jump 後就卡在 insert mode 出不來了 XD


![[IMG-vscode_vim-20241003105011405.png]]
啟動後就可以開始打字，打了會出現 trigger word, 然後打了 trigger word 就跳過去了

![[IMG-vscode_vim-20241003105011542.png]]
啟動後打字，打了兩個，再 hit trigger word, then go there


![[IMG-vscode_vim-20241003105037791.png]]
啟動和按一個鍵 -> 出現 hit -> 按下 hit 跳過去


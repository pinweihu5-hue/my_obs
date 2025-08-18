[[vim_idx]]






# how to know what you type in iterm2?


`brew install showkey

也可以用 `dd

![[IMG-iterm_for_vim-20241003104933935.png]]

使用 ^C and esc to quit

例子

當我 hit alt/cmd + d (alt已經和opt swap) to use go to def

`'<C-D>'`

```jsx
nm('<C-D>', '<cmd>Telescope lsp_definitions<CR>')
```




# alt in terminal vim

互換 opt and cmd
因此當你按左下的 cmd/alt
你按的不是 cmd/alt, 你是按 opt
(why cnage, 因為 opt/start太小，不好用, 另外 window 過來的習慣，我想要繼續用 alt)

這個應該是一次性的，全域的

![[IMG-iterm_for_vim-20241003104948855.png]]



in iterm2
調整 left opt 為 esc+


for item2 global 設定

![[IMG-karabiner for vim-20241003104948914.png]]


下面是新增 alt 組合鍵的操作

use dd to check

when I type `alt+u`
![[IMG-iterm_for_vim-20241003105005790.png]]


then this is how I assing in vim
![[IMG-iterm_for_vim-20241003105027561.png]]


# window 管理  cmd/capslock  -> iterm 的 ctrl

[[iterm_for_vim]]
item 這邊先把 cmd bind to ctrl
然後 nvim 才能設定

![[IMG-iterm_for_vim-20241003105027732.png]]

and in vim you can do..

`api.nvim_set_keymap('n', '<C-h>', '<C-W>h', { noremap = true })



# vim back and forward in nvim (超級繞XD)



kara 的 global
先把 alt -> cmd
alt+q/a to cmd `[`  and cmd `]`

因為這邊我 global 已經習慣用 alt+q/a 來 back and forward for mostly ALL APPs
![[IMG-iterm_for_vim-20241003105052749.png]]



把 cmd `[` (back) and cmd `]`   (forward)  
bind 
到 C-o (back) and C-i (forward)
![[IMG-iterm_for_vim-20241003105052967.png]]









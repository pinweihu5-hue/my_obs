[[vim_idx]]



# 目前不用 plug 了

需要放在這邊

/Users/re4388/.config/nvim/init.lua:94
```sh

vim.cmd [[
call plug#begin()

Plug 'kana/vim-textobj-user'
Plug 'beloglazov/vim-textobj-quotes'
Plug 'kana/vim-textobj-entire'
Plug 'tpope/vim-surround'
Plug 'vim-scripts/ReplaceWithRegister'
Plug 'tpope/vim-commentary'

call plug#end()
]]




```
然後進入 vim buffer 使用 Plug 安裝
![[IMG-vscode using Plug pkg mgmt-20241003104934066.png]]
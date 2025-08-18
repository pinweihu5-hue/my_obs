alias:  
creation_date: 2024-06-01 16:22  


---  

- [[tool optimize]]
- [[vscode_vim]]
- [[obs_vim]]
- [[iterm_for_vim]]
- [[karabiner for vim]]
- [[vim_learning]]
- [[repeat.vim]]
- [[安裝多個 nvim 設定]]
- [[substitute.nvim]]
- [[nvim_treesitter + textObject]]

---


# 2024_12_11_08_18_45


- [ ] 盡量用滑鼠的 vim 


# 2024_06_05-09_55_52
大約玩了一個禮拜
我的 vim 之旅結束 XDD


- [x] 總結一下這次放棄的原因

- 主因：
	- 速度反而讓我變慢 -> 因為我用vscode 和其他套件等等等都已經習慣了"左右開攻的滑鼠+keyboard"的使用方式
- 其他原因：
	- 很多軟體都不支援 vim...so



- [x] 總結一下這是整合的成果
	- [x] terminal vim
		- [x] current setting -> use this to invoke `alias nvim_kickstart='NVIM_APPNAME="nvim-kickstart" nvim'
	- [x] vscode vim
		- [x] vim 版 -> 有常用功能，也有 treesitter 的 text obj (like yank funtion) ->` alias nvim_vimscript='NVIM_APPNAME="nvim-vimscript" nvim'
- [x] 可以衍生看一下有沒有 vim 的套件?
	- [x] 有必要嗎？因為我用的很多熱鍵都不是 vim , like line end and first
	- [x] 不然我的 "玩耍遊戲" 就搞 vscode ext吧






# Todo for terminal vim
Lsp
Auto complete
Null ls
Debugger

ps: ref this sereis: https://www.youtube.com/watch?v=4zyZ3sw_ulc&t=251s





# Todo from vscode vim

- [x] comple init.vim version


try lua mini for treesitter textobj

- [ ] 解決require 都讀不到，都一定要放在同一個檔案 -> 可以從這個 config 開始解  nvim-vscodeV2


坑
yankHighligh 要用 lua 寫法
目前還沒找到



comment





fix local 23

# goal


大多數的時候都是滑鼠黨，有需要再用 vim
可以這樣用嗎？





- try gain and see if it can improve my DX 
- 都用滑鼠辣 然後使用我常用的 insert mode 下的熱鍵
- 當你想要用 vim 的時候，可以切換到 normal mode




# todo

- 整理好目前 nvim 的狀態
	- 參考 https://www.youtube.com/watch?v=4zyZ3sw_ulc&t=251s
	- 先關掉不需要用的 plugin
	- fix subsitutte where to put its keymap
	- 可以 建立一個跟作者一樣的
	- 知道如何用 neo vim file explort
	- 搞 tree text sitter 的 text obj
		- [[nvim_treesitter + textObject]]
	- 再用 LSP




add camCase, aooke_apople text object

try
https://github.com/RRethy/nvim-treesitter-textsubjects
[mini.nvim/readmes/mini-ai.md at main · echasnovski/mini.nvim](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-ai.md)


nvim ts-server
install this
https://github.com/alpha2phi/neovim-pde




- [x] take out note and screenshot
- [x] terminal vim 補上 text obj, like quote 等等
- [x] make vscode vim config indep
- [ ] make terminal vim like IDE -> next step will consider to use it?
- [ ] 是否可以搞一個出來

- [ ] find sth like vim motion
- [ ] see vim tutor via :Tutor

- [ ] learn vim (vscode ext)
- [ ] [Nano Tips for Vim](https://nanotipsforvim.prose.sh/)

- [ ] 改造現有的 nvim terminal 版本
	- [ ] https://github.com/tokiory/neovim-boilerplate


- [ ] 好好整理 vim in obs note
- [ ] Js ts text object 
	- [ ] [nvim-treesitter/nvim-treesitter-textobjects](https://github.com/nvim-treesitter/nvim-treesitter-textobjects)
	- [ ] [vim 文字物件的樂趣：r/vim --- The joy of vim text objects : r/vim](https://www.reddit.com/r/vim/comments/g1r2od/the_joy_of_vim_text_objects/)
	- 沒那麼簡單....需要用 tree sitter 處理
- [x] Let vscode has it own config using modern lua
- [ ] [How to Create Vim Text-Objects in Lua](https://thevaluable.dev/vim-create-text-objects/)
- [ ] [Prevent Duplicate Keybindings](https://nanotipsforvim.prose.sh/prevent-duplicate-keybindings)
- [ ] [Lesser known nvim plugins](https://nanotipsforvim.prose.sh/lesser-known-nvim-plugins)
- [ ] [Practical Vim command workflow | Max Shen Dev](https://m4xshen.dev/posts/vim-command-workflow/)
[Learn VIM while playing a game - VIM Adventures](https://vim-adventures.com/)
vim tutor
vscode vim 的playground -> learn vim



# wana overcome this round:
- vim make me slow and I take more time to think of vim commnd rather than thinking real stuff
- okay, so how to solve this issue?
	1. use cheat vim biding like qq
	2. try to use mouse as soon as I feel I need, do not use too much vim, only use when I think it's handy -> so, stay in insert mode XD 

# cheatsheet
- [A Great Vim Cheat Sheet](https://vimsheet.com/)
- [Vim Cheat Sheet](https://vim.rtorr.com/)
- https://vim.rtorr.com/
![[IMG-vim_idx-20241003104934061.png]]


# recent change keybinding, new muscle memory

- cltr+r + register in insert mode
	- 當你 yank, d, c, 有 stuff goes into " register
	- 然後你要先打一些字
	- 這時候你可以用 ctrl+r  and then " 輸入
- ctrl + d/u, page down and up
- shift+4 to go end, shift + 6 or 0 to go head
- f to go ON word, t to go UNTIL word
- F to back ON word, T to back UNTIL word
- 重複 f, t, F T-> `;` 
- u is undo, C-R is undo undo
- qq is copy
- qs is copy inner string
- qr is replace


- 三個textobject 快速鍵,  [r]ectanger for []



# link
- [rockerBOO/awesome-neovim: Collections of awesome neovim plugins.](https://github.com/rockerBOO/awesome-neovim)

# archive
[[vscode using Plug pkg mgmt]]
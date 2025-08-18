[[vim_idx]]

---




[VIM 学习笔记 文本对象（Text Objects）](https://yyq123.github.io/learn-vim/learn-vi-10-TextObjects.html)


## use q or s? 
- cl （change letter）等於 s
- 因此如果surround vim 改掉 s -> 那你也可以用 cl 代替
- 因為 cl 的情境很小，因此如果你要改掉就改掉吧 XD
- 更多是用 r 改一個單字
- s 就是 修改單個 char
- 跟 r 的差異?
	- r 改還完後，會自動回到 normal mode
	- cl or s 改完後，會留在 insert mode



## copy and paste
用 qq copy 
用 qw 來 replace
qw 在 vscode and nvim 用 replace plug-in 做 -> 可以用 dot
qw 在 obs 用 vimp 做 -> 無法用 dot

## search in doc

`/`  往下找
`?`  往上找
`*`  search under curosr (`#` to do the same backwards)


下一次如果直接用 `/` enter -> 會自動找上一次找的字

vscode work
obs work




## dot opeation 的運用

use case1
砍下面所有的 cucumber
```markdown
cucumber carrot lettuce
cabbage carrot lettuce cucumber
cucumber cucumber carrot
kale cucumber kale
```

1. Locate the cucumber with `/cucumber`
2. Destroy the cucumber with **`daW` ( a : include whitespace)**
3. Type **`n`** to go to the next target and use **`.`** command to repeat the operation
4. Repeat

如果是改名建議還是用 symbol 的 f2 比較安全


use case 2
```bash
1. `one operation` first and saved into one .
2. select using visual mode ( tip: v => G to select down the list)
3. ex-mode to apply 
:'<, '> norm .
```



## ctrl + r ->  insert mode 獲取剛剛 y/d/c/s  的東西
normal mode中，需要 砍東西,使用 d, D, yYsScC等等
你等於把東西放到 `"` register
然後你需要編輯一下，再把東西放進來
這時候你可以用 `ctrl+r` + “
(r means roll over, 後面接你要貼上的東西是來自那個register)
在插入模式下使用 `CTRL-R {register}`，您可以在光標後粘貼寄存器的內容:
- **`CTRL-R "`** 粘貼未命名寄存器的內容
- **`CTRL-R a`** 粘貼寄存器 **`a`** 的內容
    - Vim 會在我們使用 d、D、x、X、s、S、c、C 命令刪除文字或使用 y 或 Y 命令複製文字時,將該內容填充到未命名寄存器中。未命名寄存器使用頻繁。
- **`CTRL-R 0`** 粘貼 yank 寄存器的內容


- obs work? yes
- vscode work? yes 


## ctrl  + o at insert mode

在 insert mode 下，使用這個熱鍵，可以讓你下一個完整的 normal mode 指令，然後回到 insert mode
使用情境就看你的創意了




# 水平短距離的移動

w W  b B e E ge gE f, F, t, T

可以用這個 try
apple, this. is okey to go apple is good hello world

f, t 都是正向
f onto
t 到前一個位置
反向也是一樣的邏輯
F onto
t 到後面算來的前一個位置


大寫會 skip 標點符號

ge, gE 我還不會用




# changes list and jump list

`:changes`
`:jumps`

vscode works
obs no

大概可以拿來看剛剛去哪裡
不過我大概可以用 vscode alt+2 or alt+e  recent file 取代掉



# indent operator "="


類似 `3==`
就是下面三行都 indent
 3== - re-indent 3 lines

或是用 visual mode 選好 按 =

也可以接 text object like
 =iB - re-indent inner block with {}


其他例子:
- =% - re-indent a block with () or {} (cursor on brace)
- gg=G - re-indent entire buffer



# 一些進階用法，不過目前我大概不太會用吧 XD

`:jumps` or `:ju`   `CTRL-I` and `CTRL-O` 就是作用在此


`:changes` to show change list
`g'` 會跳轉到變更列表中的前一個位置。
`3g` 會跳轉到變更列表中第三個較舊的位置。
相反的是 `g,`它會向前移動到變更列表中較新的位置。
`gi` 通常用於跳轉到你上次在插入模式下進行文字插入的位置。 這是一種方便的方式,可以返回到你最近進行變更的地方進入插入模式。
`gv` 通常在進行可視模式操作(如字符可視模式 `v` 或行可視模式 `V`)後使用,用於重新選中上次的可視選區。如果你想對同一段文字重複操作,而不需要再次選中,這會很方便。



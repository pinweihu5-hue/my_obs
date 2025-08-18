[[vim_idx]]



https://michaeluloth.com/neovim-switch-configs/


# lazyVim

https://www.lazyvim.org/installationgit clone https://github.com/LazyVim/starter ~/.config/nvim
```sh
git clone https://github.com/LazyVim/starter ~/.config/nvim-lazyvim

rm -rf ~/.config/nvim-lazyvim/.git


```


另外加上 一個 alias
```

alias vl='NVIM_APPNAME=nvim-lazyvim nvim' # LazyVim

```


uninstall
```

rm -rf ~/.config/nvim-lazyvim

```

感覺lazyvim 很不透明，很多設定和config 檔是外面抓進來的

還需要花時間了解看 blog 才好改他...
[Lazy.nvim: plugin configuration - DEV Community](https://dev.to/vonheikemen/lazynvim-plugin-configuration-3opi)


==
不然就換這個試試看，有 幫你 preconfig 一些
https://github.com/tokiory/neovim-boilerplate


# kickstart.nvim
https://github.com/nvim-lua/kickstart.nvim

```sh

git clone https://github.com/nvim-lua/kickstart.nvim.git ~/.config/nvim-kickstart

```


上面這個單一檔案有點難管理..

換下面這個
```

git clone https://github.com/dam9000/kickstart-modular.nvim.git ~/.config/nvim-kickstart-module

```

不過以上這些都比較適合作為我要把 nvim take it as IDE, not use it as vscode BE

...


# vimlang based 的 start, use PLUG

https://builtin.com/software-engineering-perspectives/neovim-configuration






#  cosmicNvim
[https://github.com/CosmicNvim/CosmicNvim](https://github.com/CosmicNvim/CosmicNvim)
    - better ts support than lvim
    - give up → since I the null-ls stuff keep show up and it cofig it not that ez to config
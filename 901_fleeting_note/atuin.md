---  
alias:  
creation_date: 2024-05-28 20:33  
tags: 
related:[[linux_idx]]
---  


instlal and setup
```sh


bash <(curl https://raw.githubusercontent.com/atuinsh/atuin/main/install.sh)


# setup


# find enter_accept -> change to false 
vi ~/.config/atuin/config.toml









```


- ctrl + r → 好像已經被 overide by zsh 的功能,
- 方向鍵按上 → 進入 互動搜尋模式
- 找昨天 3點後的, 正常關掉的, npm 是關鍵字：
    - `atuin search --exit 0 --after "yesterday 3pm" npm`
- 更多config [https://atuin.sh/docs/config/](https://atuin.sh/docs/config/)
    - config 檔案 `vi ~/.config/atuin/config.toml`
- fuzzy search syntax: [https://atuin.sh/docs/config/#fuzzy-search-syntax](https://atuin.sh/docs/config/#fuzzy-search-syntax)



- [更多例子](https://atuin.sh/docs/commands/search)


example
```sh fold


atuin stats last friday
atuin stats


# Open the interactive search TUI
atuin search -i

# Open the interactive search TUI preloaded with a query
atuin search -i atuin

# Search for all commands, beginning with cargo, that exited successfully
atuin search --exit 0 cargo

# Search for all commands, that failed, from the current dir, and were ran before April 1st 2021
atuin search --exclude-exit 0 --before 01/04/2021 --cwd .

# Search for all commands, beginning with cargo, that exited successfully, and were ran after yesterday at 3pm
atuin search --exit 0 --after "yesterday 3pm" cargo

# Delete all commands, beginning with cargo, that exited successfully, and were ran after yesterday at 3pm
atuin search --delete --exit 0 --after "yesterday 3pm" cargo

# Search for a command beginning with cargo, return exactly one result.
atuin search --limit 1 cargo

# Search for a single result for a command beginning with cargo, skipping (offsetting) one result
atuin search --offset 1 --limit 1 cargo

# Find the oldest cargo command
atuin search --limit 1 --reverse cargo

```


[[linux_idx]]
[[ubuntu 開 port 給外部服務用 via firewall]]

---

# ubuntu init 設定 essential
```sh

sudo apt update -y 
sudo apt install unzip sudo apt install openjdk-17-jdk wget curl git python3-pip net-tools tree build-essential neofetch lsof zsh zsh-syntax-highlighting fd-find -y 



# 用我可以用 fd 來呼叫
# mac
ln -s $(which fdfind) ~/.local/bin/fd

# linux ubunut
sudo ln -s $(which fdfind) /usr/local/bin/


# oh-my-zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"



## nvim
mkdir ~/nvim_bin && cd nvim_bin
wget https://github.com/neovim/neovim/releases/download/nightly/nvim-linux64.tar.gz && tar xzvf nvim-linux64.tar.gz


### 加上 alias
~/nvim_bin/nvim-linux64/bin/nvim ~/.zshrc


alias vi="~/nvim_bin/nvim-linux64/bin/nvim"
alias vim="~/nvim_bin/nvim-linux64/bin/nvim"
alias nvim="~/nvim_bin/nvim-linux64/bin/nvim"

source ~/.zshrc


# wget fzf-0.50.0-linux_amd64.tar.gz 的 link
wget https://github.com/junegunn/fzf/releases/download/0.50.0/fzf-0.50.0-linux_amd64.tar.gz

# 會拿到 fzf binary
tar -xvzf fzf-0.50.0-linux_amd64.tar.gz 

# 移到 user local bin
sudo mv fzf /usr/local/bin 
fzf --version 



## Install plugins

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
git clone https://github.com/zdharma-continuum/fast-syntax-highlighting.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/fast-syntax-highlighting
git clone https://github.com/Aloxaf/fzf-tab ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/fzf-tab
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions


## Enable plugins by adding them to .zshrc.
nvim ~/.zshrc



ZSH_THEME="amuse"
plugins=(git zsh-syntax-highlighting fast-syntax-highlighting fzf-tab zsh-autosuggestions)


# fzf-tab 設定:

# disable sort when completing `git checkout`
zstyle ':completion:*:git-checkout:*' sort false
# set descriptions format to enable group support
# NOTE: don't use escape sequences here, fzf-tab will ignore them
zstyle ':completion:*:descriptions' format '[%d]'
# set list-colors to enable filename colorizing
zstyle ':completion:*' list-colors ${(s.:.)LS_COLORS}
# force zsh not to show completion menu, which allows fzf-tab to capture the unambiguous prefix
zstyle ':completion:*' menu no
# preview directory's content with eza when completing cd
zstyle ':fzf-tab:complete:cd:*' fzf-preview 'eza -1 --color=always $realpath'
# switch group using `<` and `>`
zstyle ':fzf-tab:*' switch-group '<' '>'

# zshrc 加上這兩行，這樣才可以建立中文檔案
export LC_ALL=en_US.UTF-8
export LANG=enUS.UTF-8



# note: source $ZSH/oh-my-zsh.sh <--this shall be at first line or before load zsh config

source ~/.zshrc
	




# ps: You can switch caplock and esc in ubuntu shell 
setxkbmap -option caps:swapescape



# zoxide
curl -sS https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | bash

nvim ~/.zshrc

# in zshrc
export PATH="$PATH:/usr/local/bin"
alias j="z"
eval "$(zoxide init zsh)"


export PATH="$PATH:/home/re4388_103_0406/.local/bin" 
export PATH="$PATH:/root/.local/bin" 
export PATH="$PATH:/usr/local/bin/"

# fnm
curl -fsSL https://fnm.vercel.app/install | bash




# https://github.com/atuinsh/atuin

bash <(curl https://raw.githubusercontent.com/atuinsh/atuin/main/install.sh)

# find enter_accept -> change to false 
vi ~/.config/atuin/config.toml



# docker
curl -sSL https://get.docker.com/ | sh


sudo groupadd docker

# get username
whoami 

sudo usermod -aG docker <username>

# apply or use below to apply immediately
newgrp docker




# speedtest
curl -s https://packagecloud.io/install/repositories/ookla/speedtest-cli/script.deb.sh | sudo bash
sudo apt-get install speedtest

```



# sgpt

```sh
sudo apt update
sudo apt install python3-pip -y
pip3 install shell-gpt

# change alias
alias ai="sgpt"

ai -s "how to update local dns mapping"

```


# ufw_and_iptables設定 

```bash fold title:ufw_and_iptables設定


sudo ufw status
sudo ufw allow 61208/tcp
sudo ufw delete allow 61208/tcp

# if disable





# 加入規則 allow tcp 61208
sudo iptables -A INPUT -p tcp --dport 61208 -j ACCEPT


# 加入規則 allow 只允許新建立的 HTTP 連接以及已建立的連接使用 61208 port
sudo iptables -A INPUT -p tcp --dport 61208 -m state --state NEW,ESTABLISHED -j ACCEPT


# see all
sudo iptables -L -n

# remove rule
sudo iptables -D INPUT <rule_number>


# 另一台機器可以確認是否有開了
nc -z 100.87.63.33 8080


# 如果要將連接埠新增至防火牆的持久性配置並立即套用更改，可以使用 --permanent 和 --reload 標誌
see https://www.digitalocean.com/community/tutorials/opening-a-port-on-linux




```


# go
```sh fold
## https://go.dev/dl/
wget https://go.dev/dl/go1.22.4.linux-amd64.tar.gz


# remove prev version
rm -rf /usr/local/go 

# unarchive
tar -xzf go1.22.4.linux-amd64.tar.gz

# move to common path
sudo mv go /usr/local

zshrc加入
export PATH="$PATH:/usr/local/go/bin"
export PATH="$PATH:/home/<userName>/go/bin"




source ~/.zshrc && go version

```



# vim yank to system board
``` sh fold

## vim yank to system board
### 1. 確認是否有正確安裝 clipboard tool for nvim
:checkhealth
-> 應該無法

### 2. nvim config 先設好 copy to system clipboard
mkdir -p ~/.config/nvim && cd ~/.config/nvim && touch init.vim
nvim init.vim

# nvim config 裡面加上這個 for using system clipboard
set clipboard+=unnamedplus   

### 3. 安裝 ssh copy/paste 套件來通訊 (這邊用 ts 來幫忙，上面ts要裝, go也要裝)
git clone https://github.com/lemonade-command/lemonade.git 

# cd 進去 build
make install

# 然後mac/host那邊要啟動 server, 一樣要 git clone and build

# mac 這邊設定
nvim ~/.config/lemonade.toml

port = 1234
allow = '100.80.233.20'
line-ending = 'crlf'

# port 用沒在用的
allow 是 遠端的 ts addr

然後要把server up
lemonade server


遠端這邊設定
nvim ~/.config/lemonade.toml

port = 1234
host = '100.104.167.17'
trans-loopback = true
trans-localfile = true
line-ending = 'crlf'

這邊host 是 mac 的 ts addr

:checkhealth -> 應該好了

也可以這樣測測看，應該會打到 host 端的 系統 clipboard了
cat a1.txt | lemonade copy



```


# 安裝 tailsacle
```sh fold


## 安裝ts
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null

curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/focal.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list

sudo apt-get update && sudo apt-get install tailscale -y && sudo tailscale up 

### 可以測一下 tcp 連接 確認 ts work!
mac 可以用
nc -l <listen_port>
另一邊 linux
nc <ip> <port>
看看是否可以通訊



```



# how to copy over file and folder over SSH
```sh fold


scp Invincible_** acer:/home/re4388/vid_stream1


# scp <local_path> remote_user_name@<ssh_machine_domain_or_ip>:<remote_path> 

scp <local_path> re4388@acer.tailbcab9.ts.net:/home/re4388/Downloads 

# 遠端的 tmp/a1路徑需要存在 
# 把 test1 資料夾 copy 到 a1 folder 中 
scp -r /Users/re4388/Downloads/test1 re4388@acer.tailbcab9.ts.net:/home/re4388/tmp/a1


```

#  ref
- - [https://linuxconfig.org/ubuntu-24-04-firewall-a-quick-guide](https://linuxconfig.org/ubuntu-24-04-firewall-a-quick-guide)
- [如何設定 Iptables 防火牆來保護伺服器之間的流量 |數位海洋 --- How To Set Up an Iptables Firewall to Protect Traffic Between your Servers | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-iptables-firewall-to-protect-traffic-between-your-servers)




# IBM linux one 開 port via iptables
update iptable https://github.com/linuxone-community-cloud/technical-resources/blob/master/faststart/deploy-virtual-server.md


```sh

# to open other port, need to config iptable

# check current
sudo iptables -L

# open port for 80
sudo iptables -I INPUT -p tcp --dport 80 -j ACCEPT 

# open port for 3000
sudo iptables -I INPUT -p tcp --dport 3000 -j ACCEPT 


# to make your changes permanently
sudo bash -c "iptables-save > /etc/iptables/rules.v4" 


# 注意：如果要使用ufw等防火牆管理工具，需要刪除已儲存的iptables檔案。

```

result:
![[IMG-ubuntu-20241003104934054.png]]







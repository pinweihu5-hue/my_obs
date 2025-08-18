[[docker_idx]]

# add user to docker group 
```sh


# 確認 group 是否存在
grep '^docker:' /etc/group

# 如果不存在
sudo groupadd docker

# 這樣add user
sudo usermod -aG docker <username>

# apply or use below to apply immediately
newgrp docker

# use below to check 目前這個 user 在那些 group
groups $USER


# to check
docker --version

```
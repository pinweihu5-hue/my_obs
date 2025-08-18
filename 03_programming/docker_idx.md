

- [ ] [[Docker 核心概念总结]]
- [ ] [[Docker 实战]]


- [[wemo_docker_compose]]
- [[docker 網路模式]]
- [[docker 橋接模式]]
- [[add user to docker group]]
- [[safetly remove docker volumn]]
- [[docker_compose_ways_to_start]]
- [[清理 不要用的 docker container and image]]
- [[docker 安全性]]
- [[docker internal]]



# 常用指令
```bash fold

docker run  # Run a command in a **new** container
docker start  # Start one or more stopped containers

# just start
docker start sql2


## compose
===


docker-compose up -d


# How to update existing images with docker-compose?
docker-compose up --force-recreate --build -d



#  project 重啟, 用在你已經調整了設定，想要重啟，不想另外讓他開另一個 project
docker compose -p prod-db-proxy up -d


```


# 想要從docker 內部的服務，打到本機(host)

要使用特定的 domain, `host.docker.internal`

要看 一個例子:  [Explore networking features on Docker Desktop | Docker Docs](https://docs.docker.com/desktop/networking/#i-want-to-connect-from-a-container-to-a-service-on-the-host)



# add a volume to an existing Docker container
```sh

# 先把一個 container clone into a new image
# docker commmit <the_container_want_to_clone> <new_img_name>
docker commit 5a8f89adeead pgacid-v2

# run up a container, 把一個 vol 掛在 我要跑起來的 pgacid-v2 img 上
# docker run -d -v <host_path>:<container_path> <new_img_name>
docker run -d -v "$PWD":/pgacid_volume -p 5439:5432 pgacid-v2

# 可以 check 了
# check container id
docker ps 
docker exec xxxx -it bash

```



# Link
- [Haxxnet/Compose-Examples: Various Docker Compose examples of selfhosted FOSS and proprietary projects.](https://github.com/Haxxnet/Compose-Examples)
- [docker/awesome-compose: Awesome Docker Compose samples](https://github.com/docker/awesome-compose/tree/master)

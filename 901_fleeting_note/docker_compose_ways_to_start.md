[[docker_idx]]

要重新啟動 Docker Compose，有多種方法可以達成這個目標。以下是一些常見的方法：

### 方法一：使用 `docker-compose restart`

這個命令會重新啟動所有在 `docker-compose.yml` 文件中定義的服務。

```sh

docker compose restart

```

### 方法二：停止並重新啟動

你可以先停止所有服務，然後再啟動它們。

```sh

docker compose down

docker compose up

```

### 方法三：重新啟動特定服務

如果你只想重新啟動特定的服務，可以在 `restart` 命令後面指定服務名稱。

```sh

docker compose restart <service_name>

```

### 方法四：停止並重新啟動特定服務

你也可以停止並重新啟動特定的服務。

```sh

docker compose stop <service_name>

docker compose up <service_name>

```

### 方法五：重新創建容器

這個方法會刪除並重新創建容器，這樣可以確保所有的配置變更都被應用。

```sh

docker compose down

docker compose up --force-recreate -d

```

### 方法六：使用 `docker compose pull` 更新並重新啟動

如果你想在重新啟動之前拉取最新的鏡像，可以使用這個方法。

```sh

docker compose pull

docker compose up --detach

```

### 方法七：使用 `docker compose up --build`

這個方法會在重新啟動之前重新構建服務。

```sh

docker compose up --build

```

### 方法八：使用 `docke -compose up -d`

這個方法會在後台重新啟動服務。

```sh

docker compose down

docker compose up -d

```

這些方法都可以用來重新啟動 Docker Compose，具體選擇哪種方法取決於你的需求和具體情況。
## note


### use **`restart: unless-stopped
- 你手動關掉不會重啟，其他情況(意外退出等等, exit code is not 0)都會自動重啟
- 比 `restart: always` 好用，因為這個任何情況都會重啟



# local port

- portainer 9000
- ceres 5461, 5462
- hermes db 6433, 5432,  redis 6480, 6489
- pricing svc  db 6434 16380
- vehicle svc  db 6435 16381




### 裝 portainer

```tsx
sudo docker pull portainer/portainer-ce
sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

### 小抄

```tsx
restart: unless-stopped
restart: on-failure

```

## microservice

### **box-service-fluentd**

```tsx
version: "3"
services:
  fluentd:
    image: fluent/fluentd:v1.16
    platform: linux/amd64
    expose:
      - 24224
    ports:
      - 24224:24224
networks: {}

```

### ceres_test

```ts
version: "3"
services:
  postgres:
    image: postgres:9.6.12
    ports:
      - 5461:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
  redis:
    image: redis:4.0.14-alpine
    ports:
      - 6389:6379
networks: {}

```

### ceres_local

```tsx
version: "3"
services:
  postgres:
    image: postgres:9.6.12
    expose:
      - 5432
    ports:
      - 5462:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
  redis:
    image: redis:4.0.14-alpine
    ports:
      - 6370:6379
networks: {}

```


### hermes local
```yaml

version: '3'

services:
  postgres:
    image: postgres:9.6.12
    expose:
      - 5432
    ports:
      - 6433:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
  redis:
    image: redis:4.0.14-alpine
    container_name: redis
    ports:
      - 6480:6379

```

### hermes_test

```tsx
version: '3'

services:
  postgres:
    image: postgres:9.6.12
    expose:
      - 5432
    ports:
      - 5432:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    command: ['-c', 'fsync=off', '-c', 'synchronous_commit=off', '-c', 'full_page_writes=off', '-c', 'max_locks_per_transaction=512', '-c', 'max_connections=200']
  redis:
    image: redis:4.0.14-alpine
    container_name: redis
    ports:
      - '6379:6379'
    restart: always
```

### gaia_test

```tsx
version: "3"
services:
  hermes_postgres:
    image: postgres:9.6.12
    ports:
      - 5464:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=hermes-test
  mnemosyne_postgres:
    image: postgres:9.6.12
    container_name: postgresql_gaia_mne_test
    restart: unless-stopped
    ports:
      - 5465:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=hermes-test
  redis:
    image: redis:4.0.14-alpine
    ports:
      - 6391:6379
networks: {}

```

### gaia_local

```tsx
version: "3"
services:
  hermes_postgres:
    image: postgres:9.6.12
    ports:
      - 5462:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=hermes-test
  mnemosyne_postgres:
    image: postgres:9.6.12
    container_name: postgresql_gaia_mne_local
    restart: unless-stopped
    ports:
      - 5463:5432
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=hermes-test
  redis:
    image: redis:4.0.14-alpine
    ports:
      - 6390:6379
networks: {}

```

### zeus local

```tsx
version: '3'
services:
  postgres:
    image: 'postgres:9.6'
    ports:
      - '5440:5432'
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    command:
      - '-c'
      - fsync=off
      - '-c'
      - synchronous_commit=off
      - '-c'
      - full_page_writes=off
      - '-c'
      - max_locks_per_transaction=512
      - '-c'
      - max_connections=200
  redis:
    image: 'redis:4.0.14-alpine'
    ports:
      - '7380:6379'

```

### angelia test

```tsx
version: '3'

services:
  postgres:
    image: postgres:9.6
    expose:
      - 8432
    ports:
      - 8432:5432
    environment:
      - POSTGRES_USER=angelia-test
      - POSTGRES_PASSWORD=password
    command: ['-c', 'fsync=off', '-c', 'synchronous_commit=off', '-c', 'full_page_writes=off', '-c', 'max_locks_per_transaction=512', '-c', 'max_connections=200']
  redis:
    image: redis:4.0.14-alpine
    container_name: angelia-redis
    ports:
      - '7379:6379'
```

### zeus test

```tsx
version: '3'
services:
  postgres:
    image: 'postgres:9.6'
    ports:
      - '5441:5432'
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    command:
      - '-c'
      - fsync=off
      - '-c'
      - synchronous_commit=off
      - '-c'
      - full_page_writes=off
      - '-c'
      - max_locks_per_transaction=512
      - '-c'
      - max_connections=200
  redis:
    image: 'redis:4.0.14-alpine'
    ports:
      - '7381:6379'

```

## 其他

### **uptime-kuma**

```tsx
version: "3.3"
services:
  uptime-kuma:
    ports:
      - 3001:3001
    volumes:
      - uptime-kuma:/app/data
    image: louislam/uptime-kuma:1
volumes:
  uptime-kuma: {}
networks: {}
```

### **simple_mind_map**

```tsx
version: "3.3"
services:
  mind-map:
    ports:
      - 8111:8080
    image: shuiche/mind-map:latest
networks: {}

```

### pg_v9.6

```tsx
version: "3.8"
services:
  postgres16:
    image: postgres:9.6
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - 5441:5432

```

### **pg-v16**

```tsx
version: "3.8"
services:
  postgres16:
    image: postgres:16
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - 5441:5432

```

### pg_dotfile_for_my_server

```tsx
version: "3.3"
services:
  postgres:
    ports:
      - 5439:5432
    environment:
      - POSTGRES_USER=dotfile
      - POSTGRES_PASSWORD=dotfile
      - POSTGRES_DB=dotfile
    image: postgres:9.6
networks: {}

```

### my_redis

```tsx
version: "3.8"
services:
  cache:
    image: redis:latest
    ports:
      - 6388:6379
    command: redis-server --save 20 1 --loglevel warning
    volumes:
      - cache:/data
volumes:
  cache:
    driver: local
networks: {}
```

## archive

sql proxy

```tsx
 qat
 
version: '3'
services:
  qat_cloudsql_proxy:
    container_name: qat_cloudsql_proxy
    image: gcr.io/cloud-sql-connectors/cloud-sql-proxy:2.1.0
    restart: always
    ports:
      - '9432:5432'
    volumes:
      - type: bind
        source: ./backend-qat.json # Required - ask TeamLead for this service account
        target: /config
    command: ['backend-qat:asia-east1:wemo-qat', '-c', '/config', '-p', '5432', '-a', '0.0.0.0', ]
    
    
    
    
    
    
prod

version: "3"
services:
  prod_cloudsql_proxy_hermes:
    container_name: prod_cloudsql_proxy
    image: gcr.io/cloud-sql-connectors/cloud-sql-proxy:2.1.0
    restart: unless-stopped
    volumes:
      - type: bind
        source: ./backend.json # Required - ask Team Lead for this service account
        target: /config
    ports:
      - "19432:5432" #hermes
      - "19433:5433" #ceres
      - "19434:5434" #mnemosyne
      - "19435:5435" #zeus
      - "19436:5436" #wemo-backup
      - "19437:5437" #angelia-prod
      - "19438:5438" #wemo-atlas
      - "19439:5439" #wemo-eirene-2
    command:
      [
        "backend-181310:asia-east1:wemo-prod?port=5432&address=0.0.0.0",
        "backend-181310:asia-east1:ceres-prod?port=5433&address=0.0.0.0",
        "backend-181310:asia-east1:wemo-postgres?port=5434&address=0.0.0.0",
        "backend-181310:asia-east1:zeus-db?port=5435&address=0.0.0.0",
        "backend-181310:asia-east1:wemo-backup?port=5436&address=0.0.0.0",
        "backend-181310:asia-east1:angelia-prod?port=5437&address=0.0.0.0",
        "backend-181310:asia-east1:wemo-atlas?port=5438&address=0.0.0.0",
        "backend-181310:asia-east1:wemo-eirene-2?port=5439&address=0.0.0.0",
        "-c",
        "/config",
      ]

```
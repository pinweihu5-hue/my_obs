### code → sql
- 先寫好 orm 的 TS code, 產生 sql
- 基本上就是用 typeorm 的工具來產生 sql

```bash


# 先跑一個 docker用來處理 db migration
docker run -d --name hermes-db-migration -p 5432:5432 -e POSTGRES_USER="hermes" -e POSTGRES_PASSWORD="password" -e POSTGRES_DB="hermes" -d postgres:9.6

## 進入 container
docker exec -it hermes-db-migration bash

## 進入 psql
psql -U hermes

## 建立 schema
CREATE SCHEMA hermes;


## 用 typeorm 來幫我們產生 sql
npx typeorm migration:generate -n m1
npx tsc
npx typeorm migration:run
npx typeorm migration:show

# 清除 schema;
DROP SCHEMA hermes CASCADE;


####################### 另一個例子 for ceres 的例子

docker run -d --name ceres-db-migration -p 5432:5432 -e POSTGRES_USER="ceres" -e POSTGRES_PASSWORD="password" -e POSTGRES_DB="ceres" -d postgres:9.6

docker exec -it ceres-db-migration bash

進入 docker 
psql -U ceres
CREATE SCHEMA ceres;

npx typeorm migration:generate -n m1
npx tsc
npx typeorm migration:run

清除 schema;
DROP SCHEMA ceres CASCADE;
```

文件
- [https://docs.google.com/document/d/1_laiM9vowEGUTB6xNBluMNP4eCuTYOxbykIsXTeVTMY/edit](https://docs.google.com/document/d/1_laiM9vowEGUTB6xNBluMNP4eCuTYOxbykIsXTeVTMY/edit)
- [Angelia migration guidline.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/130666a0-0d5b-486c-a639-d570380467d4/44f6a722-005d-4d26-99f9-87b9b32f22fa/Angelia_migration_guidline.pdf)
            

## sql → code
- 先把 sql 寫好，打入 DB, 然後再用 工具去讀 DB 然後產生 code
- 工具: 
	- [typeorm-model-generator - npm](https://www.npmjs.com/package/typeorm-model-generator)
	- /Users/re4388/project/work/wemo-typeorm-model-generator
	- - [https://athena.wemoscooter.com/jack.wu/wemo-typeorm-model-generator/-/blob/main/package.json](https://athena.wemoscooter.com/jack.wu/wemo-typeorm-model-generator/-/blob/main/package.json)
- 產生的 code 可以參考

## pro and con
- 各有好壞
    - Jack 都用 sql → code, 因為他認為， sql 是最重要的，要可以控制 sql ，然後才是讓那個 sql 產 code
        - 如果先寫 ts-code , 怕產出來的 sql 可能會錯
    - 如果要改的邏輯和scope不複雜，兩個其實都差不多，看習慣。
```sh

docker run -itd -e POSTGRES_USER=re4388 -e POSTGRES_PASSWORD=2aoxjgju -p 5499:5432 --name postgresV0 postgres:latest

docker exec -it postgresV0 psql -U re4388


CREATE TABLE test_table (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

INSERT INTO test_table (name) VALUES ('Test Name');

SELECT * FROM test_table;




docker stop postgresV0

docker rm postgresV0


```
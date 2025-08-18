

PS 像是我們連 pg in GCP 的 client 都沒有打開ssl
因此如果有壞人進來公司，又可以 access 公司內網
又可以mimt 抓包，是可以拿到 username and pass word的
![[IMG-db_hn_security-20250810210633890.png|598]]



set up SSL connection from client to pg instance
- 要去改 var/lib/postgresal/data 裡面的 conf 檔案, 設定 public/private key
![[IMG-db_hn_security-20250810210634053.png]]




# use wireshark to MIMT pg-db with to TLS

use this cloud db: [https://api.elephantsql.com/console/6f90abf5-6dee-4855-8119-b25b67d0bb35/browser](https://api.elephantsql.com/console/6f90abf5-6dee-4855-8119-b25b67d0bb35/browser)?



建立一些資料
```sql


create table employees (pid serial primary key, id integer, name text);

insert into employees (id, name) values(1, 'apple');
insert into employees (id, name) values(2, 'banana');
insert into employees (id, name) values(3, 'cringle');
insert into employees (id, name) values(5, 'doom');
insert into employees (id, name) values(6, 'elephant');
insert into employees (id, name) values(7, 'fill');
insert into employees (id, name) values(8, 'gap');
insert into employees (id, name) values(9, 'hope');

```



code
/Users/re4388/project/udemy_db_hussei/wireshark-pg
```js fold

const pg = require('pg')

const ClientClass = pg.Client
const pgUrl = 'postgres://wuldmskc:59tHzizSDRGcqLtHxRmFMz01B3qXLF10@tiny.db.elephantsql.com/wuldmskc'
const client = new ClientClass(pgUrl)


async function connect(client) {
    try {
        await client.connect()
        console.log('client is connected')

        const { rows } = await client.query('select * from employees')
        // id, name


        console.log('rows: ', rows)
    } catch (error) {
        console.log('we have an error', error)
    } finally {
        await client.end()
    }
}

connect(client)

```



use wireshark


下面可以可以看到 user and pass 被看光光
![[IMG-db_hn_security-20250810210634686.png]]
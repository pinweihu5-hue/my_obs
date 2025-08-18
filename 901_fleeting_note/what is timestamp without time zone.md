在 PostgreSQL 中，`timestamp without time zone` 表示一個日期和時間，不會考慮或存儲時區偏移量
這是 default setting
以下兩段等價
```ts
@CreateDateColumn({
  type: 'timestamp without time zone'
})
createdAt: Date;


vs


@CreateDateColumn()
createdAt: Date;

```
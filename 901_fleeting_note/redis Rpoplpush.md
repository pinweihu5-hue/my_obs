
```sh
/data # redis-cli

// 推入 a, b, c into l1 list
127.0.0.1:6379> rpush l1 'a'
(integer) 1
127.0.0.1:6379> rpush l1 'b'
(integer) 2
127.0.0.1:6379> rpush l1 'c'
(integer) 3

// 使用 RPOPLPUSH(原子操作) 把 l1 的最後一個拿出來然後推到 l2 的第一個
127.0.0.1:6379> RPOPLPUSH l1 l2
"c"

// 用lrange 去看 l1 and l2
127.0.0.1:6379> LRANGE l1 0 -1
1) "a"
2) "b"

127.0.0.1:6379> LRANGE l2 0 -1
1) "c"
127.0.0.1:6379>

```

 
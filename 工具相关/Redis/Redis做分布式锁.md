# Redis做分布式锁
Tags: #Redis
Links:
[聊聊如何用 Redis 实现分布式锁？ - 小林coding - 博客园](https://www.cnblogs.com/xiaolincoding/p/16517673.html)

```sh
SET lock_key unique_value NX PX 10000 
```
`lock_key` 就是 key 键；
`unique_value` 是客户端生成的唯一的标识，区分来自不同客户端的锁操作；
`NX` 代表只在 `lock_key` 不存在时，才对 `lock_key` 进行设置操作；
`PX 10000` 表示设置 `lock_key` 的过期时间为 10s，这是为了避免客户端发生异常而无法释放锁。

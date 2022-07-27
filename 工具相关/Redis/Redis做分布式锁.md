# Redis做分布式锁
Tags: #Redis
Links:
[聊聊如何用 Redis 实现分布式锁？ - 小林coding - 博客园](https://www.cnblogs.com/xiaolincoding/p/16517673.html)

## 加锁
Redis 的 SET 命令有个 NX 参数可以实现「key不存在才插入」，所以可以用它来实现分布式锁：
- 如果 key 不存在，则显示插入成功，可以用来表示加锁成功；
- 如果 key 存在，则会显示插入失败，可以用来表示加锁失败。
```sh
SET lock_key unique_value NX PX 10000 
```
`lock_key` 就是 key 键；
`unique_value` 是客户端生成的唯一的标识，区分来自不同客户端的锁操作；
`NX` 代表只在 `lock_key` 不存在时，才对 `lock_key` 进行设置操作；
`PX 10000` 表示设置 `lock_key` 的过期时间为 10s，这是为了避免客户端发生异常而无法释放锁。
## 解锁
而解锁的过程就是将 `lock_key` 键删除（`del lock_key`），但不能乱删，要保证执行操作的客户端就是加锁的客户端。所以，解锁的时候，我们要先判断锁的 `unique_value` 是否为加锁客户端，是的话，才将 `lock_key` 键删除。
可以看到，解锁是有两个操作，这时就需要 Lua 脚本来保证解锁的原子性，因为 Redis 在执行 Lua 脚本时，可以以原子性的方式执行，保证了锁释放操作的原子性。
```lua
// 释放锁时，先比较 `unique_value` 是否相等，避免锁的误释放
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

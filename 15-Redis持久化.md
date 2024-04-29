# Redis 持久化

## 一、Redis 的配置文件目录

要查看 Redis 的配置文件目录，

执行命令：

```bash
root@iZwz9clzmhmmlb65bbcvuuZ:~# systemctl status redis
● redis-server.service - Advanced key-value store
     Loaded: loaded (/lib/systemd/system/redis-server.service; enabled; vendor preset: enabled)
```

可以看到，Redis 服务加载的文件是 `/lib/systemd/system/redis-server.service`。

查看这个文件：

```bash
root@iZwz9clzmhmmlb65bbcvuuZ:~# cat /lib/systemd/system/redis-server.service
......
[Service]
Type=notify
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
......
```

发现 Redis 配置文件的目录是 `/etc/redis/redis.conf`

## 二、Redis 持久化

Redis 是一个基于内存的数据库，如果没有持久化，那么一旦服务器重启或者断电，那么以前的数据都会丢失。

Redis 持久化主要有两种方式：

### 1.方式一：RDB

RDB（Redis Database）指的是在指定时间间隔内，将内存中的数据快照写入磁盘，它是某一个时间点上数据的完整副本。

查看 Redis 的配置文件 `/etc/redis/redis.conf`，发现其中有 save 相关的配置：

```shell
# Save the DB to disk.
#
# save <seconds> <changes> [<seconds> <changes> ...]
#
# Redis will save the DB if the given number of seconds elapsed and it
# surpassed the given number of write operations against the DB.
#
# Snapshotting can be completely disabled with a single empty string argument
# as in following example:
#
# save ""
#
# Unless specified otherwise, by default Redis will save the DB:
#   * After 3600 seconds (an hour) if at least 1 change was performed
#   * After 300 seconds (5 minutes) if at least 100 changes were performed
#   * After 60 seconds if at least 10000 changes were performed
```

可以通过配置文件中的 save 参数来配置。

- 后面的参数是时间和修改次数的组合。第一个参数表示秒数，第二个参数表示修改次数。
- 下方还有一些其它配置：根据服务器的负载情况，来调整配置文件中的配置。

```bash
# save <seconds> <changes> [<seconds> <changes> ...]
...
#   * After 3600 seconds (an hour) if at least 1 change was performed
#   * After 300 seconds (5 minutes) if at least 100 changes were performed
#   * After 60 seconds if at least 10000 changes were performed
```

上面的三个配置选项，分别代表：

- 3600s 内（1 小时内）只要有一次修改，就进项一次快照
- 300s 内有 100 次修改才进行一次快照。
- 60s 内有 10000 次修改，才进行一次快照。

除了使用配置文件自动触发快照之外，还可以使用 save 命令来手工触发快照。

执行命令，保存一个键值对：

```bash
127.0.0.1:6379> SET k1 v1
OK
```

在命令行终端，执行 save 命令

```bash
127.0.0.1:6379> SAVE
OK
```

退出 Redis 命令行客户端

在 `/var/lib/redis/` 目录下，查看保存的快照文件 `dump.rdb`

```bash
root@iZwz9clzmhmmlb65bbcvuuZ:/var/lib/redis# ll
total 12
drwxr-x---  2 redis redis 4096 Apr 22 20:02 ./
drwxr-xr-x 54 root  root  4096 Apr 17 14:50 ../
-rw-rw----  1 redis redis  100 Apr 22 20:02 dump.rdb
```

使用 xxd 命令，查看其中的内容

> xxd 命令，是一个可以查看二进制或十六进制文件内容的 Linux 命令。

```bash
-rw-rw----  1 redis redis  100 Apr 22 20:02 dump.rdb
root@iZwz9clzmhmmlb65bbcvuuZ:/var/lib/redis# xxd dump.rdb
00000000: 5245 4449 5330 3031 31fa 0972 6564 6973  REDIS0011..redis
00000010: 2d76 6572 0537 2e32 2e34 fa0a 7265 6469  -ver.7.2.4..redi
00000020: 732d 6269 7473 c040 fa05 6374 696d 65c2  s-bits.@..ctime.
00000030: dc51 2666 fa08 7573 6564 2d6d 656d c298  .Q&f..used-mem..
00000040: c11a 00fa 0861 6f66 2d62 6173 65c0 00fe  .....aof-base...
00000050: 00fb 0100 0002 6b31 0276 31ff 599d 0fb0  ......k1.v1.Y...
00000060: fd94 4b0b                                ..K.
root@iZwz9clzmhmmlb65bbcvuuZ:/var/lib/redis#
```

快照文件不会保存快照之后的内容，所以 RDB 更适合用来做备份，比如在每天凌晨的时候，通过 crontab 来执行一次 SAVE 命令，进行备份。

在生产环境中，一般为 Redis 开放的内存都会比较大，那么内存中的数据同步到硬盘的过程，就会持续比较长的时间。这段时间内，Redis 都会处于阻塞的状态，不能接收任何请求。

Redis 提供了 bgsave 命令，这个命令会单独创建一个子进程，来负责将内存中的数据，写入到硬盘中，这样主进程就可以继续处理请求了。这个命令会有一定的性能损耗，因为 fork 子进程是需要时间的。这段时间内，Redis 还是不能处理任何请求，所以无法做到秒级的快照。

为了解决这个问题，Redis 又提供了 AOF 的持久化方式。

### 2.方式二：AOF

方式二：AOF（Append Only File），原理是在执行写命令的时候，不仅会将命令写入到内存中，还会将命令写入到一个追加的文件中，这个文件就是 AOF 文件。它会以日志的形式，来记录每一个写操作，

当 Redis 重启时，就会重新执行 AOF 中的命令，以在内存中重建整个数据库的内容。

开启 AOF 的方式是在 `redis.conf` 配置文件中设置：

```shell
appendonly yes
```

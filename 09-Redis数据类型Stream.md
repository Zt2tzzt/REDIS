# Redis 数据类型 Stream

Stream 是 Redis 5.0 引入的一个新的数据结构，它是一个轻量级的消息队列，用于解决发布订阅功能的局限性。

Stream 相关的命令，都以 X 开头。

## 一、XADD 命令

使用 XADD 命令，向 Stream 中添加消息。

语法：`XADD key [NOMKSTREAM] [MAXLEN | MINID [= | ~] threshold [LIMIT count]] * | id field value [field value ...]`

```bash
> XADD zetian * course redis
"1713418960583-0"

> XADD zetian * course nginx
"1713418972910-0"

> XADD zetian * course Docker
"1713418983534-0"
```

- 符号 * 表示自动消息的 ID。
- "course redis"是消息的内容。

手工指定的 ID（时间戳-序列号）

```bash
> XADD zetian 1-0 course docker

> XADD zetian 2-0 course docker

> XADD zetian 3-0 course docker
```

第一个整数表示一个时间戳，第二个整数表示一个序列号。

如果使用 * 号生成 ID，Redis 会保证生成的 ID 是递增的。

## 二、XLEN 命令

使用 XLEN 命令，查看消息的数量：

语法：`XLEN key`

```bash
> XLEN zetian
(integer) 3
```

## 三、XRANGE 命令

使用 XRANGE 命令，来查看 Stream 中的消息的详细内容。

```bash
> XRANGE zetian - +
1) 1) "1713418960583-0"
   2) 1) "course"
      2) "redis"
2) 1) "1713418972910-0"
   2) 1) "course"
      2) "nginx"
3) 1) "1713418983534-0"
   2) 1) "course"
      2) "Docker"
```

- 符号 -、+ 号，分别表示开始、结束

可以看到，消息的 ID，消息的内容，都被打印出来了。

## 四、XDEL 命令

使用 XDEL 命令，来删除消息。

语法：`XDEL key id [id ...]`

```bash
> XDEL zetian 1713418960583-0
(integer) 1
```

再查看 Stream 中的消息，可以看到只剩下两条消息了。

```bash
> XRANGE zetian - +
1) 1) "1713418972910-0"
   2) 1) "course"
      2) "nginx"
2) 1) "1713418983534-0"
   2) 1) "course"
      2) "Docker"
```

## 五、XTRIM 命令

使用 XTRIM 命令，来删除消息。

语法L：`XTRIM key MAXLEN | MINID [= | ~] threshold [LIMIT count]`

```bash
> XTRIM zetian MAXLEN 0
(integer) 2
```

- `MEXLEN 0` 表示删除 Stream 中所有的消息。

输出 2 表示删除了 2 条消息。

再查看 Stream 中的消息，可以看到没有消息了。

```bash
> XRANGE zetian - +
(empty list or set)
```

## 六、XREAD 命令

消息创建后，使用 XREAD 命令，进行消费。

语法：`XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] id [id ...]`

```bash
> XREAD COUNT 2 BLOCK 1000 STREAMS zetian 0
1) 1) "zetian"
   2) 1) 1) "1713420730815-0"
         2) 1) "course"
            2) "redis"
      2) 1) "1713420734080-0"
         2) 1) "course"
            2) "nginx"
```

- `count 2`，表示一次读取 2 条消息。
- `BLOCK 1000`，表示如果没有消息，就阻塞 1000 毫秒。
- `STREAMS zetian`，表示要读取的 Stream 名称。
- `0`，表示从头开始读取

在 Stream 中的消息，可以重复读取。

当从一个大于 ID 的数值开始读取时，因为 Stream 中没有该 ID 对应的消息，所以会被阻塞 1s，然后输出 null。

```shell
> XREAD COUNT 2 BLOCK 1000 STREAMS zetian 999999999999999
(nil)
```

获取从现在开始，以后的消息，使用 `$` 符号。

```bash
> XREAD COUNT 2 BLOCK 10000 STREAMS zetian $
```

在 10s 内，打开另一个终端，往流 zetian 里，添加一个消息

观察阻塞终端中的输出：

```bash
> XREAD COUNT 2 BLOCK 10000 STREAMS zetian $
1) 1) "zetian"
   2) 1) 1) "1713424308787-0"
         2) 1) "course"
            2) "linux"
```

## 七、XGROUP CREATE 命令

使用 XGROUP 命令，创建消费者组。

```bash
> XGROUP CREATE zetian group1 0
"OK"
```

- `zetian`，是消息的名称。
- `group1`，是组的名称。
- `0`，是 ID

现在，创建了一个名为 group1 的消费者组

查看 Stream 消息中的详细内容：

```bash
> XRANGE zetian - +
1) 1) "1713447421902-0"
   2) 1) "course"
      2) "redis\x11"
2) 1) "1713447426489-0"
   2) 1) "course"
      2) "nginx"
3) 1) "1713447430000-0"
   2) 1) "course"
      2) "docker"
```

## 八、XGROUP CREATECONSUMER 命令

有了组，再来添加两个消费者。

Redis6.2.0 以上版本，可使用 CREATECONSUMER 子命令

```bash
XGROUP CREATECONSUMER zetian group1 consumer1

XGROUP CREATECONSUMER zetian group1 consumer2

XGROUP CREATECONSUMER zetian group1 consumer3
```

## 八、XINFO CROUPS 命令

使用 XINFO GROUPS 命令，查看消费者组的信息。

```bash
> XINFO GROUPS zetian
1) 1) "name"
   2) "group1"
   3) "consumers"
   4) "3"
   5) "pending"
   6) "0"
   7) "last-delivered-id"
   8) "0-0"
```

可以看到，返回了组的名称，消费者的数量，待处理的消息数等等。

## 九、XREAD GROUP 命令

现在已经有三个消费者了，可通过 XREAD GROUP 命令，来读取消息。

```bash
> XREADGROUP GROUP group1 consumer1 COUNT 2 BLOCK 3000 STREAMS zetian >
1) 1) "zetian"
   2) 1) 1) "1713447421902-0"
         2) 1) "course"
            2) "redis\x11"
      2) 1) "1713447426489-0"
         2) 1) "course"
            2) "nginx"

```

- `>` 符号，表示从这个消息中，读取最新的消息。

Redis6.2.0 之前，上方的命令，这将从流 `zetian` 中读取一条消息，并将其分配给名为 `consumer1` 的消费者。

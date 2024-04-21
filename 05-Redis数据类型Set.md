# Redis 数据类型 Set

Set 是一种无序集合，它和列表的区别在于，

- 列表中的元素可以重复，而 Set 中的元素是不能重复的。
- Set 中的元素是无序的。

## 一、SADD 命令

SADD 命令，用于在 Set 中添加元素。

语法：`SADD key member [member ...]`

创建一个 course 集合，在其中添加“Redis”元素。

```bash
> SADD course Redis
(integer) 1
```

当往 course 集合里，再次添加一个“Redis”元素时，会发现无法添加成功：

```bash
> SADD course Redis
(integer) 0
```

## 二、SMEMBERS 命令

SMEMBERS 命令，用于查看集合中的元素。

语法：`SMEMBERS key`

```bash
> SMEMBERS course
1) "Redis"
```

## 三、SISMEMBER 命令

SISMEMBER 命令，用于判断一个元素是否在集合中，

语法：`SISMEMBER key member`

判断“Redis”是否在 course 集合中。

```bash
> SISMEMBER course Redis
(integer) 1
```

输出 1，表示“Redis”在集合中。

判断“Python”是否在 course 集合中：

```bash
> SISMEMBER course python
(integer) 0
```

输出 0，表示“Python”不在集合中。

## 四、SREM 命令

SREM 命令，用于删除集合中的元素。

语法：`SREM key member [member ...]`

在 course 集合中，删除 Redis 元素。

```bash
> SREM course Redis
(integer) 1
```

再次查看 course 集合中的元素：

```bash
> SMEMBERS course
(empty list or set)
```

## 五、集合的运算

在 Redis 中，使用 SINTER、SUNION、SDIFF 等命令，进行集合的运算，

比如：两个集合之间的交集，并集的运算。

### 1.SINTER 命令

SINTER 命令，用于获取给定集合的交集。如果给定的集合中有一个为空集，那么结果也将为空集。

```bash
> SADD myset1 hello foo bar
(integer) 3

> SADD myset2 hello world
(integer) 2

> SINTER myset1 myset2
1) "hello"
```

### 2.SUNION 命令

SUNION 命令用于返回给定集合的并集。

```bash
> SADD key1 a b c
(integer) 3

> SADD key2 c d e
(integer) 3

> SADD key3 a c e
(integer) 3

> SUNION key1 key2 key3
1) "a"
2) "c"
3) "b"
4) "d"
5) "e"
```

### 2.SDIFF 命令

SDIFF 命令，用于返回给定集合之间的差集。差集的结果来自前面的第一个集合，而不是后面的其他集合。

```bash
> SADD key1 a b c
(integer) 3

> SADD key2 c d e
(integer) 3

> SADD key3 a c e
(integer) 3

> SDIFF key1 key2 key3
1) "b"
```




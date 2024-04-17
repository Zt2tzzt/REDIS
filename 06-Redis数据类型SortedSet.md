# Redis 数据类型 SortedSet

SortedSet 也称为 ZSet，意思是有序集合。

SortedSet  和 Set 的区别在于：

- SortedSet 中的每个元素，都会关联一个浮点类型的分数，然后按照这个分数，对集合中的元素进行**从小到大**的排序。

SortedSet  有序集合的元素是唯一的，但是元素关联的分数，是可以重复的。

## 一、ZADD 命令

使用 ZADD 命令，向有序集合中添加一个元素：

语法：`ZADD key [NX | XX] [GT | LT] [CH] [INCR] score member [score member ...]`

添加一个 result 有序集合，在其中存入元素，高校录取分数作为分数，高校名称作为成员。

```bash
> ZADD result 680 清华 660 北大 650 复旦 640 浙大
(integer) 4
```

## 二、ZRANGE 命令

ZRANGE 命令，用于查看有序集合中的元素：

```bash
> ZRANGE result 0 -1
浙大
复旦
北大
清华
```

在输出成员的同时，输出分数

```bash
> ZRANGE result 0 -1 WITHSCORES
浙大
640
复旦
650
北大
660
清华
680
```

## 三、ZSCORE 命令

ZSCORE 命令，查看有序集合中某个成员的分数：

查看 result 有序集合中，“清华”的分数。

```bash
> ZSCORE result 清华
680
```

## 四、ZRANK 命令

ZRANK 命令，查看有序集合中某个成员的排名。

查看 result 有序集合中，“清华”的排名。

```bash
> ZRANK result 清华
3
```

有序集合中的排序，是按照从小到大来排序的（升序）。

## 五、ZREVRANK 命令

ZREVRANK 命令，查看有序集合中某个成员的反向排名。

查看 result 有序集合中，“清华”的反向排名。

```bash
> ZREVRANK result 清华
0
```

## 六、ZREM 命令

ZREM 命令，用于删除有序集合中的成员。会返回该成员的排序。

语法：`ZREM key member [member ...]`

删除 result 有序集合中的”清华“成员。

```bash
> ZRANK result 清华
3
```

## 七、ZINCRBY 命令

ZINCRBY 命令，用于对某个成员的分数进行增加，返回增加后的分数。

- 如果 `key` 不存在，或者成员的分数不是 `key` 的成员，那么 `ZINCRBY` 的行为等同于 `ZADD key increment member`。
- 分数值可以是整数或双精度浮点数。

语法：`ZINCRBY key increment member`

在 result 有序集合中，为成员“复旦”增加 50 分

```bash
> ZINCRBY result 50 复旦
700
```

## 八、ZREMRANGEBYSCORE 命令

ZREMRANGEBYSCORE 命令，用于删除有序集合中，某个排名范围之内的成员。返回被删除成员的排序。

语法：`ZREMRANGEBYSCORE key min max`

在 result 有序集合中，删除分数在 650-660 之间的成员：

```bash
> ZREMRANGEBYSCORE result 650 660
1
```

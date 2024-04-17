# Redis 数据类型 Set

Set 是一种无序集合，它和列表的区别在于，

- 列表中的元素可以重复，而 Set 中的元素是不能重复的。
- Set 中的元素是无序的。

## 一、SADD 命令

SADD 命令，用于在 Set 中添加一个元素。

语法：`SADD 键名 键值 键值 ...`

创建一个 course 集合，在其中添加“Redis”元素。

```bash
> SADD course Redis
(integer) 1
```

当再次执行命令，往 course 集合里，添加一个“Redis”元素时，会发现无法添加成功：

```bash
> SADD course Redis
(integer) 0
```

## 二、SMEMBERS 命令

SMEMBERS 命令，用于查看集合中的元素。

```bash
> SMEMBERS course
1) "Redis"
```

## 三、SISMEMBER 命令

SISMEMBER 命令，用于判断一个元素是否在集合中，

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

输出 0，表示“Redis”不在集合中。

## 四、SREM 命令

SREM 命令，用于删除集合中的元素。

语法：`SREM 键名 要删除的元素`

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

在 Redis 中，使用 SINTER、SUNION、SDIFF 等命令，进行集合的运算，比如：两个集合之间的交集，并集的运算。

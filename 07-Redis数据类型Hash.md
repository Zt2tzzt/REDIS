# Redis 数据类型 Hash

Hash（哈希）是字符类型的字段和值的映射表，简单来说就是一个键值对的集合，特别适合用来存储对象。

Redis 中，哈希相关的命令，都是以 H 开头的。

## 一、HSET 命令

使用 HSET 命令，向哈希中添加一个键值对（字段<->值）。

语法：`HSET key field value [field value ...]`

```bash
> HSET person name zetian
(integer) 1

> HSET person age 8
(integer) 1
```

## 二、HGET 命令

使用 HGET 命令，来获取哈希表中的某个键值对。

语法：`HGET key field`

```bash
> HGET person name
"zetian"

> HGET person age
"8"
```

## 三、HGETALL 命令

使用 HGETALL 命令，获取哈希中的所有键值对，它们是成对出现的：

语法：`HGETALL key`

```bash
> HGETALL person
1) "name"
2) "zetian"
3) "age"
4) "8"
```

## 四、HDEL 命令

使用 HDEL 命令，删除哈希中的某个键值对。

语法：`HDEL key field [field ...]`

```bash
> HDEL person age
(integer) 1

> HGETALL person
1) "name"
2) "zetian"
```

## 五、HEXISTS 命令

使用 HEXISTS 命令，来判断某个键值对是否存在。

语法：`HEXISTS key field`

判断键 person 哈希中，是否存在 name 字段和 age 字段。

```bash
> HEXISTS person name
(integer) 1

> HEXISTS person age
(integer) 0
```

## 六、HKEYS 命令

使用 HKEYS 命令，获取哈希中所有的键值对

语法：`HKEYS key`

```bash
> HKEYS person
1) "name"
```

## 七、HLEN 命令

使用 HLEN 命令，获取哈希中所有键值对的数量。

```bash
> HLEN person
(integer) 1
```


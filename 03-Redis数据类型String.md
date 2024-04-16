# Redis 数据类型 String

Redis 中的数据，是以键值对的形式存储的。

所以，设置数据时，需要指定一个键和一个值。它们之间以一个空格分隔。

## 一、SET、GET 命令

使用 SET 命令，来设置一个键值对。

```bash
> SET name zetian
"OK"
```

使用 GET 命令，获取键对应的值。

```bash
> GET name
"zetian"
```

Redis 中，键是区分大小写的。

Redis 中，默认使用二进制安全的字符串，来存储数据，

这就意味之，可以把很多类型的数据，都存储在 Redis 中。比如：数字，布尔值，甚至序列化之后的对象......。

设置一个键 age

```bash
> SET age 25
"OK"
```

获取该键对应的值

```bash
> GET age
"25"
```

发现输出了 `"25"`，表示是一个字符串类型。

## 二、DEL 命令

使用 DEL 命令，删除一个键

```bash
> DEL name
(integer) 1
```

发现输出了 1，表示删除成功。

获取 name 键，对应的值：

```bash
> GET name
(nil)
```

## 三、FLUSHALL 命令

使用 FLUSHALL 命令，将数据库中所有键，都删除掉。

```bash
> FLUSHALL
"OK"
```

再查看数据库中所有的键，发现都是空的。

```bash
> KEYS *
(empty list or set)
```

发现获取到的是 null，表示该舰已经删除了。

## 四、KEYS 命令

使用 KEYS 命令，查看 Redis 中有哪些键。后面加上一个 pattern 匹配模式，

比如 `*` 表示查找所有键。

```bash
> KEYS *
1) "Name"
2) "age
```

比如 `*me` 表示查找所有以”me“结尾的键。

```bash
> KEYS *me
1) "Name"
```

## 五、EXISTS 命令

使用 EXISTS 命令，来判断 name 键，是否存在：

```bash
> EXISTS name
(integer) 0
```

发现输出了 0，表示该键不存在。

使用 EXISTS 命令，来判断 age 键，是否存在：

```bash
> EXISTS age
(integer) 1
```

发现输出了 1，表示该键存在。

## 六、Redis 中存储中文

Redis 中的键和值，都是以二进制的形式存储的，所以键和值都是不支持中文的。

```bash
> SET name 坤坤
"OK"

> GET name
"\xe5\x9d\xa4\xe5\x9d\xa4"
```

发现汉字被转成了二进制。`\x` 表示后面的内容是十六进制的。

如果要正常显示汉字，先退出客户端，然后执行命令：

```shell
redis-cli --raw
```

表示以原始的形式，来显示内容。

然后在使用 GET 命令，获取 name 键对应的值：

```bash
> GET name
坤坤
```

> 使用 clear 命令或者 Ctrl + L，来清空屏幕。

## 七、TTL 命令

使用 TTL（Time To Live 的缩写）命令，来查看一个键的过期时间。

查看 name 键的过期时间：

```bash
> TTL name
(integer) -1
```

发现输出了 -1，表示没有设置过期时间。

## 八、EXPIRE 命令

使用 EXPIRE 命令，设置一个带有过期时间的键。

设置 name 键的过期时间为 10s：

```bash
> EXPIRE name 10
(integer) 1
```

## 九、SETEX、SETNX 命令

或者，使用 SETEX 命令，设置一个带有过期时间的键。

语法：`SETEX 键名 时间(s) 键值`

设置 name 键的过期时间为 5s，键值为 kunkun

```bash
> SETEX name 5 kunkun
"OK"
```

相似的命令还有 SETNX 命令，只有当键不存在时，才设置键的值，如果键已经存在，那么不做任何动作。

使用 TTL 命令，查看 name 键的过期时间，当输出为 -2 时，表示这个键已经过期了。

```bash
> TTL name
(integer) -2

> GET name
(nil)
```

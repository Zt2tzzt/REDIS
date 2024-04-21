# Redis 数据类型 List

List 列表，一般用于存储和操作一组有顺序的数据，和数组的概念类似。

## 一、LPUSH、RPUSH 命令

LPUSH、RPUSH 命令，分别用于将元素添加到列表的头部或者尾部。

语法：`LPUSH key element [element ...]`；`RPUSH key element [element ...]`

添加一个列表 letter，其中有一个元素 a，一个元素 b，三个元素 c、d、e

```bash
> LPUSH letter a
(integer) 1

> LPUSH letter b
(integer) 2

> LPUSH letter c d e
(integer) 5
```

在列表尾部，添加一个元素 f

```bash
> RPUSH letter f
(integer) 6
```

发现 LPUSH 命令，会返回列表的长度。

## 二、LRANGE 命令

LRANGE 命令，用于获取列表的内容：

语法：`LRANGE key start stop`

```bash
> LRANGE letter 0 -1
1) "e"
2) "d"
3) "c"
4) "b"
5) "a"
6) "f"
```

`0`，表示第一个元素；`-1`，表示最后一个元素。这种表示方法，就是获取列表中的所有元素。

## 三、LLEN 命令

LLEN 命令，查看列表的长度。

语法：`LLEN key`

查看键 letter 列表的长度：

```bash
> LLEN letter
(integer) 3
```

## 四、LPOP、RPOP 命令

LPOP、RPOP 命令，分别用于从列表的头部和尾部删除元素。会返回被删除的元素

语法：`LPOP key [count]`；`RPOP key [count]`

使用 RPOP 命令，删除列表尾部的元素。

```bash
> RPOP letter
"f"
```

使用 LPOP 命令，删除列表前面的 2 个元素

```bash
> LPOP letter 2
1) "e"
2) "d"
```

## 五、LTRIM 命令

LTRIM 命令，删除列表中指定范围以外的元素。

语法：`LTRIM key start stop`

创建一个列表 letter，在其中加入元素 a、b、c、d、e

使用 LTRIME 命令，删除索引为 1-3 以外的元素。

```bash
> LTRIM letter 1 3
"OK"

> LRANGE letter 0 -1
1) "d"
2) "c"
3) "b"
```

## 六、简单的消息队列

现在，我们已经可以实现一个简单的消息队列了。

使用 LPUSH 命令，在列表头添加一个元素，再使用 RPOP 命令，在列表尾部删除一个元素。这就是一个简单的先进先出队列。

> 后续会在 Stream 类型中，介绍消息队列的其它实现方式。

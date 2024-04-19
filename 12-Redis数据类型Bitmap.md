# Redis 数据类型 Bitmap（位图）

Bitmap 位图，是字符串类型的扩展，可以使用一个 String 类型，来模拟一个 Bit 数组。

数组的下标，就是偏移量，值只有 0、1。也支持一些位运算，比如：与、或、非、异或

Bitmap 可用来：

- 记录用户的签到情况。
- 记录用户的在线状态。
- 记录用户有没有点过赞。

位图的命令，都以 BIT 开头。

## 一、SETBIT 命令

使用 SETBIT 命令，来设置某个偏移量的值。后面加上一个 key，表示键的名字。

语法：`SETBIT key offset value`

设置一个键 thumbsup，把它在偏移量为 0 的位置，设置成 1；偏移量为 1 的位置，设置为 0。

```bash
> SETBIT thumbsup 0 1
(integer) 0

> SETBIT thumbsup 1 0
(integer) 0
```

这样就设置了一个长度为 2 的位图。

## 二、GETBIT 命令

使用 GETBIT 命令，获取某个偏移量的值：

语法：`GETBIT key offset`

```bash
> GETBIT thumbsup 0
(integer) 1

> GETBIT thumbsup 1
(integer) 0
```

## 三、SET 命令

一位一位的设置，非常麻烦，直接使用字符串的 SET 命令，来设置它的值：

使用 SET 命令，设置键 thumbsup 的前四位为1，后四位为 0。

使用十六进制，来表示二进制的数

```bash
> SET thumbsup "\xF0"
"OK"
```

然后，使用 GETBIT 命令，获取键 thumbsup 前 8 位的值。

```bash
> GETBIT thumbsup 0
(integer) 1

> GETBIT thumbsup 1
(integer) 1

> GETBIT thumbsup 2
(integer) 1

> GETBIT thumbsup 3
(integer) 1

> GETBIT thumbsup 4
(integer) 0

> GETBIT thumbsup 5
(integer) 0

> GETBIT thumbsup 6
(integer) 0

> GETBIT thumbsup 7
(integer) 0
```

发现前四位是 1，后四位是 0。

## 四、BITPOS 命令

使用 BITPOS 命令，获取 key 中第一个出现 0 或 1 的位置。

语法：`BITPOS key bit [start [end [BYTE | BIT]]]`

```bash
> BITPOS thumbsup 0
(integer) 4

> BITPOS thumbsup 1
(integer) 0
```

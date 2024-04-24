# Redis 数据类型 HyprLogLog

HyperLogLog 是用来做基数统计的算法，它并非 Redis 特有的算法。

基数的概念：如果集合中的数，都是唯一，且不重复的。那么这个集合的基数，就是集合中元素的个数。

| value | Hash | HyprLogLog |
| ----- | ---- | ---------- |
| a     | 0001 | 1          |
| b     | 0010 | 2          |
| c     | 0011 | 3          |
| a     | 0001 | 3          |

比如：集合 (1, 2, 3, 4, 5) 的基数是 5。集合 (1, 2, 3, 4, 5, 1, 2, 3, 4, 5) 的基数也是 5。

HyprLogLog 使用随机算法来计算，通过牺牲一定的精确度，来换取更小的内存消耗。

适合用来做对精确度要求不高，但数据量非常大的统计工作。比如：

- 统计网站的独立访客（UV）。
- 统计某个词语的搜索次数。

HyprLogLog 相关的命令，都是以 PF 开头的。

## 一、PFADD 命令

使用 PFADD 命令，添加一个 course 元素

语法：`PFADD key [element [element ...]]`

```bash
> PFADD course redis nginx docker
(integer) 1
```

## 二、PFCOUNT 命令

使用 PFCOUNT 命令，查看 course 的基数。

语法：`PFCOUNT key [key ...]`

```bash
> PFCOUNT course
(integer) 3
```

输出 3，表示 course 的基数是 3。

## 二、PFMERGE 命令

使用 PFMERGE 命令，合并多个 HyprLogLog 元素。

语法：`PFMERGE destkey [sourcekey [sourcekey ...]]`

使用 PFADD 命令，创建一个名为 course2 的元素，在其中添加 git、mysql、node

```shell
> PFADD course2 git mysql node
(integer) 1
```

使用 PFMERGE 命令，合并 course 和 course2 到 result 中。

```bash
> PFMERGE result course course2
"OK"
```

查看 result 的基数：

```bash
> PFCOUNT result
(integer) 6
```

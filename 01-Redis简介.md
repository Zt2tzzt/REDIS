# Redis 简介

Redis（Remote dictionary Server）是一个开源的，基于内存的，数据存储系统。

它可以用于数据库、缓存、消息队列等等各种场景。也是目前最热门的 NoSQL 数据库之一。

随着数据访问量的增大，MySQL 数据库的瓶颈，也越来越明显。这主要是因为磁盘 I/O 导致的。

磁盘 I\O 的读写操作速度，与内存相比，是非常慢的。如果能把数据存储在内存中，那么就能大大提高它的性能。于是就有了 Redis 这种基于内存的数据存储系统。

## 一、Redis 支持的数据结构

五种基础数据类型：

| 类型      | 说明     |
| --------- | -------- |
| String    | 字符串   |
| List      | 列表     |
| Set       | 集合     |
| SortedSet | 有序集合 |
| 哈希      | Hash     |

五种高级数据类型：

| 类型       | 说明     |
| ---------- | -------- |
| Stream     | 消息队列 |
| Geospatial | 地理空间 |
| HyperLog   | Log      |
| Bitmap     | 位图     |
| Bitfield   | 位域     |

## 二、Redis 的使用方式

方式一：CLI（Command Line Interface），命令行。就是通过 Redis-CLI 命令行工具，来使用 Redis。

方式二：API（Application Programming Interface），应用程序。

方式三：GUI（Graphical User Interface），图形界面。

## 三、Redis 的优势

优势一：性能极高。

优势二：数据类型丰富，单键值对最大支持 512M 大小的数据。

优势三：简单易用，支持所有主流编程语言。

优势四：支持数据持久化，主从复制，哨兵模式等高可用特性。

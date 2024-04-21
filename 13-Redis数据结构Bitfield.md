# Redis 数据结构位域 Bitfield

位域 Bitfield，能够将很多小的整数，存储到一个较大的位图中。这样就可以高效的使用内存。

比如可以用来记录玩家在游戏中的关键信息：Level（等级）、金钱（Money）、Exp（经验）

使用命令 BITFIELD

```bash
> BITFIELD player:1 set u8 #0 1
1) "0"
```

- `player:1`，表示 1 号玩家。
- `u8`，表示一个 8 位的无符号整数。
- `#0`，表示第一个位置。
- `1`，表示玩家的等级为 1.

使用命令 GET，获取内存中 `player:1` 的情况。

```bash
> GET player:1
"\x01"
```

返回的 `\x01`，表示十六进制的 1，表示 player:1 的等级是 1.

使用 BITFIELD 命令，获取 player:1 玩家的等级：

```bash
> BITFIELD player:1 get u8 #0
1) "1"
```

---

为 player:1 玩家设置 100 金钱，使用一个无符号整数，32 位整数来表示。

```bash
> BITFIELD player:1 set u32 #1 100
1) "0"
```

- `player:1`，表示 1 号玩家。
- `u32`，表示一个 32 位的无符号整数。
- `#1`，表示第二个位置。
- `100`，表示玩家的金钱为 100.

使用命令 GET，获取内存中 `player:1` 的情况。

```bash
> GET player:1
"\x01\x00\x00\x00\x00\x00\x00d"
```

100 转成字符就是 "d"，“d”的 ASCll 码就是 100.

使用 BITFIELD 命令，获取 player:1 玩家的金钱：

```bash
> BITFIELD player:1 get u32 #1
1) "100"
```

使用 BITFIELD 命令，为 player:1 玩家加 100 金钱：

```bash
> BITFIELD player:1 incrby u32 #1 100
1) "200"
```

---

使用 BITFIELD 命令，设置 player:1 玩家的等级为 2：

```bash
> BITFIELD player:1 set u16 #2 2
1) "0"
```

使用命令 GET，获取内存中 `player:1` 的情况。

```bash
> GET player:1
"\x01\x00\x00\x00\x00\x02\x00\xc8"
```

使用 BITFIELD 命令，获取 player:1 玩家的等级：

```bash
> BITFIELD player:1 get u16 #2
1) "2"
```


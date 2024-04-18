# Redis 发布订阅模式

Redis 提供了发布订阅的功能，

- 可以通过 PUBLISH 命令，将消息发送到指定的频道。
- 然后通过 SUBSCRIBE 命令，来订阅这个频道

这样就可以接收到这个频道的消息了。

## 一、SUBSCRIBE 命令

语法：`SUBSCRIBE channel [channel ...]`

打开一个终端，在其中执行 SUBSCRIBE 命令，订阅一个频道 zetian

```bash
> SUBSCRIBE zetian
subscribe
zetian
1
```

订阅频道的终端，可以有多个。

## 二、PUBLISH 命令

语法：`PUBLISH channel message`

打开另一个终端，使用 PUBLISH 命令，发布一个消息"haha"，到 zetian 频道

```bash
> PUBLISH zetian haha
1
```

观察订阅终端的变化：

```bash
> SUBSCRIBE zetian
subscribe
zetian
1
message
zetian
haha
```

## 三、局限性

发布订阅功能的局限性：

- 消息无法持久化。
- 无法记录历史消息。

要解决这些问题，需要使用 Stream 流。
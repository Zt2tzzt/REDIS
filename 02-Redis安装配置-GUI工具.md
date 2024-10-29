# Redis 安装配置

## 一、Redis 安装

### 1.Mac

使用 Homebrew 安装，执行命令：

```shell
brew install redis
```

### 2.Linux

使用系统自带的安装工具安装，执行命令：

```shell
# CentOS
yum install redis

# Ubuntu
apt update
apt install redis-server
```

如果你在 Ubuntu 上安装的 Redis 版本过低，可以尝试以下方法进行更新：

Ⅰ、添加 Redis PPA

在终端中执行以下命令，添加 Redis PPA：

```bash
sudo add-apt-repository ppa:redislabs/redis
```

Ⅱ、更新 apt cache

执行以下命令更新 apt cache：

```bash
sudo apt-get update
```

Ⅲ、安装最新版本 Redis

执行以下命令安装最新版本 Redis：

```bash
sudo apt-get install redis
```

四、安装完成后，你可以执行以下命令查看 Redis 版本：

```bash
redis-server -v

# 或者

redis-cli
INFO SERVER
```

### 3.Windows

方式一：WSL，参考上方 Linux 的安装方式。

方式二（不推荐）：.exe 安装文件安装。

### 4.Docker

查看 Redis 在 Docker 中的[官方文档](https://hub.docker.com/_/redis)。

执行命令：

```shell
docker run -d \
  --name redis \
  -p 6379:6379 \
  -e REDIS_PASSWORD=yourpassword \
  redis
```

redis 容器创建后，设置密码：

```sh
docker exec -it redis redis-cli
127.0.0.1:6379> CONFIG SET requirepass newpassword
```

> Redis 中没有用户的概念，只需使用密码就可连接。

## 二、Redis 启动

Mac、Linux 在终端启动 Redis，执行命令：

```shell
redis-server
```

Windows 在 CMD 命令行中启动 Reids，执行命令：

```shell
redis-server.exe
```

打开一个新的终端，用 Redis 的客户端，进入 Redis 命令行客户端，执行命令：

```shell
redis-cli
```

## 三、Redis GUI 工具

RedisInsight 是 Redis 官方出品的可视化管理工具。

在连接管理器中，配置要连接的 Redis 服务的地址、端口号。点击连接 Redis 服务。

左侧上方工具栏从上至下依次是：

1. 【连接管理器】，用来管理 Redis 服务，比如可以添加一个新的 Redis 服务。
2. 【浏览器Browser】，用来查看 Redis 服务里面的各种数据。
3. 【工作台】，里面包含一些手册和教程，上方是一个命令执行窗口，可以在此输入 Redis 命令，点击右侧执行按钮执行命令。下方查看命令执行结果。
4. 【分析工具】，用来查看 Redis 的性能，运行日志等等。
5. 【发布订阅模式的工具】，后续介绍。

点击左下方【>_CLI】，打开一个命令行窗口。和打开终端后执行 `redis-cli` 命令的效果是一样的。

点击左下方【Command Helper】，打开一个命令的帮助文档，可在搜索框中输入要搜索的命令，在 CLI 窗口中输入的命令，也会在该文档中显示对应的说明页面。

点击左下方【Profilter】，打开一个执行日志工具，用来查看 Redis 的执行日志。


# Redis 数据类型 Geospatial

Geospatial 是 Redis 3.2 新增的数据类型，用于存储地理位置信息。

支持对地理位置进行各种计算操作。比如：

- 计算两个地理位置之间的距离。
- 某个位置的经纬度。
- 查找附近的人。

地理位置相关的命令，都以 GEO 开头。

## 一、GEOADD 命令

使用 GEOADD 命令，添加一个地理位置信息。

语法：`GEOADD key [NX | XX] [CH] longitude latitude member [longitude latitude member ...]`

添加一个深圳的地理位置信息：

```bash
> GEOADD city 114.0545429 22.5445741 shenzhen
(integer) 1
```

- `city` 是 Geospatial 的键，表示城市的位置。
- `114.0545429 22.5445741`，分别表示经度、纬度。
- `shenzhen`，表示 city 键的成员。

一次性添加多个地理位置信息：

```bash
> GEOADD city 116.405285 39.904989 beijing \
  113.27324 23.15792 guangzhou \
  121.48941 31.40527 shanghai
(integer) 3
```

输出 3，表示成功添加 3 条信息。

## 二、GEOPOS 命令

使用 GEOPOS 命令，来获取地理位置的经纬度。

语法：`GEOPOS key [member [member ...]]`

获取北京的经纬度：

```bash
> GEOPOS city beijing
1) 1) "116.40528291463851929"
   2) "39.9049884229125027"
```

输出的第一个值，表示经度；第二个值，表示纬度。

## 三、GEODIST 命令

使用 GEODIST 命令，计算两个地理位置之间的距离：

语法：`GEODIST key member1 member2 [M | KM | FT | MI]`

计算北京和上海之间的直线距离。

```bash
> GEODIST city beijing shanghai
"1050646.0382"
```

输出的是两地理位置之间的直线距离，默认单位是米（m）。

返回以千米（km）为单位的距离：

```bash
> GEODIST city beijing shanghai km
"1050.6460"
```

## 四、GEOSEARCH 命令

使用 GEOSEARCH 命令，搜索指定范围内的成员并返回。

语法：`GEOSEARCH key FROMMEMBER member | FROMLONLAT longitude latitude BYRADIUS radius M | KM | FT | MI | BYBOX width height M | KM | FT | MI [ASC | DESC] [COUNT count [ANY]] [WITHCOORD] [WITHDIST] [WITHHASH]`

这个范围可以以成员的位置，或者一个指定的经纬度为中心，按照圆形，或者矩形的方式来搜索。

搜索距离上海 300km 以内的城市。

```bash
> GEOSEARCH city FROMMEMBER shanghai BYRADIUS 300 km
1) "shanghai"
```

除了 GEOSEARCH  命令以外，还可以使用 GEORADIUS、GEORADIUSBYMEMBER 命令来实现同样的功能。

也可以使用 `BYBOX` 关键字，来表示一个矩形的范围。

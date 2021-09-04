# pg处于inactive状态

## 一、故障现象

使用**ceph health**查询集群状态时，出现如下统计信息：

```
[root@node0 ~]# ceph health 
HEALTH_WARN Reduced data availability: 64 pgs inactive; Degraded data redundancy: 23389/70167 objects degraded (33.333%), 64 pgs degraded, 64 pgs undersized
```

当PG处于**inactive**状态，意味着：它将不再响应客户端的I/O请求；


## 二、原因分析

通常都是由于OSD down引起。


## 三、处理过程

### 第一步：定位异常OSD；

```
[root@node0 ~]# ceph osd tree | grep down 
3   hdd 0.09769         osd.3    down        0 1.00000 
4   hdd 0.09769         osd.4    down        0 1.00000 
5   hdd 0.09769         osd.5    down        0 1.00000
```

### 第二步：修复OSD。
详见相关章节。

## 四、故障构建
- 方法一：关闭OSD，让某些PG对应的OSD数量低于**min_size**数。


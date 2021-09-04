# pg处于stale状态


## 一、故障现象

执行`ceph -s`可以看到如下信息：

```
[root@node0 ~]# ceph -s 
cluster:
id:     8bd1a02d-5c91-464c-86ee-7ff61405ce77
health: HEALTH_WARN
3 osds down
Reduced data availability: 4 pgs inactive, 4 pgs peering, 4 pgs stale
……
```


## 二、原因分析

PG被标记为stale，说明无法找到该PG的主OSD，或者monitor一直无法收到主PG更新PG状态信息。

出现这种状态，通常有如下可能性：
- 在集群启动后，PG peering一直无法完成；
- 集群运行过程中，PG对应故障域内的OSD在短时间（故障恢复未完成前）内全部故障。


## 三、处理过程


### 第一步：首先确定处于stale状态的PG，以及它所对应的OSD；

```
[root@node0 ~]# ceph health detail | grep stale
HEALTH_WARN Reduced data availability: 4 pgs inactive, 4 pgs peering, 4 pgs stale; Degraded data redundancy: 16602/70167 objects degraded (23.661%), 34 pgs degraded, 34 pgs undersized
PG_AVAILABILITY Reduced data availability: 4 pgs inactive, 4 pgs peering, 4 pgs stale
pg 1.0 is stuck stale for 808.238834, current state stale+peering, last acting [7]
pg 1.9 is stuck stale for 808.238820, current state stale+peering, last acting [7]
pg 1.20 is stuck stale for 808.238792, current state stale+peering, last acting [7]
pg 1.2a is stuck stale for 808.238764, current state stale+peering, last acting [7]
```

从上述截图可以得到两点信息：
- 处于stale状态的PG共有4个；
- PG异常前，最后一个正常的OSD；


### 第二步：修复相关OSD

根据确定的OSD，进行相关修复。


## 四、故障构建

方法一：使用ceph pg map确定某个PG所对应的所有OSD，关闭或者kill掉所有对应的OSD。

方法二：集群启动时，将某PG对应的所有OSD均不启动。



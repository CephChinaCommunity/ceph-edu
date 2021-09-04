# pg处于down状态

## 一、故障现象

执行`ceph -s`得到如下信息：

```
[root@node0 ~]# ceph -s 
  cluster:
    id:     8bd1a02d-5c91-464c-86ee-7ff61405ce77
    health: HEALTH_WARN
            Reduced data availability: 3 pgs inactive, 4 pgs down
……
```

## 二、原因分析

出现该现象意味着PG**无法完成peering**，通常都是由于OSD异常引起，如反复重启OSD等。


## 三、处理过程

### 第一步：获取异常PG；

```
[root@node0 ~]# ceph health detail | grep down 
HEALTH_WARN Reduced data availability: 4 pgs inactive, 4 pgs down
PG_AVAILABILITY Reduced data availability: 4 pgs inactive, 4 pgs down
    pg 1.0 is down, acting [8,4,2]
    pg 1.9 is down, acting [2,5,6]
    pg 1.20 is down, acting [2,4,6]
    pg 1.2a is down, acting [2,5,6]
```
 
### 第二步：确认PG无法**peering**的原因；

使用`ceph pg <pg_id> query`查询：

```
[root@node0 ~]# ceph pg 1.0 query
{
    "state": "down",
        "snap_trimq": "[]",
        "snap_trimq_len": 0,
        "epoch": 591,
        ……
            "probing_osds": [
            "2",
        "4",
        "8"
            ],
            "blocked": "peering is blocked due to down osds",
            "down_osds_we_would_probe": [
                3,
            7
                ],
             "peering_blocked_by": [
             {
                 "osd": 7,
                 "current_lost_at": 0,
                 "comment": "starting or marking this osd lost may let us proceed"
             }
             ]
},
……
```

从上可以发现是由于OSD.3和OSD.7 **Down**导致**peering被阻塞**。

 
### 第三步：按提示原因修复对应OSD。



## 四、故障构建

关闭PG对应的OSD组中的某一个OSD，在PG未恢复成*active+clean*前写入数据，再关闭剩余OSD，并启动之前关闭的OSD。


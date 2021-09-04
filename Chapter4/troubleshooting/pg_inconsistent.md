# pg处于inconsistent状态

## 一、故障现象

执行`ceph health detail`可以看到如下信息：

```
$ ceph health detail
HEALTH_ERR 1 pgs inconsistent; 2 scrub errors
pg 0.6 is active+clean+inconsistent, acting [0,1,2]
2 scrub errors
```

## 二、原因分析

有三种情况会出现数据不连续：
- 硬盘坏道，导致PG数据校验（**scrub**、**deep-scrub**）时校验失败；
- 数据写入或者修改时不同步等导致副本之间校验不一致；
- 节点时间不同步，导致周期性出现**active+clean+inconsistent**。


## 三、处理过程

### 第一步：找到处于**inconsistent**的异常PG；

```
$ ceph health detail
HEALTH_ERR 1 pgs inconsistent; 2 scrub errors
pg 0.6 is active+clean+inconsistent, acting [0,1,2]
2 scrub errors
```

在本例中，PG 0.6异常。

### 第二步：确定出现异常原因;

方法有二：

- 方法一：手动启动**scrub**，根据异常日志判断；
 
```
$ ceph pg deep-scrub 0.6
$ grep scrub /var/log/ceph/ceph-mgr.node0.log
```

- 方法二：直接列出异常对象（*object*），根据相关提示判断。
 
```
$ rados list-inconsistent-obj 0.6 --format=json-pretty
```

### 第三步：进行PG修复。

当由于磁盘原因导致数据不一致时，可以使用**repair**命令行自动修复，除此之外则需要手动判断主副本再进行修复。以下分情况说明：

- 当出现如下错误信息或者日志时，可以尝试使用`ceph pg repair <pgid>`自动修复；

```
missing attr
digest 0 != known digest
size 0 != known size
deep-scrub stat mismatch
candidate had a read error
```

- 当出现如下错误时，需要手动修复。

```
digest <digest> != known digest <digest>         // 非0
omap_digest <digest> != known omap_digest <digest>
```

即有确切的非0值时，可以判断非硬件原因引起的不一致，需人工来判断。
人工判断出正确数据版本后，可以使用**ceph-objectstore-tool**工具手动进行副本替换。


## 四、故障构建
略


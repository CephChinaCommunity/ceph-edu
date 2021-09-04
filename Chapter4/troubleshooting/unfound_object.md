# unfound object

## 一、故障现象

通过**ceph health**命令查询，可以获取相关故障告警信息如下：

```
$ ceph health detail
HEALTH_WARN 1 pgs degraded; 78/3778 unfound (2.065%)
pg 2.4 is active+degraded, 78 unfound
```

出现此告警意味着出现数据块丢失，即该数据块的元数据存在，但集群中找不到对应副本；


## 二、原因分析

通常是由于OSD的频繁上下线引起，如在集群做数据恢复过程中，存在完整数据副本的OSD掉线。

## 三、处理过程

### 第一步：确认异常PG；

```
$ ceph health detail | grep unfound
```

### 第二步：分场景定位原因并修复。

- 如果**unfound**的数量很大，则可判断大概率是由于OSD离线引起，使用如下命令定位离线OSD，并进行OSD修复。

```
$ ceph pg 2.4 query
```

在*recovery_state*下可以看到如下提示：

```
"recovery_state": [
{
    "name": "Started/Primary/Active",
        "enter_time": "2021-08-28 10:36:48.758889",
        "might_have_unfound": [
        {
            "osd": "1",
            "status": "already probed"
        },
        {
            "osd": "3",
            "status": "osd is down"
        },
        {
            "osd": "6",
            "status": "osd is down"
        }
    ],
```
根据提示修复对应的OSD。

- 如果**unfound**数量较小，可进行单独的对象定位，如果能人工判断正确副本，可用正确副本进行修复。
 
```
$ ceph pg 2.4 list_unfound | head -n 20
{
    "num_missing": 817,
    "num_unfound": 817,
    "objects": [
    {
        "oid": {
            "oid": "benchmark_data_node0_2866182_object42955",
            "key": "",
            "snapid": -2,
            "hash": 2847686659,
            "max": 0,
            "pool": 1,
            "namespace": ""
        },
        "need": "711'1502",
        "have": "0'0",
        "flags": "none",
        "locations": []
    },

```

获取丢失对象的*id*，配合**ceph-objectstore-tool**工具进行数据块，若实在无法找到，可使用如下命令进行自动处理（存在数据丢失风险）：

```
$ ceph pg 2.4 mark_unfound_lost <revert|delete>
```

## 四、故障构建

方法有二：
- 1）设置集群norecover，关闭PG中的某个OSD，向对应的存储池写入数据，恢复关闭的OSD，待该PG peering完成处于active状态后，关闭PG中剩余的OSD。
- 2）在磁盘上手动删除某个对象的所有副本，手动触发scrub。



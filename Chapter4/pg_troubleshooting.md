# 各种PG异常及解决

在Ceph运维过程中，通过`ceph -s`或者`ceph health detail`查询可以看到PG相关状态信息，按照`ceph health`显示的状态划分有如下几种常见情况：

| HEALTH_ERR       | HEALTH_WARN |
|------------------|-------------|
| pgs down         | pgs stale   |
| pgs inconsistent | unfound     |
| scrub errors     | incomplete  |


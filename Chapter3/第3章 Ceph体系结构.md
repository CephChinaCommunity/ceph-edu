Ceph 是一个开源的、分布式的、可扩展的、软件定义的存储(SDS)系统，它可以提供块、对象和文件存储。通过使用 CRUSH 算法，Ceph 不再需要集中的元数据，并且可以将负载分布到集群中的所有节点。Ceph 是一个纯粹的 SDS 解决方案，因此意味着只要围绕数据一致性提供了正确的保证，我们就可以在普通硬件上自由地运行它。

Ceph 主要提供 3 种类型的存储，通过 RBD 协议提供块设备使用，通过 CEPHFS 提供文件存储使用，通过 RADOS 网关提供 S3 协议及 Swift 协议兼容的对象存储使用。

<center> <img src="https://docs.ceph.com/en/nautilus/_images/stack.png"> <br> <div style="color:orange; border-bottom: 1px solid #d9d9d9; display: inline-block; color: #999; padding: 2px;">图3-1 Ceph体系结构</div> </center>

一个 Ceph 存储集群至少需要一个 Ceph Monitor、Ceph Manager 和 Ceph OSD（对象存储守护进程）。 运行 Ceph 文件系统客户端时也需要 Ceph 元数据服务器。
# TiDB 体系结构

![figure2-tidb-architecture](../assets/figure2-tidb-architecture.png)

_**图 2：TiDB 体系结构**_

本节将会描述 TiDB 的体系结构，如 *图 2* 所示。TiDB 支持 MySQL 协议，并且可以用 MySQL 兼容的客户端访问。三个核心组件分别是：分布式存储层、布局驱动（Placement Driver, PD）和计算引擎层。

分布式存储层由行式存储（TiKV）和列式存储（TiFlash）组成。从逻辑上讲，存储在 TiKV 中的数据是一个有序的键值映射。每个元组都映射成一个键值对。关键字由行数据的表标识符（table ID）和行标识符（row ID）组成，值对应实际的行数据，其中表标识符和行标识符是唯一的整数，行标识符来自主键列（primary key column）。四列元组的 key-value 示例：

```
Key: {table{tableID} record{rowID}}
Value: {col0, col1, col2, col3}
```

为了能够横向扩展，采用范围分区（range partition）策略，将大键值映射分割成许多连续的范围（range），每个范围称为一个区块（region）。每个区块都存有多个副本来保证高可用性。Raft 共识算法用于维护每个区块的副本之间的一致性，从而形成 Raft 组。不同 Raft 组中的 Leader 会从 TiKV 异步复制数据到 TiFlash 。TiKV 和 TiFlash 都可以部署在单独的物理资源中，因此在处理事务和分析查询时可以提供隔离保证。

布局驱动（PD）负责管理区块，包括提供每个键的区块和物理位置，以及自动移动区块以平衡工作负载。PD 也是时间戳分配器，提供严格递增且全局唯一的时间戳。这些时间戳也可以作为事务的标识符。为了提高稳定性和性能，布局驱动同样可以包含多个布局驱动成员。布局驱动不具有持久状态，启动时布局驱动成员会从其他成员和 TiKV 节点读取所有必要的状态信息。

计算引擎层是无状态、可扩展的。定制的 SQL 引擎由基于成本的查询优化器（cost-based query optimizer）和分布式查询执行器（distributed query executor）组成。TiDB 基于 Percolator [33] 实现了两阶段提交（2PC）协议，以支持事务处理。查询优化器可以根据查询最佳地选择从 TiKV 还是 TiFlash 中读取。

TiDB 体系结构符合 HTAP 数据库的要求。每个组件均设计成具有高可用性和可扩展性。存储层使用 Raft 算法来实现数据副本之间的一致性。TiKV 和 TiFlash 之间的低延迟复制使新数据可用于分析查询。查询优化器，以及 TiKV 和 TiFlash 之间的强一致性数据，可以提供快速的分析查询处理，而对事务处理的影响很小。

除了上述组件，TiDB 还与 Spark 集成，这有助于集成 TiDB 和 Hadoop 分布式文件系统（HDFS）中存储的数据。TiDB 具有丰富的生态系统工具集，可以将数据导入到 TiDB 或从 TiDB 中导出数据，抑或将数据从其他数据库迁移到 TiDB 。

在接下来的部分中，我们将深入研究分布式存储层、SQL 引擎和 TiSpark ，以展示 TiDB 这一生产就绪的 HTAP 数据库的功能。

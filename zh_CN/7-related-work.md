# 相关工作

构建 HTAP 系统的常见方法有：从现有数据库演变而来，对开源分析系统进行扩展，或者从零开始构建。TiDB 是从零开始构建的，在架构、数据来源、计算引擎和一致性保证方面不同于其他系统。

**从现有数据库演变而来** 成熟的数据库可以在现有产品的基础上提供 HTAP 解决方案，这类系统格外注重加速分析查询，并采用定制方式来分别实现数据一致性和高可用性。相比之下，TiDB 自然受益于 Raft 中的日志复制，以实现数据一致性和高可用性。

Oracle [19] 在 2014 年推出了内存数据库选项，成为业界第一个双格式、内存式关系数据库管理系统。该选项旨在打破分析工作负载中的性能障碍，而不影响（甚至提高）常规事务工作负载的性能。列式存储作为一个只读快照存在，在某个时间点保持一致，并使用完全在线的重新填充机制进行更新。Oracle 在后来的工作中 [27] 展示了其分布式架构的高可用性方面，并提供了容错的分析查询执行。

SQL Server [21] 在其核心中集成了两个专门的存储引擎：用于分析工作负载的 Apollo 列式存储引擎和用于事务工作负载的 Hekaton 内存引擎。数据迁移任务定期将数据从 Hekaton 表的尾部复制到压缩的列式存储中。SQL Server 使用列式存储索引加上批处理来高效地处理分析查询，利用 SIMD [15] 进行数据扫描。

SAP HANA 支持高效地执行单独的 OLAP 和 OLTP 查询，并为每个查询使用不同的数据组织形式。为了提高 OLAP 性能，它将行格式存储数据异步复制到分布在服务器集群上的列式存储中 [22] 。这种方法为 MVCC 数据提供了亚秒级的可视性。然而，处理错误和保持数据一致性需要付出相当大的努力。更严重的是，其事务引擎缺乏高可用性，因为它只部署在单个节点上。

**对开源分析系统进行扩展** Apache Spark 是一个开源的数据分析框架。它需要一个事务模块来实现 HTAP 。下面列出的许多系统都遵循这一思想。TiDB 并不十分依赖 Spark ，因为 TiSpark 只是扩展程序。即便没有 TiSpark ，TiDB 仍然是一个独立的 HTAP 数据库。

Wildfire [10，9] 构建了一个基于 Spark 的 HTAP 引擎。在同一个列式数据存储（即，Parquet）上处理分析和事务请求。采用 last-write-wins（保留最后更新）策略进行并发更新，并使用快照隔离进行读取。为了实现高可用性，分片日志被复制到多个节点，但没有使用一致性算法。分析查询和事务处理可以在不同的节点上进行；但是，在处理最新更新时会有明显的延迟。Wildfire 针对大规模 HTAP 工作负载使用统一的多版本和多区域索引方法 [23] 。

SnappyData [25] 为 OLTP、OLAP 和流分析提供了一个统一平台。将用于高吞吐量分析的计算引擎（Spark）和横向扩展的内存事务存储（GemFire）。最近更新会先以行格式进行存储，然后写入用于分析查询的列格式。事务处理遵循 2PC（两阶段提交）协议，使用 GemFire 的 Paxos 实施来确保集群达成共识并提供一致的视图。

**从零开始构建**。许多新的 HTAP 系统从不同方面研究混合工作负载，包括利用内存计算来提高性能、优化数据存储和提高可用性。与 TiDB 不同的是，它们不能同时提供高可用性、数据一致性、可扩展性、数据实时更新和隔离。

MemSQL [3] 的引擎被设计为用于可扩展的内存中 OLTP 和快速分析查询。MemSQL 可以以行格式或列格式存储数据库表。也可以将部分数据保存为行格式，并根据需要将其转换为列格式，以便在将数据写入磁盘时进行快速分析。它将重复查询编译成低级机器代码，以加速分析查询，并使用许多无锁结构来帮助事务处理。但是，在运行 HTAP 工作负载时，它不能为 OLAP 和 OLTP 提供隔离性能。

HyPer [18] 使用操作系统的 `fork` 系统调用为分析工作负载提供快照隔离。它的新版本采用 MVCC（多版本并发控制）实现，以提供可序列化、快速跨动作处理和快速扫描。ScyPer [26] 使用传播更新或者是逻辑/物理上的 redo log（重做日志），进一步扩展 HyPer 以评估远程副本上的大规模分析查询。

BatchDB [24] 是为 HTAP 工作负载设计的内存数据库引擎。依赖于带有专用副本的主从复制，每个副本都针对特定的工作负载类型（即 OLTP 或 OLAP ）进行了优化。最大限度地减少了事务引擎和分析引擎之间的负载交互，从而能够在 HTAP 工作负载的严格 SLA（服务级别协议）下对新数据进行实时分析。但要注意的是，它对行格式副本执行分析查询，并且不保证高可用性。

基于数据沿袭的数据存储（L-Store）[35] 通过引入一个更新友好的、基于数据沿袭（lineage-based）的存储体系结构，将实时分析和事务查询处理结合在一个统一的引擎中。该存储实现了对本地多版本列式存储模型的无争用更新机制，以便将稳定的数据从写优化的列格式延迟且独立地转移到读优化的列布局中。

Peloton [31] 是一个自驱动的 SQL 数据库管理系统。它试图在运行时为 HTAP 工作负载调整数据来源 [8] 。使用无锁、多版本并发控制来支持实时分析。但是，根据设计，这是一个单节点内存数据库。

Cockroach DB [38] 是一个分布式数据库，提供高可用性、数据一致性、可扩展性和隔离。和 TiDB 一样，也建立在 Raft 算法之上，并支持分布式事务。具有更强的隔离特性：可序列化，而不是快照隔离。但是，它不支持专用的 OLAP 或 HTAP 功能。
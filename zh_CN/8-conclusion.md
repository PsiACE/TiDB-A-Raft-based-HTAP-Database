# 结论

本文介绍了一个可用于生产环境的 HTAP 数据库：TiDB 。TiDB 建立在 TiKV 之上，而 TiKV 是一个基于 Raft 共识算法的分布式行式存储。本研究向 Raft 共识算法引入了用于实时分析的列式学习者，可以从 TiKV 异步复制日志，并将行格式的数据转换为列格式。TiKV 和 TiFlash 之间的这种日志复制提供了实时数据一致性，但只需要很少的开销。TiKV 和 TiFlash 可以部署在独立的物理资源上，以高效地完成事务性和分析性的查询。TiDB 可以最佳地选择 TiKV 行存或者 TiFlash 列存，以便在事务性和分析性查询扫描表时进行访问。实验结果表明，TiDB 在 CH-benCHmark HTAP 基准下表现良好。TiDB 提供了一个将 NewSQL 系统演化为 HTAP 系统的通用解决方案。

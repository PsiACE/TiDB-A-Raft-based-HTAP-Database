# 摘要

混合事务和分析处理（HTAP）数据库要求隔离地处理事务和分析查询，以消除它们之间的干扰。为了实现这一点，有必要为两类查询所特定数据维护不同副本。然而，为存储系统中的分布式副本提供一致的视图是一项挑战，在该系统中，分析请求可以大规模、高可靠地从事务性工作负载中高效读取一致的实时数据。

为了应对这一挑战，本研究建议扩展基于复制状态机的共识算法，为 HTAP 工作负载提供一致的副本。基于这个新颖的想法，提出了一个基于 Raft 的 HTAP 数据库：TiDB 。在数据库中，设计了由行式存储和列式存储共同组成的 multi-Raft 存储系统。行式存储基于 Raft 算法构建。能够高可靠地从事务请求中实现更新。特别是，它异步地将 Raft 日志复制到学习者节点 Learner（译者注：这是对 Raft 共识机制进行扩展后引入的新成员），Learner 将行格式转换为列格式存储，从而形成实时可更新的列式存储。这一列式存储允许分析查询高效地读取与行式存储一致且实时更新的数据，并与行式存储中的处理事务达成强隔离。在这一存储系统之上，构建了一个 SQL 引擎来处理大规模分布式事务和高成本的分析查询。SQL 引擎以最佳方式访问数据的行格式副本和列格式副本。该存储系统还包括功能强大的分析引擎 TiSpark ，以帮助 TiDB 连接到 Hadoop 生态系统。综合实验表明，在专注于 HTAP 工作负载的 CH-benCHmark 基准下，TiDB 实现了资源隔离情形下的高性能。

**PVLDB 引用格式**

Dongxu Huang, Qi Liu, Qiu Cui, Zhuhe Fang, Xiaoyu Ma, Fei Xu, Li Shen, Liu Tang, Yuxing Zhou, Menglong Huang, Wan Wei, Cong Liu, Jian Zhang, Jianjun Li, Xuelian Wu, Lingyu Song, Ruoxi Sun, Shuaipeng Yu, Lei Zhao, Nicholas Cameron, Liquan Pei, Xin Tang. TiDB: A Raft-based HTAP Database. PVLDB, 13(12): 3072-3084, 2020.

DOI: https://doi.org/10.14778/3415478.3415535

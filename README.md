# TiDB：基于 Raft 共识算法的 HTAP 数据库

> 为学读书，须是耐心，细意去理会，切不可粗心。为数重物，包裹在里面，无缘得见。必是今日去一重，又见得一重。明日又去一重，又见得一重。去尽皮，方见肉。去尽肉，方见骨。去尽骨，方见髓。（朱熹）

本 Repo 系我个人本科毕业论文开题准备工作之外文参考文献翻译，所选篇目为《TiDB: A Raft-based HTAP Database 》，目前在 GitHub 上同步进度。

**特别声明** 翻译的内容仅代表个人理解，不代表 PingCAP，内容的最终解释权归 PingCAP 所有。

## 原文简介

PingCAP 团队的论文《TiDB: A Raft-based HTAP Database 》入选 VLDB 2020 ，成为业界第一篇 Real-time HTAP 分布式数据库工业实现的论文。在本篇论文中，PingCAP 重点介绍了其研发的 TiDB 作为一款定位于在线事务处理和在线实时分析（HTAP）混合负载融合型分布式数据库产品的系统架构和核心特性。（摘选自《PingCAP 开源分布式数据库 TiDB 论文入选 VLDB》）

本论文原文见：http://www.vldb.org/pvldb/vol13/p3072-huang.pdf

本 Repo 中存有副本，位于 [`p3072-huang.pdf`](./p3072-huang.pdf)。

**原论文 PVLDB 引用格式**

```
Dongxu Huang, Qi Liu, Qiu Cui, Zhuhe Fang, Xiaoyu Ma, Fei Xu, Li Shen, Liu Tang, Yuxing Zhou, Menglong Huang, Wan Wei, Cong Liu, Jian Zhang, Jianjun Li, Xuelian Wu, Lingyu Song, Ruoxi Sun, Shuaipeng Yu, Lei Zhao, Nicholas Cameron, Liquan Pei, Xin Tang. TiDB: A Raft-based HTAP Database. PVLDB, 13(12): 3072-3084, 2020.

DOI: https://doi.org/10.14778/3415478.3415535
```

## 相关信息

本译文由 Chojan Shang - [@PsiACE](https://github.com/psiace) - <psiace@outlook.com> 和任何参与此项目的贡献者共同发布。

**项目地址** https://github.com/PsiACE/TiDB-A-Raft-based-HTAP-Database

**赞助相关**

- Paypal: https://paypal.me/psiace
- Afdian: https://afdian.net/@psiace [爱发电 - 国内]


## 许可协议

- 本 Repo 中所涉原文以及文献之 PDF 的一切权利归原作者及其团队所有，仅供学习对照使用。原始论文以 [`CC-BY-NC-ND-4.0`](https://creativecommons.org/licenses/by-nc-nd/4.0/) 发布。
- 中文译本以 `CC-BY-4.0` 许可协议进行发布，请查阅 [`LICENSE`](./LICENSE) 文件以获取更多信息。

---
title: "欢迎来到新数据库时代"
subtitle: "新型数据库云服务的出现"
summary: "在 Oracle Database、MySQL、SQL Server 几乎统治数据库领域几十年后，新一轮数据库的创新发展浪潮已经开始。"
date: 2021-10-05T17:24:38+08:00
tags: ['CloudDatabase','CloudServer','SaaS']
categories: ['Database']
featuredImage: https://miro.medium.com/v2/resize:fit:1400/format:webp/1*1H2HJ45Zh76WlBbdJw8wyg.jpeg
featuredImagePreview: https://miro.medium.com/v2/resize:fit:1400/format:webp/1*1H2HJ45Zh76WlBbdJw8wyg.jpeg
draft: true
---

> 本文是一篇翻译练习，原文来自 [Welcome to the New Database Era](https://ethanjb.medium.com/welcome-to-the-new-database-era-f4f8c8c407e1)

## 新型数据库云服务的出现

**The new category of cloud database services emerging**

One of the most profound and maybe non-obvious shifts driving this is the emergence of the cloud database. Services such as Amazon S3, Google BigQuery, Snowflake, Databricks, and others, have solved computing on large volumes of data, and have made it easy to store data from every available source. Enterprise wants to store everything they can in the hopes of being able to deliver improved customer experiences and new market capabilities

推动这一转变最深刻但可能并不明显的原因，是云数据库的出现。类似 Amazon S3、Google BigQuery、Snowflake、Databricks 这样的的服务，已经解决了计算大量数据的困难，并且让存储各种来源的数据变得更加简单。企业希望能存储一切他们可以存储的数据，以期提供优化用户体验和拓展新市场的能力。

---

### 现在是成为一家数据库公司的好时机

**It has been a good time to be a database company.**

Database companies have raised over $8.7B over the last 10 years, with almost half of that, $4.1B, just in the last 24 months, up from $849M in 2019 (according to CB Insights).

数据库公司在过去 10 年间筹集了超过 87 亿美元资金，其中几乎一半的资金，也就是 41 亿美元是在过去 24 个月中筹集的（原文发布于 2022 年 6 月 8 号）而 2019 年就有 8.49 亿（数据来自 [CB Insights](https://www.cbinsights.com/)）

It’s not surprising with the sky-high valuations of Snowflake and Databricks and $16B in new revenue up for grabs in 2021, simply from market growth. A market that doubled in the last four years to almost 💲90B, is expected to double again over the next four. Safe to say there is a huge opportunity to go after.

Snowflake 和 Databricks 的天价估值和 2021 年 160 亿美元的新兴市场并不奇怪，这还仅仅是来自于市场增长。过去四年中，市场容量翻了一番，达到了 900 亿美元，而且完全可以期待未来四年再翻一番。可以放心的说，未来的仍然有巨大的机会可以追求。

[See here for a solid list of database financings in 2021.](https://adat.blog/2022/02/fundraising-by-data-companies-in-2021/)

[有关 2021 年数据库公司融资的数据清单，请查看此处](https://adat.blog/2022/02/fundraising-by-data-companies-in-2021/)

![data growth driving new spend](https://miro.medium.com/max/1400/0*I_uCl1Q70PbntCWx)

---

### 20 年前，你只有一种选择，关系型数据库

**20 years ago, you had one option, a relational database.**

Today, thanks to the cloud, microservices, distributed applications, global scale, real-time data, deep learning, etc., new database architectures emerged to hyper-solve new performance requirements. Different systems for fast reads, and fast writes. Systems specifically to power ad-hoc analytics, or for data that is unstructured, semi-structured, transactional, relational, graph, or time-series. Also for data used for cache, search, based on indexes, events, and more.

而今天，得益于云、微服务、分布式应用、全球拓展、实时数据、深度学习等一些列新兴技术，出现了新的数据库架构来解决新的性能需求。有不同的系统可以支持快速写入和快读读取取，有专门用于支持即席数据分析的系统，或者用于非结构化数据、半结构化数据、事务数据、关系数据、图数据或者时间序列数据的系统，还有基于索引、事件或者更多方式处理缓存、搜索的数据。

Each came with different performance needs, including high availability, horizontal scale, distributed consistency, failover protection, partition tolerance, serverless, and fully managed.

每一种都有各自不同的性能需求，包括高可用、水平拓展、分布式一致性、故障转移保护、分区容差、无服务器计算以及全面托管。

As a result, enterprises on average store data across seven or more different databases (i.e., Snowflake as your data warehouse, Clickhouse for ad-hoc analytics, Timescale for time series data, Elastic for their search data, S3 for logs, Postgres for transactions, Redis for caching or application data, Cassandra for complex workloads, and Dgraph for relationship data or dynamic schemas). That’s all assuming you are collocated to a single cloud, and that you’ve built a modern data stack from scratch.

因此，企业平均在超过 7 种不同的数据库中存储数据（比如，Snowflake 用作数据仓库，Clickhouse 用作即席分析，Timescale 用作时间序列，Elastic 用作全文检索，S3 用作日志，Postgres 用作事务，Redis 用作缓存或应用数据，Cassandra 用作复杂的工作负载，Dgraph 用作关系数据或者动态架构）这一切都假设你配置了一个单一的云，并且从头构建了一个现代化的数据工作站。

The level of performance and guarantees from these services and platforms are unparalleled, compared to 5–10 years ago. At the same time, the proliferation and fragmentation of the database layer are increasingly creating new challenges. For example, syncing across the different schemas and systems, writing new ETL jobs to bridge workloads across multiple databases, constant cross-talk and connectivity issues, the overhead of managing active-active clustering across so many different systems, or data transfers when new clusters or systems come online. Each with different scaling, branching, propagation, sharding, and resource requirements.

与 5-10 年前相比，这些服务和平台提供的性能和可靠性是无与伦比的。但同时，与日俱增的数据库层的碎片化扩散问题也在创造新的挑战。例如，在不同模式的数据库之间同步数据，编写新的 ETL 任务以桥接多个数据库，持续的连接和干扰问题，维护如此之多的集群系统带来的额外性能开销，或者是新集群上线时的数据传输。每一个问题都有不同的拓展、分支、传播、分片和资源要求。

What’s more, new databases emerge monthly to solve the next challenge of enterprise scale.

更重要的是，每个月都会有新的数据库出现，以解决企业拓展遇到的下一个挑战。
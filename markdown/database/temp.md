1、数据库的总类

> 根据存储分类

- 关系型数据库 [System R , Oracle , DB2 , SQL Server , PostgreSQL , MySQL] : 读多写少
- NoSQL数据库：牺牲复杂SQL的支持及ACID事务功能,换取弹性扩展能力和更高的读写性能.
  - 文档数据库 [Document-based Database] - [MongoDB]
  - 键值数据库 [Key-Value Database] - [Redis]
  - 图数据库 [Graph-based Database] - [Neo4j]
  - 时序数据库 [Time Series Database] - [InfluxDB]
  - 宽列式存储数据库 [Wide Column-based Store] - [Hbase]
  - 多模数据库 [Multi-Model Database] - []
- NewSQL数据库 [TiDB , OceanBase , CockroachDB]：保持事务一致，同时具备NoSQL数据库的扩展性及访问性能。

[//]: # (![picture]&#40;https://cms-danger-sequel.oss-cn-zhangjiakou.aliyuncs.com/article/4/4958/4958627.png_e6ebc2d45ba1827a2299ce9deef4825c&#41;)

> 根据数据处理场景类

- OLTP数据库：<br> 联机事务处理，高并发，低延迟，高吞吐。
  - MySQL、Oracle、Redis、MongoDB
- OLAP数据库：<br> 联机分析处理，专注决策分析，支持高度可扩展，以及复杂的分析操作。<br> 侧重数据任务的离线处理，不实时提供服务。<br> 绝大部分的OLAP数据库是采用LSM树构建存储引擎。
  - HBase、Teradata、Hive、Presto、Druid、ClickHouse
- HTAP数据库：OLTP和OLAP两种数据库类型的合体。
  - TiDB、OceanBase、CockroachDB

<img src="https://cms-danger-sequel.oss-cn-zhangjiakou.aliyuncs.com/article/4/4958/4958626.png_e6ebc2d45ba1827a2299ce9deef4825c">
































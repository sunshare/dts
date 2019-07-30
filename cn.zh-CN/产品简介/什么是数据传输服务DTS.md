# 什么是数据传输服务DTS {#concept_26592_zh .concept}

数据传输服务DTS（Data Transmission Service）支持RDBMS、NoSQL、OLAP等数据源间的数据交互，集数据迁移/订阅/同步于一体，助您构建安全、可扩展、高可用的数据架构。

## DTS视频简介 {#section_cxc_wvj_wgb .section}

  

## 为什么选择DTS {#section_ufv_l0y_aqs .section}

相对于传统数据迁移/同步工具，DTS为您提供功能更丰富、传输性能更强、易用性更高且安全可靠的服务，帮助您简化复杂的数据交互工作，专注于上层的业务开发。

-   DTS承诺**99.95%**的链路稳定性及**99.999%**的数据可靠性。
-   数据传输支持同/异构数据源之间的数据交互，提供数据迁移/订阅/同步交互功能。
-   支持节点的故障容灾，可实现链路的秒级恢复。
-   支持断点续传，可有效解决因硬件、网络等异常导致的传输中断。
-   DTS支持RAM主子账号体系，用户可以使用子账号创建并管理DTS实例，提高企业安全性。详情请参见[使用子账号访问DTS](https://help.aliyun.com/document_detail/47568.html)。

更多详情请参见[产品优势](cn.zh-CN/产品简介/产品优势.md#)和[使用场景](https://help.aliyun.com/document_detail/26599.html)。

## DTS支持的数据源 {#section_97w_ild_lgl .section}

DTS支持多种数据源类型，例如：

-   关系型数据库：Oracle、MySQL、SQLServer、DB2、PostgreSQL 、RDS For PPAS、DRDS、PetaData、OceanBase。
-   非关系型数据库（NoSQL）：MongoDB、Redis 。
-   OLAP：ODPS、ADS、流计算、Datahub。

## 学习路径 {#section_fkv_l1r_mem .section}

您可以通过[DTS学习路径](https://help.aliyun.com/product/26590.html)，由浅入深地了解DTS的相关概念、基础操作、进阶操作等。

## 产品定价 {#section_zr8_9tb_2iu .section}

详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。

## 功能简介 {#section_euz_1lj_lnr .section}

|功能|介绍|相关文档|
|--|--|----|
|数据迁移|帮助您实现同/异构数据源之间的数据迁移，适用于数据上云迁移、阿里云内部跨实例数据迁移、数据库拆分扩容等业务场景。| -   [数据迁移功能特性](cn.zh-CN/产品简介/功能特性/数据迁移.md#)
-   [DTS数据迁移方案概览](../../../../cn.zh-CN/用户指南/数据迁移/DTS数据迁移方案概览.md#)

 |
|数据同步|帮助您实现数据源之间的数据实时同步，适用于数据异地多活、数据异地灾备、本地数据灾备、跨境数据同步、查询与报表分流、云BI及实时数据仓库等多种业务场景。| [数据同步功能特性](cn.zh-CN/产品简介/功能特性/数据同步.md#)

 |
|数据订阅|帮助您获取MySQL、DRDS、Oracle的实时增量数据，您可以根据业务需求自由消费增量数据，适用于缓存更新策略、业务异步解耦、异构数据源的数据实时同步和复杂ETL的数据实时同步等多种业务场景。| -   [数据订阅功能特性](cn.zh-CN/产品简介/功能特性/数据订阅.md#)
-   [数据订阅功能特性（新）](cn.zh-CN/产品简介/功能特性/数据订阅（新）.md#)

 |

## 如何使用DTS {#section_fsc_3gr_rod .section}

您可以通过以下方式管理DTS实例，进行实例创建、任务配置、实例运维等操作：

-   [控制台](https://dts.console.aliyun.com/)：提供图形化的Web界面，操作方便。
-   [CLI](https://help.aliyun.com/product/29991.html)：控制台上所有的操作都可以通过CLI实现。
-   [API](https://help.aliyun.com/document_detail/49456.html)：控制台上所有的操作都可以通过API实现。
-   [SDK](https://help.aliyun.com/document_detail/57690.html)：控制台上所有的操作都可以通过SDK实现。

## 相关服务 {#section_wm9_yh6_mce .section}

-   [ECS](https://help.aliyun.com/document_detail/25367.html)：云服务器ECS（Elastic Compute Service）上的自建数据库作为DTS数据迁移/同步/订阅的数据源。
-   [RDS](https://help.aliyun.com/document_detail/26092.html)：阿里云关系型数据库RDS（Relational Database Service）是一种稳定可靠、可弹性伸缩的在线数据库服务，提供容灾、备份、恢复、迁移等方面的全套解决方案，彻底解决数据库运维的烦恼。
-   [POLARDB](https://help.aliyun.com/document_detail/58764.html)：POLARDB是阿里云自研的下一代关系型云数据库，兼容MySQL、PostgreSQL、Oracle引擎，存储容量最高可达100TB，单库最多可扩展到16个节点，适用于企业多样化的数据库应用场景。
-   [DRDS](https://help.aliyun.com/document_detail/117729.html)：分布式关系型数据库服务DRDS（Distributed Relational Database Service）是阿里巴巴致力于解决单机数据库服务瓶颈问题而自主研发推出的分布式数据库产品。DRDS高度兼容 MySQL 协议和语法，支持自动化水平拆分、在线平滑扩缩容、弹性扩展、透明读写分离，具备数据库全生命周期运维管控能力。
-   [云数据库Redis版](https://help.aliyun.com/document_detail/26342.html)：阿里云数据库Redis版（ApsaraDB for Redis）是兼容开源Redis协议的高性能Key-Value类型在线存储服务。
-   [云数据库MongoDB版](https://help.aliyun.com/document_detail/26558.html)：云数据库MongoDB版（ApsaraDB for MongoDB）是基于飞天分布式系统和高可靠存储引擎的在线数据库服务，可提供多节点副本集高可用架构、弹性扩容、容灾、备份回滚、性能优化等解决方案。


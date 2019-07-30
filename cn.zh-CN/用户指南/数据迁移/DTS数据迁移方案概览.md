# DTS数据迁移方案概览 {#concept_1130256 .concept}

DTS的数据迁移功能支持同构或异构数据源之间的数据迁移，同时提供了库表列三级映射、数据过滤等多种ETL特性，适用于数据迁移上云、阿里云实例间迁移、数据迁移下云等多种场景。

|迁移场景|源库类型|文档链接|
|:---|:---|:---|
|从自建数据库迁移至阿里云|MySQL|[从自建MySQL迁移至RDS for MySQL](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建MySQL迁移至RDS for MySQL.md#)|
|[从自建MySQL迁移至POLARDB](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/将ECS上的自建MySQL迁移至POLARDB实例.md#)|
|[从自建MySQL迁移至DRDS](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建MySQL迁移至DRDS.md#)|
|SQL Server|[从自建SQL Server增量迁移至RDS for SQL Server](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建SQL Server增量迁移至RDS for SQL Server.md#)|
|[从自建SQL Server全量迁移至RDS for SQL Server](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建SQL Server全量迁移至RDS for SQL Server.md#)|
|Oracle|[从自建Oracle迁移至RDS for MySQL实例](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建Oracle迁移至RDS for MySQL实例.md#)|
|[从自建Oracle迁移至DRDS](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建Oracle迁移至DRDS.md#)|
|[从自建Oracle迁移至RDS for PPAS](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建Oracle迁移至RDS for PPAS.md#)|
|PostgreSQL|[从自建PostgreSQL增量迁移至RDS for PostgreSQL](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建PostgreSQL增量迁移至RDS for PostgreSQL.md#)|
|[从自建PostgreSQL全量迁移至RDS for PostgreSQL](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建PostgreSQL全量迁移至RDS for PostgreSQL.md#)|
|Redis|[从自建Redis迁移至阿里云Redis](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建Redis迁移至阿里云Redis.md#)|
|MongoDB|[从单节点架构的自建MongoDB迁移至阿里云](https://help.aliyun.com/document_detail/125257.html)|
|[从副本集架构的自建MongoDB迁移至阿里云](https://help.aliyun.com/document_detail/125258.html)|
|[从分片集群架构的自建MongoDB迁移至阿里云](https://help.aliyun.com/document_detail/125259.html)|
|DB2|[从自建DB2迁移至RDS for MySQL](cn.zh-CN/.md#)|
|从第三方云迁移至阿里云|Amazon RDS|[从Amazon RDS for MySQL至阿里云](cn.zh-CN/用户指南/数据迁移/从第三方云迁移至阿里云/从Amazon RDS for MySQL至阿里云.md#)|
|[从Amazon RDS for Oracle迁移至阿里云RDS for MySQL](cn.zh-CN/用户指南/数据迁移/从第三方云迁移至阿里云/从Amazon RDS for Oracle迁移至阿里云RDS for MySQL.md#)|
|[从Amazon RDS for Oracle迁移至阿里云RDS for PPAS](cn.zh-CN/用户指南/数据迁移/从第三方云迁移至阿里云/从Amazon RDS for Oracle迁移至阿里云RDS for PPAS.md#)|
|[从Amazon RDS for PostgreSQL迁移至阿里云](cn.zh-CN/用户指南/数据迁移/从第三方云迁移至阿里云/从Amazon RDS for PostgreSQL迁移至阿里云.md#)|
|Amazon Aurora|[从Amazon Aurora for MySQL迁移至阿里云RDS for MySQL](cn.zh-CN/用户指南/数据迁移/从第三方云迁移至阿里云/从Amazon Aurora for MySQL迁移至阿里云RDS for MySQL.md#)|
|[从Amazon Aurora for MySQL迁移至POLARDB for MySQL](https://help.aliyun.com/document_detail/124691.html)|
|[从Amazon Aurora for PostgreSQL迁移至阿里云](cn.zh-CN/用户指南/数据迁移/从第三方云迁移至阿里云/从Amazon Aurora for PostgreSQL迁移至阿里云.md#)|
|Amazon SQL Server|[从Amazon SQL Server迁移到阿里云](https://help.aliyun.com/document_detail/124678.html)|
|华为云文档数据库|[从华为云文档数据库迁移至阿里云](https://help.aliyun.com/document_detail/127837.html)|
|腾讯云MySQL|[从腾讯云MySQL迁移至阿里云](cn.zh-CN/用户指南/数据迁移/从第三方云迁移至阿里云/从腾讯云MySQL迁移至阿里云.md#)|
|腾讯云MongoDB|[从腾讯云MongoDB增量迁移至阿里云](https://help.aliyun.com/document_detail/127839.html)|
|[从腾讯云MongoDB全量迁移至阿里云](https://help.aliyun.com/document_detail/127840.html)|
|阿里云实例间迁移|RDS实例|[RDS实例间的数据迁移](cn.zh-CN/用户指南/数据迁移/阿里云实例间迁移/RDS实例间的数据迁移.md#)|
|RDS for MySQL实例|[从RDS for MySQL迁移至POLARDB for MySQL](cn.zh-CN/用户指南/数据迁移/阿里云实例间迁移/从RDS for MySQL迁移至POLARDB for MySQL.md#)|
|MongoDB实例|[从MongoDB单节点实例迁移至副本集或分片集群实例](https://help.aliyun.com/document_detail/127832.html)|
|[从MongoDB副本集实例迁移至分片集群实例](https://help.aliyun.com/document_detail/113516.html)|
|[迁移MongoDB实例至其他地域](https://help.aliyun.com/document_detail/127833.html)|
|[跨阿里云账号迁移MongoDB实例](https://help.aliyun.com/document_detail/127834.html)|
|从阿里云迁移至自建数据库|RDS for SQL Server|[从RDS for SQL Server增量迁移至自建SQL Server](cn.zh-CN/用户指南/数据迁移/从阿里云迁移至自建数据库/从RDS for SQL Server增量迁移至自建SQL Server.md#)|


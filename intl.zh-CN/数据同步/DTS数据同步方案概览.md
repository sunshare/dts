# DTS数据同步方案概览

DTS的数据同步功能支持同构或异构数据源之间的数据同步，同时提供了库表列三级映射、数据过滤等多种ETL特性，适用于数据异地多活、数据本地/异地灾备、跨境数据同步、查询与报表分流、云BI及实时数据仓库等多种业务场景。本文汇总了各类场景下的数据同步方案。

## 数据同步方案

关于数据同步对数据库的支持情况请参见[支持的数据库、同步初始化类型和同步拓扑](/intl.zh-CN/数据同步/支持的数据库、同步初始化类型和同步拓扑.md)。

|同步场景|文档链接|
|----|----|
|MySQL间数据同步|[RDS MySQL实例间的双向同步](/intl.zh-CN/数据同步/MySQL间数据同步/RDS MySQL实例间的双向同步.md)|
|[RDS MySQL实例间的单向同步](/intl.zh-CN/数据同步/MySQL间数据同步/RDS MySQL实例间的单向同步.md)|
|[从ECS上的自建MySQL同步至RDS MySQL](/intl.zh-CN/数据同步/MySQL间数据同步/从ECS上的自建MySQL同步至RDS MySQL.md)|
|[从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至RDS MySQL](/intl.zh-CN/数据同步/MySQL间数据同步/从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至RDS MySQL.md)|
|[从RDS MySQL同步至通过专线、VPN网关或智能接入网关接入的自建MySQL](/intl.zh-CN/数据同步/MySQL间数据同步/从RDS MySQL同步至通过专线、VPN网关或智能接入网关接入的自建MySQL.md)|
|[不同阿里云账号下RDS MySQL实例间的数据同步](/intl.zh-CN/数据同步/MySQL间数据同步/不同阿里云账号下RDS MySQL实例间的数据同步.md)|
|MySQL同步至其他数据库|[从RDS MySQL同步至PolarDB MySQL](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从RDS MySQL同步至PolarDB MySQL.md)|
|[从RDS MySQL同步到AnalyticDB for MySQL](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从RDS MySQL同步到AnalyticDB for MySQL.md)|
|[从RDS MySQL同步至云原生数据仓库AnalyticDB PostgreSQL](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从RDS MySQL同步至云原生数据仓库AnalyticDB PostgreSQL.md)|
|[从ECS上的自建MySQL同步至云原生数据仓库AnalyticDB PostgreSQL](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从ECS上的自建MySQL同步至云原生数据仓库AnalyticDB PostgreSQL.md)|
|[从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至云原生数据仓库AnalyticDB PostgreSQL](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至云原生数据仓库AnalyticDB PostgreSQL.md)|
|[从ECS上的自建MySQL同步至Elasticsearch](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从ECS上的自建MySQL同步至Elasticsearch.md)|
|[从RDS MySQL同步至MaxCompute](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从RDS MySQL同步至MaxCompute.md)|
|[从自建MySQL同步至阿里云消息队列Kafka版](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从自建MySQL同步至阿里云消息队列Kafka版.md)|
|[从RDS MySQL同步至自建Kafka集群](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从RDS MySQL同步至自建Kafka集群.md)|
|[从ECS上的自建MySQL同步至自建Kafka集群](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从ECS上的自建MySQL同步至自建Kafka集群.md)|
|[从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至自建Kafka集群](/intl.zh-CN/数据同步/MySQL同步至其他数据库/从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至自建Kafka集群.md)|
|PolarDB数据同步|[PolarDB MySQL集群间的双向同步](/intl.zh-CN/数据同步/PolarDB数据同步/PolarDB MySQL集群间的双向同步.md)|
|[PolarDB MySQL集群间的单向同步](/intl.zh-CN/数据同步/PolarDB数据同步/PolarDB MySQL集群间的单向同步.md)|
|[PolarDB-O集群间的单向同步](/intl.zh-CN/数据同步/PolarDB数据同步/PolarDB-O集群间的单向同步.md)|
|[从PolarDB MySQL同步至RDS MySQL](/intl.zh-CN/数据同步/PolarDB数据同步/从PolarDB MySQL同步至RDS MySQL.md)|
|[从PolarDB MySQL同步至Elasticsearch](/intl.zh-CN/数据同步/PolarDB数据同步/从PolarDB MySQL同步至Elasticsearch.md)|
|[从PolarDB MySQL同步到Kafka](/intl.zh-CN/数据同步/PolarDB数据同步/从PolarDB MySQL同步到Kafka.md)|
|[从PolarDB MySQL同步至AnalyticDB for MySQL](/intl.zh-CN/数据同步/PolarDB数据同步/从PolarDB MySQL同步至AnalyticDB for MySQL.md)|
|[从PolarDB MySQL同步至云原生数据仓库AnalyticDB PostgreSQL](/intl.zh-CN/数据同步/PolarDB数据同步/从PolarDB MySQL同步至云原生数据仓库AnalyticDB PostgreSQL.md)|
|[从ECS上的自建MySQL同步至PolarDB MySQL](/intl.zh-CN/数据同步/PolarDB数据同步/从ECS上的自建MySQL同步至PolarDB MySQL.md)|
|[从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至PolarDB MySQL](/intl.zh-CN/数据同步/PolarDB数据同步/从通过专线、VPN网关或智能接入网关接入的自建MySQL同步至PolarDB MySQL.md)|
|DRDS数据同步|[DRDS实例间的数据实时同步](/intl.zh-CN/数据同步/DRDS数据同步/DRDS实例间的数据实时同步.md)|
|[从DRDS同步至AnalyticDB for MySQL](/intl.zh-CN/数据同步/DRDS数据同步/从DRDS同步至AnalyticDB for MySQL.md)|
|[从DRDS同步至AnalyticDB for PostgreSQL](/intl.zh-CN/数据同步/DRDS数据同步/从DRDS同步至AnalyticDB for PostgreSQL.md)|
|Redis数据同步|[Redis实例间的单向数据同步](/intl.zh-CN/数据同步/Redis数据同步/Redis实例间的单向数据同步.md)|
|[跨云账号同步Redis集群版实例](/intl.zh-CN/数据同步/Redis数据同步/跨云账号同步Redis集群版实例.md)|
|[Redis企业版实例间的双向同步](/intl.zh-CN/数据同步/Redis数据同步/Redis企业版实例间的双向同步.md)|
|[从ECS上的自建Redis同步至Redis实例](/intl.zh-CN/数据同步/Redis数据同步/从ECS上的自建Redis同步至Redis实例.md)|
|[从通过专线、VPN网关或智能接入网关接入的自建Redis同步至ECS上的自建Redis](/intl.zh-CN/数据同步/Redis数据同步/从通过专线、VPN网关或智能接入网关接入的自建Redis同步至ECS上的自建Redis.md)|
|[从自建Redis集群同步至Redis集群实例](/intl.zh-CN/数据同步/Redis数据同步/从自建Redis集群同步至Redis集群实例.md)|
|[从ECS上的Codis集群同步至Redis实例](/intl.zh-CN/数据同步/Redis数据同步/从ECS上的Codis集群同步至Redis实例.md)|
|[从ECS上的Twemproxy Redis集群同步至Redis实例](/intl.zh-CN/数据同步/Redis数据同步/从ECS上的Twemproxy Redis集群同步至Redis实例.md)|
|PostgreSQL数据同步|[从RDS PostgreSQL同步至云原生数据仓库AnalyticDB PostgreSQL](/intl.zh-CN/数据同步/PostgreSQL数据同步/从RDS PostgreSQL同步至云原生数据仓库AnalyticDB PostgreSQL.md)|
|[自建PostgreSQL同步到云原生数据仓库AnalyticDB PostgreSQL](/intl.zh-CN/数据同步/PostgreSQL数据同步/自建PostgreSQL同步到云原生数据仓库AnalyticDB PostgreSQL.md)|


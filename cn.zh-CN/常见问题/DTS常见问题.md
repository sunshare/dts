# DTS常见问题 {#concept_1957988 .concept}

本文为您列出数据传输服务DTS（Data Transmission Service）的常见问题和相关解答。

## 数据迁移/同步/订阅的工作原理是什么 {#section_ygj_70v_7mu .section}

详情请参见[产品架构及功能原理](../../../../cn.zh-CN/产品简介/产品架构及功能原理.md#)。

## DTS支持哪些数据库的迁移/同步/订阅 {#section_n4q_38f_qxm .section}

DTS支持RDBMS、NoSQL、OLAP等数据源间的数据交互，详情请参见[迁移/同步/订阅支持的数据库](../../../../cn.zh-CN/产品简介/迁移__同步__订阅支持的数据库.md#)。

**说明：** DTS同时支持将第三方云厂商的数据库迁移/同步至阿里云，相关案例请参见[DTS数据迁移方案概览](../../../../cn.zh-CN/用户指南/数据迁移/DTS数据迁移方案概览.md#)。

## 数据迁移和数据同步的区别是什么 {#section_8yo_tqz_gwl .section}

|对比项|区别|
|:--|:-|
|场景| 数据迁移主要用于上云迁移，例如将本地数据库/ECS上的自建数据库/第三方云数据库迁移至阿里云数据库。它属于一次性任务，迁移完成后即可释放实例。

 数据同步主要用于两个数据源之间的数据实时同步，例如应用于异地多活、数据灾备、跨境数据同步、查询与报表分流、云BI及实时数据仓库等场景。它属于持续性任务，任务创建后会一直同步，保持数据源和数据目标的数据一致性。

 |
|功能|数据同步功能支持在线新增/移除同步对象，支持RDS for MySQL双向同步，数据迁移则不支持。|
|收费| 数据迁移的付费类型固定为按量付费，且结构迁移和全量迁移不收费，仅增量迁移需收费。

 数据同步的付费类型支持按量付费和包年包月，且结构初始化和全量初始化均需收费。

 |

## 是否支持跨云账号的数据迁移/同步 {#section_3qu_2jx_itv .section}

-   数据迁移：可直接支持RDS for MySQL的跨账号迁移，详情请参见[使用DTS跨阿里云账号迁移RDS数据](../../../../cn.zh-CN/最佳实践/使用DTS跨阿里云账号迁移RDS数据.md#)。其他类型的数据库实例（例如POLARDB for MySQL、DRDS、Redis、MongoDB），可将其作为**有公网IP的自建数据库**进行跨云账号迁移。
-   数据同步：当前仅支持RDS for MySQL的跨账号同步，详情请参见[不同阿里云账号下的RDS for MySQL实例配置数据同步](../../../../cn.zh-CN/用户指南/实时同步/MySQL实时同步至MySQL/不同阿里云账号下的RDS for MySQL实例配置数据同步.md#)。

## 不同的链路规格有什么区别 {#section_0xy_32u_rg2 .section}

详情请参见[数据迁移链路规格说明](../../../../cn.zh-CN/产品简介/规格说明/数据迁移链路规格说明.md#)和[数据同步规格说明](../../../../cn.zh-CN/产品简介/规格说明/数据同步规格说明.md#)。

## 链路规格是否支持降级 {#section_tmj_cm6_9jm .section}

暂不支持。

## DTS如何收费 {#section_stl_285_48e .section}

详情请参见[DTS产品定价](https://www.aliyun.com/price/product#/dts/detail)。

## 为什么数据同步的价格普遍高于比数据迁移 {#section_k32_hxn_llm .section}

数据同步具有更多的高级特性，例如在线调整同步对象、MySQL双向同步，且数据同步基于内网传输，可以保证更低的网络延时。

## 如何解决DTS无法连接数据库的问题 {#section_np4_7e2_k21 .section}

详情请参见[源库连接性检查](../../../../cn.zh-CN/用户指南/数据迁移/预检查及修复方法/源库连接性检查.md#)和[目标数据库连接性检查](../../../../cn.zh-CN/用户指南/数据迁移/预检查及修复方法/目标数据库连接性检查.md#)。

## 是否支持迁移同一个实例内的不同数据库 {#section_xsb_ru0_zcj .section}

支持，相关案例请参见[实例内不同数据库之间的数据迁移](https://help.aliyun.com/document_detail/26651.html)。

## 是否支持DML/DDL操作的同步 {#section_icj_d5d_pvf .section}

支持，关系数据库之间的数据迁移/同步，支持的DML操作为INSERT、UPDATE、DELETE，支持的DDL操作为CREATE、DROP、ALTER、RENAME、TRUNCATE。

**说明：** 不同场景下支持的DML或DDL操作有所区别，例如从MySQL同步到Analytic DB for MySQL（2.0）时，DDL仅支持CREATE TABLE、ALTER TABLE、DROP TABLE，DML仅支持INSERT、UPDATE、DELETE，详情请参见具体的数据迁移/同步的场景文档。

## DTS是否支持分库分表的数据迁移/同步 {#section_ipd_ru6_sbz .section}

支持，例如将MySQL、POLARDB for MySQL中的分库分表迁移/同步到AnalyticDB for MySQL中，以实现多表归并。

## DTS是否支持跨时区/字符集的数据迁移/同步 {#section_zk8_h10_gye .section}

支持。

## 是否支持更改数据迁移/同步的对象在目标库中的名称 {#section_8ud_rx7_tlh .section}

支持，DTS支持库名、表名、列名的名称映射，详情请参见[库表列映射](../../../../cn.zh-CN/用户指南/数据迁移/库表列映射.md#)或[设置同步对象在目标实例中的名称](../../../../cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)。

## 是否支持过滤部分字段或数据 {#section_79e_b54_nr9 .section}

支持，DTS支持过滤数据表的部分字段或数据，详情请参见[通过SQL条件过滤待迁移数据](../../../../cn.zh-CN/用户指南/数据迁移/通过SQL条件过滤待迁移数据.md#)或[通过SQL条件过滤待同步数据](../../../../cn.zh-CN/用户指南/实时同步/通过SQL条件过滤待同步数据.md#)。

## 数据同步是否支持新增/移除同步对象 {#section_7s5_yc6_27v .section}

支持，详情请参见[新增同步对象](../../../../cn.zh-CN/用户指南/实时同步/新增同步对象.md#)或[移除同步对象](../../../../cn.zh-CN/用户指南/实时同步/移除同步对象.md#)。

## 如何查看数据迁移/同步的性能信息 {#section_1kk_pik_p30 .section}

详情请参见[查看增量迁移性能](../../../../cn.zh-CN/.md#)或[查看同步性能](../../../../cn.zh-CN/用户指南/实时同步/查看同步性能.md#)。

## 同步延迟的计算规则是什么 {#section_ag8_doq_sq7 .section}

数据写入目标库的时间戳 - 日志读取的时间戳，即可得到同步延迟信息。

## 如何配置延迟告警及阀值 {#section_cj7_ul6_r8w .section}

详情请参见[配置监控报警](../../../../cn.zh-CN/用户指南/实时同步/配置监控报警.md#)。

## 如何消费订阅的数据 {#section_p2d_kbg_127 .section}

-   旧版数据订阅：通过SDK实现订阅数据的消费，详情请参见[使用SDK消费订阅数据](https://help.aliyun.com/document_detail/26647.html)。

    **说明：** 当前SDK仅支持Java语言，SDK版本信息请参见[下载SDK](https://help.aliyun.com/document_detail/26648.html)。

-   新版数据订阅：通过kafka client实现订阅数据的消费，详情请参见[使用Kafka客户端消费订阅数据](../../../../cn.zh-CN/用户指南/数据订阅（新）/使用Kafka客户端消费订阅数据.md#)。


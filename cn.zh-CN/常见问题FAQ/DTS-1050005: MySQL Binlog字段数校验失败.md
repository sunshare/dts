# DTS-1050005: MySQL Binlog字段数校验失败 {#concept_yxp_4yg_t2b .concept}

因DTS本地存储的表的表结构跟Binlog中这个表的表结构不一致, 导致DTS 解析 MySQL Binlog日志异常

## DTS-1050005 MySQL table xx.xx binlog column count check error, local count is 21, but binlog count is 22, at offset 9198@111547. {#section_uhc_qrh_t2b .section}

**DTS报错信息**

DTS-1050005 MySQL table xx.xx binlog column count check error, local count is 21, but binlog count is 22, at offset 9198@111547.

其中, DTS 报错code为: DTS-1050005。

DTS的报错语句格式为: MySQL table xx.xx binlog column count check error, local count is 21, but binlog count is 22, at offset 9198@111547.。其中, xx.xx是对应的库名和表名, 21 是这个表在DTS本地维护的表结构的字段个数, 22是 这个表在Binlog中对应的字段个数; 9198@111547 是这个语句在Binlog中的位置。

**失败原因**

Binlog中待与解析的日志对应的表的表结构跟DTS本地存储的这个表的表结构不一致,导致Binlog解析失败。出现表结构跟DTS本地存储表结构不一致的原因主要有几个:

-   这个表之前进行的表结构变更DDL语句, DTS解析失败, 被忽略跳过, 未更新DTS本地存储的表结构
-   全量数据迁移或初始化过程中, 源库业务进行过表结构变更

**解决方案**

需要提交工单, 联系DTS技术人员重新加载DTS本地存储的表结构。


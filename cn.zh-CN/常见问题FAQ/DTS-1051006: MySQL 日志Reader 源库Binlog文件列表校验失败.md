# DTS-1051006: MySQL 日志Reader 源库Binlog文件列表校验失败 {#concept_uh2_kkf_2fb .concept}

DTS MySQL日志Reader日志内容校验失败

## DTS-1051006 MySQL Server xx.xx.xx.xx:xx, Table db\_name.table\_name is not exists. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1051006 MySQL Server xx.xx.xx.xx:xx, Table db\_name.table\_name is not exists.

其中, DTS 报错code为: DTS-1051006

DTS的报错语句格式为: DTS-1051006 MySQL Server xx.xx.xx.xx:xx, Table db\_name.table\_name is not exists. 其中:xx.xx.xx.xx:xx为对应的MySQL连接信息, db\_name.table\_name 为对应的表名

**失败原因**

1. DTS MySQL日志Reader查询源库db\_name.table\_name表结构不存在,可能导致的原因是近期该表上有DDL变更

**解决方案**

1. 提交工单并提供db\_name.table\_name 建表语句和最近执行的相关DDL, 联系DTS值班db\_name.table\_name处理


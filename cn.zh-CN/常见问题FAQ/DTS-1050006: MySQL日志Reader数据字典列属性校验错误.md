# DTS-1050006: MySQL日志Reader数据字典列属性校验错误 {#concept_d2t_gkf_2fb .concept}

DTS MySQL日志Reader数据字典列属性校验错误

## DTS-1050005 MySQL local meta column db\_name.table\_name.column\_name attribute type, local<\>record\(bigint<\>varchar\). {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1050005 MySQL local meta column db\_name.table\_name.column\_name attribute type, local<\>record\(local\_type<\>event\_type\).

其中, DTS 报错code为: DTS-1050006

DTS的报错语句格式为: DTS-1050006 MySQL local meta column db\_name.table\_name.column\_name attribute type, local<\>record\(bigint<\>varchar\). 其中,db\_name.table\_name.column\_name是对应出错表列全名称, 是local\_type是DTS数据字典列类型,event\_type是binlog 中对应数据类型.

**失败原因**

1. DTS DDL解析失败导致的数据字典更新错误

2. DTS任务创建过程中源库发生了对应DDL

3. DDL 操作的表不在迁移对象内, 发生了rename 同步对象外的表到同步对象内

**解决方案**

1. 提交工单说明最近执行的DDL,联系DTS值班协助解决.

2. 建议给DTS的源库帐号有整个information\_schema的查询权限.


# DTS-1050008: MySQL日志Reader数据字典表信息不存在 {#concept_bdj_3kf_2fb .concept}

DTS MySQL日志Reader数据字典表信息不存在

## DTS-1050008 MySQL local table db\_name.table\_name meta not found. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1050008 MySQL local table db\_name.table\_name meta not found

其中, DTS 报错code为: DTS-1050008

DTS的报错语句格式为: DTS-1050008 MySQL local table db\_name.table\_name meta not found. 其中:db\_name.table\_name 是对应表信息不存在的表名称

**失败原因**

1. DTS DDL解析失败导致的数据字典更新错误

2. DTS任务创建过程中源库发生了对应DDL

**解决方案**

1. 提交工单说明最近执行的DDL,联系DTS值班协助解决.

2. 建议给DTS的源库帐号有整个information\_schema的查询权限.


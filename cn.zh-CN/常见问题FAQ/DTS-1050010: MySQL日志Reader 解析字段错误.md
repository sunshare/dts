# DTS-1050010: MySQL日志Reader 解析字段错误 {#concept_uh2_kkf_2fb .concept}

DTS MySQL日志Reader解析字段错误

## DTS-1050010 column db\_name.table\_name.column\_name binlog parse error, at offset filename@offset. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1050010 column db\_name.table\_name.column\_name binlog parse error, at offset filename@offset..

其中, DTS 报错code为: DTS-1050010

DTS的报错语句格式为: DTS-1050010 column db\_name.table\_name.column\_name binlog parse error, at offset filename@offset. 其中:解析失败的列名是db\_name.table\_name.column\_name, Binlog event对应的.物理位置是filename@offset

**失败原因**

1. DTS DDL解析失败导致的数据字典更新错误

2. DTS任务创建过程中源库发生了对应DDL,导致DTS查询的schema信息不对

3. DTS MySQL日志Reader解析event存在BUG

**解决方案**

1. 提交工单说明最近执行的DDL,联系DTS值班协助解决.

2. 建议给DTS的源库帐号有整个information\_schema的查询权限.


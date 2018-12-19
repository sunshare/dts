# DTS-1051004: MySQL日志Reader日志内容校验失败 {#concept_uh2_kkf_2fb .concept}

DTS MySQL日志Reader日志内容校验失败

## DTS-1051004 BEGIN and COMMIT middle have any other evetns, Please vaild binlog file. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1051004 BEGIN and COMMIT middle have any other evetns, Please vaild binlog file.

其中, DTS 报错code为: DTS-1051004

DTS的报错语句格式为: DTS-1051004 BEGIN and COMMIT middle have any other evetns, Please vaild binlog file.

**失败原因**

1. MySQL 日志BEGIN和COMMIT 中间没有有效的DML操作,如果源库使用RDS只读实例会出现该问题,原因是RDS只读实例没有Binlog,无法做增量迁移和同步

**解决方案**

1. 检查源库是否是RDS只读实例,如果是只读实例需要使用非只读实例重新配置任务.


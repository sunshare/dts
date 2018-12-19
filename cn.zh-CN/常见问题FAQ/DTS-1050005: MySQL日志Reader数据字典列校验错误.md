# DTS-1050005: MySQL日志Reader数据字典列校验错误 {#concept_acb_gkf_2fb .concept}

DTS MySQL日志Reader数据字典列校验错误

## DTS-1050005 MySQL table rdsdb.dept binlog column count check error, local count is 3, but binlog count is 4, at offset 3@4105797. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1050005 MySQL table xxx.xxx binlog column count check error, local count is 3, but binlog count is 4, at offset 3@4105797.

其中, DTS 报错code为: DTS-1050005

DTS的报错语句格式为: DTS-1050005 MySQL table rdsdb.dept binlog column count check error, local count is 3, but binlog count is 4, at offset 3@4105797. 其中,xxx.xxx是对应出错表名, 3是DTS数据字典列数量,4是binlog列数量,3@4105797是对应出错的binlog event所处的文件编号和偏移位置

**失败原因**

1. DTS DDL解析失败导致的数据字典更新错误

2. DTS任务创建过程中源库发生了对应DDL

3. DDL 操作的表不在迁移对象内, 发生了rename 同步对象外的表到同步对象内

**解决方案**

1. 提交工单说明最近执行的DDL,联系DTS值班协助解决.

2. 建议给DTS的源库帐号有整个information\_schema的查询权限.


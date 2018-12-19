# DTS-1050003: 当前抓取的MySQL日志不存在 {#concept_jng_2kf_2fb .concept}

DTS 当前抓取的MySQL日志不存在

## DTS-150003 MySQL binlog timstamp 12345678 is not found on server. Original error: original\_error. {#section_eky_jgz_dgb .section}

**DTS报错信息**

DTS-150003 MySQL binlog timstamp 12345678 is not found on server. Original error: original\_error.

其中, DTS 报错code为: DTS-150003

DTS的报错语句格式为:MySQL binlog timstamp 12345678 is not found on server. Original error: original\_error. 其中,12345678 是MySQL binlog 对应的头部时间戳, original\_error 是java驱动报错内容

**失败原因**

1. 如果任务暂停超过7天, RDS日志被清理库, 需要重新创建任务.

2. 源库的Binlog保留时间过短, 建议源库MySQL日志至少保留3天.

**解决方案**

1. 如果源数据库是RDS, 需要提交工单,联系DTS值班辅助处理

2. 自建数据库需要恢复相应的binlog文件


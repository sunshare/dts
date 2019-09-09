# DTS-1050002: 当前抓取的MySQL日志不存在 {#concept_sjd_dkf_2fb .concept}

当DTS提示

DTS 当前抓取的MySQL日志不存在

## 报错信息 {#section_pkr_2lj_ayv .section}

DTS-150002 MySQL binlog filename@offset is not exists. Original error: original\_error.

DTS的报错语句格式为：`MySQL binlog filename@offset is not exists. Original error: original_error.`。其中，`filename@offset`是Binlog位置。

## 可能的原因及解决方法 {#section_a3r_ll9_o7z .section}

|可能的原因|解决方法|
|-----|----|
|数据迁移任务暂停超过七天或RDS日志被清理。|重新创建数据迁移任务。|
|源库的Binlog保留时间过短|建议源库MySQL日志至少保留3天或恢复相应的binlog文件。|


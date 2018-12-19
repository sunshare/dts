# DTS-1050004: MySQL SQL解析失败 {#concept_zld_fkf_2fb .concept}

DTS MySQL 解析引擎解析SQL失败

## MySQL binlog sql sql\_xx parse error, binlog at filename@offset. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-150004 MySQL binlog sql sql\_xx parse error, binlog at filename@offset

其中, DTS 报错code为: DTS-150004

DTS的报错语句格式为:MySQL binlog sql sql\_xx parse error, binlog at filename@offset. Original error: original\_error.。其中,sql\_xx 为binlog 内sql语句, filename@offset是对应binlog位置,original\_error 是java驱动报错内容

**失败原因**

1. DTS内部解析引擎解析SQL失败

**解决方案**

1. 需要提交工单,联系DTS值班辅助处理


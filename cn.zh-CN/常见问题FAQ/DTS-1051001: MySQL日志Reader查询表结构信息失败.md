# DTS-1051001: MySQL日志Reader查询表结构信息失败 {#concept_uh2_kkf_2fb .concept}

DTS MySQL日志Reader查询表结构信息失败

## DTS-1051001 Query remote MySQL instance xx.xx.xx.xx:xx table db\_name.table\_name meta fail. Original error: original\_error. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1051001 Query remote MySQL instance xx.xx.xx.xx:xx table db\_name.table\_name meta fail. Original error: original\_error.

其中, DTS 报错code为: DTS-1051001

DTS的报错语句格式为: DTS-1051001 Query remote MySQL instance xx.xx.xx.xx:xx table db\_name.table\_name meta fail. Original error: original\_error. 其中:对应查询数据库的连接信息是xx.xx.xx.xx:xx ,查询失败的表名称是db\_name.table\_name, original\_error是java驱动报错内容

**失败原因**

1. DTS 查询表结构可能存在查询超时

**解决方案**

1.根据java驱动报错内容定位原因

2. 提交工单,联系DTS值班协助处理.


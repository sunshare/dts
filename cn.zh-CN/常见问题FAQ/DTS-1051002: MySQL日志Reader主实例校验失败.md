# DTS-1051002: MySQL日志Reader主实例校验失败 {#concept_uh2_kkf_2fb .concept}

DTS MySQL日志Reader主实例校验失败

## DTS-1051002 Current connect MySQL instance xx.xx.xx.xx:xx is not master. {#section_xk1_ncv_cgb .section}

**DTS报错信息**

DTS-1051002 Current connect MySQL instance xx.xx.xx.xx:xx is not master.

其中, DTS 报错code为: DTS-1051002

DTS的报错语句格式为: Current connect MySQL instance xx.xx.xx.xx:xx is not master. 其中:xx.xx.xx.xx:xx对应数据库连接信息

**失败原因**

1. DTS MySQL日志Reader主实例验证失败

**解决方案**

1. 修该任务配置,使用MySQL主实例重新配置任务


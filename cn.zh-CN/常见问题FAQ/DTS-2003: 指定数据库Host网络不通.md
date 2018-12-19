# DTS-2003: 指定数据库Host网络不通 {#concept_sqq_wqd_x2b .concept}

DTS服务器和指定的Host之间网络不通

## DTS-2003 Connect db failure, unknow db url xx.xx.xx.xx:xx, please vaild url. Original error: Communications link failure The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server. {#section_uhc_qrh_t2b .section}

**DTS报错信息**

DTS-2003 Connect db failure, unknow db url xx.xx.xx.xx:xx, please vaild url. Original error: Communications link failure The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.

其中, DTS 报错code为: DTS-2003.

DTS的报错语句格式为: Connect db failure, unknow db url xx.xx.xx.xx, please vaild url. Original error: xxxx。其中, xx.xx.xx.xx:xx是对应的数据库地址, xxxx 是java驱动报错内容。

**失败原因**

根据接入方式不同,可能的原因有:

1.自建库接入: 地址填写错误, DTS到自建库的网络不通

2. 专线接入: 专线不通

**解决方案**

1.自建库接入:检查地址, 测试数据库公网连

2. 专线接入: 测试专线连通性

3. 以上方法都无法解决: 需要提交工单, 联系DTS技术人员


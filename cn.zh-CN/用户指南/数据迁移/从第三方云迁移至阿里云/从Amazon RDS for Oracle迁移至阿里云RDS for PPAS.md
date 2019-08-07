# 从Amazon RDS for Oracle迁移至阿里云RDS for PPAS {#concept_dd5_gjv_fhb .concept}

本文介绍使用数据传输服务DTS（Data Transmission Service），将Amazon RDS for Oracle数据库迁移至阿里云RDS for PPAS数据库。

## 前提条件 {#section_nnr_lkv_fhb .section}

-   源端 Oracle 实例版本须为10g，11g或12c版本。
-   为保障DTS可以通过公网连接至Amazon RDS for Oracle，Amazon RDS for Oracle的网络与安全配置中须将公开可用性功能设置为**是**。
-   已经[创建RDS for PPAS实例](https://help.aliyun.com/document_detail/53731.html)。
-   已经[创建拥有读写权限的账号](https://help.aliyun.com/document_detail/96933.html)。

## 注意事项 {#section_mqg_bx0_e21 .section}

对于七天之内的异常任务，DTS会尝试自动恢复，可能会导致迁移任务的源端数据库数据覆盖目标实例数据库中写入的业务数据，迁移任务结束后务必将DTS访问目标实例账号的**写权限**用`revoke`命令回收掉。

## 迁移账号权限要求 {#section_nyt_yjg_ap7 .section}

|迁移数据源|结构迁移|全量迁移|增量数据迁移|
|:----|:---|:---|:-----|
|Amazon RDS for Oracle数据库|schema的owner权限|schema的owner权限|MASTER USER 具备的权限|
|RDS for MySQL实例|待迁入数据库的读写权限|待迁入数据库的读写权限|待迁入数据库的读写权限|

**数据库账号创建及授权方法：**

-   Amazon RDS for Oracle数据库请参见[CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm)和[GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm)。
-   RDS for MySQL实例请参见[创建账号](https://help.aliyun.com/document_detail/96089.html)和[修改账号权限](https://help.aliyun.com/document_detail/96101.html)。

## 迁移限制 {#section_avu_st9_ahq .section}

-   迁移过程中，不支持DDL操作。
-   不支持物化视图的迁移。
-   结构迁移时，reverse index迁移到RDS For PPAS中，存储成普通索引。
-   结构迁移时，位图索引迁移到RDS For PPAS，存储成普通索引。
-   结构迁移时，分区索引迁移到RDS For PPAS，在每个分区上创建独立的索引。
-   由于AWS数据库不提供dbcreator和sysadmin角色权限，暂不支持增量迁移。

## 数据类型映射关系 {#section_l1h_2lw_fhb .section}

由于Oracle跟RDS for PPAS的数据类型不是一一对应的，所以数据传输服务在进行结构迁移时，会根据两种数据库类型的数据类型定义，进行类型映射，下表为数据传输服务定义的数据类型映射关系。

|Oracle数据类型|PPAS数据类型|数据传输服务是否支持|
|----------|--------|----------|
|varchar2\(n \[char/byte\]\)|varchar2\[\(n\)\]|支持|
|nvarchar2\[\(n\)\]|nvarchar2\[\(n\)\]|支持|
|char\[\(n \[byte/char\]\)\]|char\[\(n\)\]|支持|
|nchar\[\(n\)\]|nchar\[\(n\)\]|支持|
|number\[\(p\[,s\]\)\]|number\[\(p\[,s\]\)\]|支持|
|float\(p\)\]|double precision|支持|
|long|long|支持|
|date|date|支持|
|binary\_float|real|支持|
|binary\_double|double precision|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]|timestamp\[\(fractional\_seconds\_precision\)\]|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]with time zone|timestamp\[\(fractional\_seconds\_precision\)\]with time zone|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]with local time zone|timestamp\[\(fractional\_seconds\_precision\)\]with time zone|支持|
|clob|clob|支持|
|nclob|nclob|支持|
|blob|blob|支持|
|raw|raw\(size\)|支持|
|long raw|long raw|支持|
|bfile|—|不支持|
|interval year\(year\_precision\) to month|interval year to month|不支持|
|interval day\(day\_precision\) to second\[\(fractional\_seconds\_precision\)\]|interval day to second\[\(fractional\_seconds\_precision\)\]|不支持|

**说明：** 由于RDS For PPAS不支持数据类型timestamp\[\(fractional\_seconds\_precision\)\]with local time zone，所以数据传输服务在迁移这种类型的数据时，会将其转换成UTC时区后，存入RDS For PPAS的数据类型timestamp\[\(fractional\_seconds\_precision\)\]with time zone中。

## 操作步骤 {#section_ixj_jmw_fhb .section}

1.  登陆Amazon RDS控制台。
2.  进入Amazon RDS for Oracle实例的基本信息页面。查看连接和安全性页面的**终端节点**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150426/156514033141904_zh-CN.png)

3.  单击下方安全组规则中的安全组名称，打开安全组管理页面。
4.  右键目标安全组名称，选择**编辑入站规则**。
5.  单击**添加规则**，放通DTS里的源库实例地区的IP地址，单击**保存**。具体参数配置说明如下：

    |参数|说明|
    |--|--|
    |类型|入站的数据类型，这里选择**Oracle-RDS**|
    |来源|选择**自定义**，在右侧框内粘贴[DTS IP段](https://help.aliyun.com/document_detail/84900.html)里的IP地址，用英文逗号（,）分隔。|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150426/156514033141912_zh-CN.png)

6.  登录[DTS控制台](https://dts.console.aliyun.com/)。
7.  在左侧菜单栏单击**数据迁移**，单击右上角**创建迁移任务**。
8.  填写源库和目标库信息，具体参数配置说明如下：

    |库类别|参数|说明|
    |---|--|--|
    |源库|实例类型|源库实例类型，这里选择**有公网IP的自建数据库**。|
    |实例地区|如果您的实例进行了访问限制，请先放开对应地区公网IP段的访问权限后，再配置数据迁移任务。 **说明：** 可以单击右侧**获取DTS IP段**查看、复制对应地区的IP段。

 |
    |数据库类型|源数据库类型，这里选择**Oracle**。|
    |主机名或IP地址|Amazon RDS for Oracle实例的**终端节点**。|
    |端口|默认的1521端口。|
    |实例类型|根据源实例类型进行选择，这里选择**非RAC实例**。|
    |SID|Oracle数据库的SID|
    |数据库账号|Amazon RDS for Oracle数据库的主用户账号。|
    |数据库密码|Amazon RDS for Oracle数据库的主用户密码。|
    |目标库|实例类型|目标实例的类型，这里选**RDS实例**。|
    |实例地区|目标实例的地区。|
    |RDS实例ID|对应地区下的实例ID，这里选择想要迁移到的目标实例的ID。|
    |数据库账号|目标实例的拥有读写权限的账号。|
    |数据库密码|目标实例的对应账号的密码。|
    |连接方式|有**非加密传输**和**SSL安全连接**两种连接方式，选择SSL安全加密连接会显著增加CPU消耗。|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150426/156514033141913_zh-CN.png)

9.  填写完毕后单击**测试连接**，确定源库和目标库都测试通过。
10. 单击**授权白名单并进入下一步**。
11. 选择**迁移对象**和**迁移类型**。

    **说明：** 

    -   由于AWS数据库不提供dbcreator和sysadmin角色权限，暂不支持增量迁移。
    -   为保障数据一致性，迁移期间请勿在Amazon RDS for Oracle数据库中写入新的数据。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150426/156514033141914_zh-CN.png)

12. 单击**预检查并启动**，等待预检查结束。

    **说明：** 如果检查失败，可以根据错误项的提示进行修复，然后重新启动任务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150426/156514033141915_zh-CN.png)

13. 单击**下一步**，在**购买配置确认**对话框中，勾选**《数据传输（按量付费）服务条款》**并单击**立即购买并启动**。
14. 单击目标地域，查看迁移状态。迁移完成时，状态为已完成。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150426/156514033241916_zh-CN.png)

    至此，完成 Amazon RDS for Oracle数据库迁移到阿里云 RDS for PPAS 的数据迁移任务。



# 从Amazon SQL Server迁移到阿里云 {#concept_pc4_mcq_fhb .concept}

本文以Amazon SQL Server 2016迁移到阿里云RDS for SQL Server 2016为例，详细说明操作步骤及注意事项。

## 迁移限制 {#section_ang_jbk_5fb .section}

-   由于Amazon数据库不提供sysadmin角色权限，暂不支持增量迁移。
-   源库SQL Server结构迁移和全量数据迁移支持如下版本：

    -   SQL Server 2016
    -   SQL Server 2014
    -   SQL Server 2012
    -   SQL Server 2008 R2
    -   SQL Server 2008
    -   SQL Server 2005
    **说明：** 如果需要跨版本迁移，请提前确认兼容性。

-   如果迁移的对象使用了对象名映射功能，则有可能导致依赖该对象的其他对象迁移失败。
-   不支持sql\_variant数据类型。
-   结构迁移不支持assemblies、service broker、全文索引、全文目录、分布式schema、分布式函数、CLR存储过程、CLR标量函数、CLR标值函数、内部表、聚合函数、系统的迁移。

## 前提条件 {#section_vw2_ycl_5fb .section}

-   Amazon SQL Server 2016实例需要开启**公开可用性**（否则无法通过外网访问）。
-   已经[创建RDS for SQL Server实例](../cn.zh-CN/RDS for SQL Server 快速入门/创建RDS for SQL Server实例.md#)。
-   已经[创建拥有读写权限的账号](../cn.zh-CN/RDS for SQL Server 快速入门/初始化配置/创建数据库和账号/创建数据库和账号SQL Server 2012__2016版.md#)。
-   当使用DTS进行SQL Server迁移时，源Amazon SQL Server数据库实例及目标RDS for SQL Server实例的迁移账号权限要求如下：

    |库类型|结构迁移|全量迁移|
    |---|----|----|
    |源Amazon SQL Server实例|select|select|
    |目的RDS for SQL Server实例|读写权限|读写权限|


## 注意事项 {#section_ypx_pbk_5fb .section}

-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。
-   对于七天之内的异常任务，DTS会尝试自动恢复，可能会导致迁移任务的源端数据库数据覆盖目标实例数据库中写入的业务数据，迁移任务结束后务必将DTS访问目标实例账号的**写权限**用`revoke`命令回收掉。

## 操作步骤 {#section_uwt_sck_5fb .section}

1.  登录Amazon SQL Server数据库实例，在总览页面单击安全组规则的任意条目。

    ![进入安全组设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517441830_zh-CN.png)

2.  在下方选择**入站** \> **编辑**。

    ![编辑安全组](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517441840_zh-CN.png)

3.  单击**添加规则**，将对应区域的DTS服务器地址添加至入站规则中，IP地址段详情请参考[DTS IP地址段](https://help.aliyun.com/document_detail/84900.html)，单击**保存**。具体参数配置说明如下：

    |参数|说明|
    |--|--|
    |类型|入站数据的类型，这里选择**所有流量**。|
    |来源|选择**自定义**，在右侧框里粘贴IP，用英文逗号（,）分隔。 **说明：** 

    -   您只需放开目标数据库所在区域对应的DTS IP地址段。本示例中，源数据库地区为首尔，目标数据库地区为杭州，您只需要放开杭州地区的DTS IP地址段。
    -   添加IP时在**来源**右侧框里直接粘贴所有IP即可，保存后会自动生成多条规则。
 |

    ![安全组设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517441831_zh-CN.png)

4.  返回总览页面，在连接和安全性页签查看**终端节点**和**端口**。

    ![查看连接](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517541841_zh-CN.png)

5.  登录[DTS控制台](https://dts.console.aliyun.com/)。
6.  在左侧菜单栏单击**数据迁移**，单击右上角**创建迁移任务**。
7.  填写源库和目标库信息，具体参数配置说明如下：

    |库类别|参数|说明|
    |---|--|--|
    |源库信息|实例类型|源库实例类型，这里选择**有公网IP的自建数据库**。|
    |实例地区|源库**实例类型**为**有公网IP的自建数据库**时忽略该参数，无需设置。|
    |数据库类型|源数据库类型，这里选择**SQLServer**。|
    |主机名或IP地址|Amazon数据库的**终端节点**。|
    |端口|Amazon数据库的**端口**。|
    |数据库账号|Amazon数据库主用户账号。|
    |数据库密码|Amazon数据库主用户密码。|
    |目标库信息|实例类型|这里选择**RDS实例**。|
    |实例地区|目标实例的地区。|
    |RDS实例ID|目标RDS for SQL Server实例的ID。|
    |数据库账号|目标实例的拥有读写权限的账号。|
    |数据库密码|目标实例的对应账号的密码。|

    ![迁移任务配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517541842_zh-CN.png)

8.  填写完成后单击**测试连接**，确定源库和目标库都**测试通过**。
9.  单击**授权白名单并进入下一步**。
10. 在迁移对象框中将要迁移的数据库选中，单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63394/156523517531842_zh-CN.png)移动到已选择对象框。

    **说明：** 

    -   由于Amazon数据库不提供dbcreator和sysadmin角色权限，暂不支持增量迁移。
    -   结构迁移和全量迁移任务暂不收费。
    ![选择要迁移数据库](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517541843_zh-CN.png)

11. 单击**预检查并启动**，等待预检查结束。

    **说明：** 如果预检查失败，可以根据错误项的提示进行修复，然后重新启动任务。

    ![预检查](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517541845_zh-CN.png)

12. 单击**下一步**，在**购买配置确认**对话框中，勾选**《数据传输（按量付费）服务条款》**并单击**立即购买并启动**。

    ![购买迁移配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517641846_zh-CN.png)

13. 等待迁移任务完成即可。

    ![迁移成功](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150143/156523517641847_zh-CN.png)



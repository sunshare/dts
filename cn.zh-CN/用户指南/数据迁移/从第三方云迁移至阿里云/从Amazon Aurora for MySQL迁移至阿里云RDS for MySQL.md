# 从Amazon Aurora for MySQL迁移至阿里云RDS for MySQL {#concept_trf_zgx_fhb .concept}

本文介绍使用阿里云[数据传输服务（DTS）](https://help.aliyun.com/product/26590.html)，从 Amazon Aurora 迁移 MySQL 到阿里云RDS。

## 前提条件 {#section_vzt_dhx_fhb .section}

-   为保障DTS可以通过公网连接至Amazon Aurora for MySQL，Amazon Aurora for MySQL的网络与安全配置中须将公开可用性功能设置为**是**。
-   已经[创建阿里云RDS实例](../../../../cn.zh-CN/RDS for MySQL 快速入门/创建RDS for MySQL实例.md#)。

## 迁移限制 {#section_ang_jbk_5fb .section}

-   结构迁移不支持 event 的迁移。
-   对于MySQL的浮点型float/double，DTS通过round\(column,precision\)来读取该列的值，若列类型没有明确定义其精度，对于float，精度为38位，对于double类型，精度为308，请先确认DTS的迁移精度是否符合业务预期。
-   如果使用了对象名映射功能，依赖这个对象的其他对象可能迁移失败。
-   当选择增量迁移时，源端的Aurora实例需要按照要求开启 binlog。
-   当选择增量迁移时，源库的 binlog\_format 要为 row。
-   当选择增量迁移且源 MySQL 如果为 5.6 及以上版本时，它的 binlog\_row\_image 必须为 full。

## 迁移账号权限要求 {#section_eby_wfq_fhb .section}

|迁移数据源|结构迁移|全量迁移|增量迁移|
|:----|:---|:---|----|
|Amazon Aurora for MySQL实例|迁移对象的select权限|迁移对象的select权限|迁移对象的select、replication slave、replication client权限|
|阿里云RDS for MySQL实例|迁移对象的读写权限|迁移对象的读写权限|迁移对象的读写权限|

## 注意事项 {#section_ypx_pbk_5fb .section}

-   对于七天之内的异常任务，DTS会尝试自动恢复，可能会导致迁移任务的源端数据库数据覆盖目标实例数据库中写入的业务数据，迁移任务结束后务必将DTS访问目标实例账号的**写权限**用`revoke`命令回收掉。

## 操作步骤 {#section_nkp_pjx_fhb .section}

1.  登录Amazon RDS控制台。
2.  进入Amazon Aurora for MySQL实例的基本信息页面。
3.  选择角色为**写入器**的节点。
4.  查看连接和安全性页面的**终端节点**信息和**安全组**信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150461/156254991741949_zh-CN.png)

5.  单击**安全组**名称，打开安全组的管理页面。
6.  右键点击目标安全组，单击**编辑入站规则**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150461/156254991741950_zh-CN.png)

7.  单击**添加规则**，放通DTS中[源实例地区](#table_jwb_1ns_ngb)对应的IP地址，单击**保存**。具体参数配置说明如下：

    |参数|说明|
    |--|--|
    |类型|入站的数据类型，这里选择**MySQL/Aurora**|
    |来源|选择**自定义**，在右侧框内粘贴[DTS IP段](https://help.aliyun.com/document_detail/84900.html)里的IP地址，用英文逗号（,）分隔。|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150461/156254991741951_zh-CN.png)

8.  登录[DTS控制台](https://dts.console.aliyun.com/)。
9.  在左侧菜单栏单击**数据迁移**，单击右上角**创建迁移任务**。
10. 填写源库和目标库信息，具体参数配置说明如下：

    |库类别|参数|说明|
    |---|--|--|
    |源库|实例类型|源库实例类型，这里选择**有公网IP的自建数据库**。|
    |实例地区|如果您的实例进行了访问限制，请先放开对应地区公网IP段的访问权限后，再配置数据迁移任务。 **说明：** 可以单击右侧**获取DTS IP段**查看、复制对应地区的IP段。

 |
    |数据库类型|源数据库类型，这里选择**MySQL**。|
    |主机名或IP地址|Amazon Aurora for MySQL实例的**终端节点**。|
    |端口|默认的3306端口。|
    |数据库账号|Amazon Aurora for MySQL数据库的主用户账号。|
    |数据库密码|Amazon Aurora for MySQL数据库的主用户密码。|
    |目标库|实例类型|目标实例的类型，这里选**RDS实例**。|
    |实例地区|目标实例的地区。|
    |RDS实例ID|对应地区下的实例ID，这里选择想要迁移到的目标实例的ID。|
    |数据库账号|目标实例的拥有读写权限的账号。|
    |数据库密码|目标实例的对应账号的密码。|
    |连接方式|目标实例的连接方式，选择**非加密传输**。|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150461/156254991741952_zh-CN.png)

11. 填写完毕后单击**测试连接**，确定源库和目标库都测试通过。
12. 单击**授权白名单并进入下一步**。
13. 选择**迁移对象**和**迁移类型**。

    **说明：** 

    -   为保证迁移数据的一致性，建议选择结构迁移+全量数据迁移+增量数据迁移。
    -   结构迁移和全量数据迁移暂不收费，增量数据迁移根据链路规格按小时收费。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150461/156254991741953_zh-CN.png)

14. 单击**预检查并启动**，等待预检查结束。

    **说明：** 如果检查失败，可以根据错误项的提示进行修复，然后重新启动任务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150461/156254991841954_zh-CN.png)

15. 单击**下一步**，在**购买配置确认**对话框中，勾选**《数据传输（按量付费）服务条款》**并单击**立即购买并启动**。

    如果选择了增量迁移，那么进入增量迁移阶段后，源库的更新写入都会被DTS同步到目标RDS实例。迁移任务不会自动结束。如果您只是为了迁移，那么建议在增量迁移无延迟的状态时，源库停写几分钟，等待增量迁移再次进入无延迟状态后，停止掉迁移任务，直接将业务切换到目标RDS实例上即可。

16. 单击目标地域，查看迁移状态。当迁移停止后，状态为已完成。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150461/156254991841955_zh-CN.png)

    **说明：** 当增量迁移无延迟时，AWS和阿里云RDS上面的数据一致，可以停止迁移任务。由于AWS会以最快的速度回收binlog，而增量迁移依赖源库的binlog日志，为了防止未增量同步的binlog日志被清除，您可以调用AWS的存储过程 `mysql.rds_set_configurations`来设置binlog的保存时间。例如将保存时间延长至一天，调用这个存储过程的命令为：

    ``` {#codeblock_ecn_ifh_7as}
    call mysql.rds_set_configurations(“binlog retention hours” 24)
    ```

    至此，完成 Amazon Aurora for MySQL数据库迁移到阿里云 RDS for MySQL的数据迁移任务。



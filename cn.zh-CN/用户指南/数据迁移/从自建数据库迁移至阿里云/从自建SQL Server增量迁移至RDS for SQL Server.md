# 从自建SQL Server增量迁移至RDS for SQL Server {#concept_287101 .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将自建SQL Server增量迁移至RDS for SQL Server。DTS支持结构迁移、全量数据迁移以及增量数据迁移，同时使用这三种迁移类型可以实现在自建应用不停服的情况下，平滑地完成自建SQL Server数据库迁移上云。

## 源库支持的实例类型 {#section_jl7_w8l_av7 .section}

执行数据迁移操作的SQL Server数据库支持以下实例类型：

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库

本文以**有公网IP的自建数据库**为例介绍增量数据迁移的配置流程。如果仅需要全量数据迁移，请参见[从自建SQL Server全量迁移至RDS for SQL Server](cn.zh-CN/用户指南/数据迁移/从自建数据库迁移至阿里云/从自建SQL Server全量迁移至RDS for SQL Server.md#)。

## 前提条件 {#section_d8a_429_hom .section}

-   自建SQL Server数据库版本为2008、2008 R2、2012、2014或2016版本。

    **说明：** 如需跨版本迁移，请提前确认兼容性。

-   自建SQL Server数据库中待迁移的表需具备主键或者唯一性非空索引。
-   RDS for SQL Server实例的存储空间须大于自建SQL Server数据库占用的存储空间。
-   自建SQL Server数据库的服务端口已开放至公网。

## 注意事项 {#section_h8z_uz0_ei2 .section}

-   为保证增量数据迁移延迟显示的准确性，DTS会在自建SQL Server数据库中新增一张心跳表，表的表名为*待迁移表名*\_dts\_mysql\_heartbeat。
-   如果待迁移的数据库在目标RDS for SQL Server中不存在，DTS会自动创建。但是对于如下两种情况，您需要在配置迁移任务之前在目标RDS for SQL Server中[创建数据库](https://help.aliyun.com/document_detail/43164.html#h2-url-2)。
    -   数据库名称不符合RDS定义规范。具体规范请参见[创建数据库](https://help.aliyun.com/document_detail/43164.html#h2-url-2)。
    -   待迁移数据库在源SQL Server数据库与目标RDS for SQL Server实例中的名称不同。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例，请务必先终止或释放该任务，避免该任务被自动恢复后，导致源端数据覆盖目标实例的数据。

## 数据迁移限制 {#section_ww4_vq7_kwr .section}

-   不支持assemblies、service broker、全文索引、全文目录、分布式schema、分布式函数、CLR存储过程、CLR标量函数、CLR标值函数、内部表、系统、聚合函数的结构迁移。
-   不支持迁移数据类型为sql\_variant的数据。
-   不支持迁移含有计算列的表。
-   一个数据迁移任务只能对一个数据库进行增量数据迁移，如果有多个数据库需要增量数据迁移，则需要为每个数据库创建数据迁移任务。

## 迁移类型说明 {#section_v5n_hab_7df .section}

-   结构迁移

    DTS将迁移对象的结构定义迁移到目标实例，目前DTS支持结构迁移的对象为表、视图、表触发器、同义词、SQL 存储过程、SQL 函数、plan guid、自定义类型、rule和default。

-   全量数据迁移

    DTS会将自建SQL Server数据库迁移对象的存量数据，全部迁移到目标RDS for SQL Server数据库中。

-   增量数据迁移

    DTS在全量数据迁移的基础上读取自建SQL Server数据库的日志信息，将自建SQL Server数据库的增量更新数据同步到目标RDS for SQL Server数据库中。通过增量数据迁移可以实现在自建应用不停服的情况下，平滑地完成自建SQL Server数据库迁移上云。


## 增量数据迁移支持同步的SQL操作 {#section_48w_t9l_ph6 .section}

-   INSERT、UPDATE、DELETE

    **说明：** 不支持同步只更新大字段的UPDATE语句。

-   CREATE TABLE

    **说明：** 不支持分区、表定义内部包含函数。

-   ALTER TABLE，仅包含ADD COLUMN、DROP COLUMN、RENAME COLUMN
-   DROP TABLE
-   RENAME TABLE、TRUNCATE TABLE、CREATE INDEX

## 费用说明 {#section_u5c_s0r_llq .section}

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|不收取|

## 数据库账号的权限要求 {#section_859_54s_t60 .section}

|迁移数据源|结构迁移|全量迁移|增量迁移|
|:----|:---|:---|:---|
|自建SQL Server|select权限|select权限|sysadmin|
|RDS for SQL Server|读写权限|读写权限|读写权限|

**数据库账号创建及授权方法：**

-   自建SQL Server数据库请参见[CREATE USER](https://docs.microsoft.com/zh-cn/sql/t-sql/statements/create-user-transact-sql?view=sql-server-2017)。
-   RDS for SQL Server实例请参见[创建账号](https://help.aliyun.com/document_detail/95810.html)。

## 增量数据迁移流程 {#section_hr4_p2j_pdo .section}

为解决对象间的依赖，提高迁移成功率，DTS对SQL Server结构和数据的迁移流程如下：

1.  进行表、视图、同义词、自定义类型、rule、default和plan guid的结构迁移。
2.  进行全量数据迁移。
3.  进行SQL存储过程、SQL函数、触发器和外键的结构迁移。
4.  进行增量数据迁移。

    **说明：** 在进行增量数据迁移前，请勿对自建SQL Server数据库中的迁移对象进行DDL操作，否则可能导致迁移失败。


## 数据迁移前准备工作 {#section_4iy_dc9_zfj .section}

在正式配置数据迁移任务之前，需要在自建SQL Server数据库上进行日志配置。

1.  在自建SQL Server数据库中执行如下命令，将待迁移的数据库恢复模式修改为完整模式。

    ``` {#codeblock_07i_r42_e97}
    use master;
    GO
    ALTER DATABASE <database_name> SET RECOVERY FULL WITH ROLLBACK IMMEDIATE;
    GO
    ```

    **参数说明：**

    <database\_name\>：待迁移的数据库名。

    **示例：**

    ``` {#codeblock_8ri_fiu_pdf}
    use master;
    GO
    ALTER DATABASE mytestdata SET RECOVERY FULL WITH ROLLBACK IMMEDIATE;
    GO
    ```

2.  执行如下命令，将待迁移的数据库进行逻辑备份。如您已进行过逻辑备份，可跳过本步骤。

    ``` {#codeblock_nk5_i7a_p4b}
    BACKUP DATABASE <database_name> TO DISK='<physical_backup_device_name>';
    GO
    ```

    **参数说明：**

    -   <database\_name\>：待迁移的数据库名。
    -   <physical\_backup\_device\_name\>：指定备份文件存储的路径和文件名。
    **示例：**

    ``` {#codeblock_83n_c05_e3o}
    BACKUP DATABASE mytestdata TO DISK='D:\backup\dbdata.bak';
    GO
    ```

3.  执行如下命令，将待迁移的数据库日志进行备份。

    ``` {#codeblock_iq0_cgk_yis}
    BACKUP LOG <database_name> to DISK='<physical_backup_device_name>' WITH init;
    GO
    ```

    **参数说明：**

    -   <database\_name\>：待迁移的数据库名。
    -   <physical\_backup\_device\_name\>：指定备份文件存储的路径和文件名。
    **示例：**

    ``` {#codeblock_i5a_l9o_n32}
    BACKUP LOG mytestdata TO DISK='D:\backup\dblog.bak' WITH init;
    GO
    ```


## 操作步骤 {#section_p08_wai_hvz .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711733/156560278750439_zh-CN.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![源库和目标库连接配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17106/156560278747832_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**有公网IP的自建数据库**。|
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 如果您的自建SQL Server数据库进行了白名单安全设置，您需要在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入自建SQL Server数据库的白名单安全设置中。

 |
    |数据库类型|选择**SQL Server**。|
    |主机名或IP地址|填入自建SQL Server数据库的访问地址，本案例中填入公网地址。|
    |端口|填入自建SQL Server数据库的服务端口，默认为**1433**。|
    |数据库账号|填入自建SQL Server数据库的连接账号，权限要求请参见[数据库账号的权限要求](#section_859_54s_t60)。|
    |数据库密码|填入自建SQL Server数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择目标RDS实例所属地域。|
    |RDS实例ID|选择目标RDS实例ID。|
    |数据库账号|填入连接目标RDS实例数据库的账号，权限要求请参见[数据库账号的权限要求](#section_859_54s_t60)。|
    |数据库密码|填入连接目标RDS实例数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标RDS实例的白名单中，用于保障DTS服务器能够正常连接目标RDS实例。迁移完成后如不再需要可手动删除，详情请参见[设置白名单](https://help.aliyun.com/document_detail/43186.html)。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17107/156560278847858_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量数据迁移，则同时勾选**结构迁移**和**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建SQL Server数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。
 本案例为增量数据迁移，同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。

 |
    |迁移对象| 在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156560278840698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 

    -   一个数据迁移任务只能对一个数据库进行增量数据迁移，如果有多个数据库需要增量数据迁移，则需要为每个数据库创建数据迁移任务。
    -   迁移对象选择的粒度可以为库、表、列三个粒度。
    -   默认情况下，迁移完成后，迁移对象名跟自建SQL Server数据库一致。如果您需要迁移对象在目标RDS实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。
 |

8.  单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156560278847468_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
9.  预检查通过后，单击**下一步**。
10. 在购买配置确认页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
11. 单击**购买并启动**，迁移任务正式开始。
    -   全量数据迁移

        请勿手动结束迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

    -   增量数据迁移

        迁移任务不会自动结束，您需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至目标实例时。

        1.  观察迁移任务的进度变更为**增量迁移**，并显示为**无延迟**状态时，将源库停写几分钟，此时**增量迁移**的状态可能会显示延迟的时间。
        2.  等待迁移任务的**增量迁移**再次进入**无延迟**状态后，手动结束迁移任务。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156560278847604_zh-CN.png)

12. 将业务切换至RDS实例。

## 后续操作 {#section_xug_f8d_sx7 .section}

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除自建SQL Server数据库和RDS for SQL Server实例中的数据库账号。


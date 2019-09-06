# POLARDB for MySQL集群间的数据迁移 {#task_1813884 .task}

POLARDB是阿里巴巴自主研发的下一代关系型分布式云原生数据库，可完全兼容MySQL，具备简单易用、高性能、高可靠、高可用等优势。通过数据传输服务DTS（Data Transmission Service），可以帮助您实现POLARDB for MySQL集群间的数据迁移。

-   已购买源和目标POLARDB for MySQL集群，详情请参见[创建POLARDB for MySQL集群](https://help.aliyun.com/document_detail/58769.html)。
-   为满足增量数据迁移的要求，源POLARDB for MySQL集群需要开启Binlog，详情请参见[如何开启Binlog](https://help.aliyun.com/document_detail/113546.html)。

## 注意事项 {#section_m17_4v5_vwg .section}

-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标集群，请务必先停止或释放该任务，避免该任务被自动恢复后，导致源端数据覆盖目标集群的数据。

## 迁移类型介绍 {#section_8vr_syz_69z .section}

支持结构迁移、全量数据迁移和增量数据迁移，详细介绍请参见[名词解释](../../../../cn.zh-CN/产品简介/名词解释.md#)。

**说明：** 同时使用这三种迁移类型可实现在应用不停服的情况下，平滑地完成数据库迁移。

## 费用说明 {#section_oil_1bq_efg .section}

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|结构迁移/全量数据迁移|不收费|通过公网进行数据迁移时收费，详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|
|增量数据迁移|收费，详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|

## 增量数据迁移阶段支持同步的SQL操作 {#section_mpx_c9r_6w8 .section}

-   INSERT、UPDATE、DELETE、REPLACE
-   ALTER TABLE、ALTER VIEW、ALTER FUNCTION、ALTER PROCEDURE
-   CREATE DATABASE、CREATE SCHEMA、CREATE INDEX、CREATE TABLE、CREATE PROCEDURE、CREATE FUNCTION、CREATE TRIGGER、CREATE VIEW、CREATE EVENT
-   DROP FUNCTION、DROP EVENT、DROP INDEX、DROP PROCEDURE、DROP TABLE、DROP TRIGGER、DROP VIEW
-   RENAME TABLE、TRUNCATE TABLE

## 数据库账号的权限要求 {#section_b6v_ids_zln .section}

|数据库|权限要求|
|:--|:---|
|源POLARDB for MySQL|待迁移对象的读权限|
|目标POLARDB for MySQL|迁移对象的读写权限|

**说明：** 关于数据库账号的创建和授权方法，请参见[创建POLARDB数据库账号](https://help.aliyun.com/document_detail/68508.html)。

## 操作步骤 {#section_yc6_fkw_zyy .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标集群所属地域。 

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711733/156774846750439_zh-CN.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库和目标库连接信息。 

    ![配置源库和目标库连接信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1436590/156774846756870_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |任务名称|-|DTS会自动生成一个任务名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源库信息|实例类型|选择**POLARDB**。|
    |实例地区|选择源POLARDB集群所属的地域。|
    |POLARDB实例ID|选择源POLARDB集群ID。|
    |数据库账号|填入连接源POLARDB集群的数据库账号。|
    |数据库密码|填入该账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的信息是否正确。如果填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**POLARDB**。|
    |实例地区|选择目标POLARDB集群所属的地域。|
    |POLARDB实例ID|选择目标POLARDB集群ID。|
    |数据库账号|填入连接目标POLARDB集群的数据库账号。|
    |数据库密码|填入该账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的信息是否正确。如果填写正确则提示**测试通过**；如果提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

6.  完成上述配置后，单击页面右下角的**授权白名单并进入下一步**。 

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到源和目标POLARDB集群的白名单中，用于保障DTS服务器能够正常连接源和目标集群。

7.  选择迁移类型和迁移对象。 

    ![选择迁移对象和类型](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1436590/156774846756880_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，则同时勾选**结构迁移**和**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建MySQL数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。
 |
    |迁移对象| 在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156774846840698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 

    -   迁移对象选择的粒度可以为库、表、列三个粒度。
    -   默认情况下，迁移完成后，迁移对象的名称保持不变。如果您需要迁移对象在目标集群中名称不同，那么需要使用DTS提供的对象名映射功能，详情请参见[库表列映射](cn.zh-CN/用户指南/数据迁移/库表列映射.md#)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。
 |

8.  单击页面右下角的**预检查并启动**。 

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有通过预检查，DTS才能迁移数据。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156774846847468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
9.  预检查通过后，单击**下一步**。
10. 在弹出的购买配置确认对话框，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
11. 单击**购买并启动**，迁移任务正式开始。 
    -   结构迁移+全量数据迁移

        请勿手动结束迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

    -   结构迁移+全量数据迁移+增量数据迁移

        迁移任务不会自动结束，您需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至目标集群时。

        1.  观察迁移任务的进度变更为**增量迁移**，并显示为**无延迟**状态时，将源库停写几分钟，此时**增量迁移**的状态可能会显示延迟的时间。
        2.  等待迁移任务的**增量迁移**再次进入**无延迟**状态后，手动结束迁移任务。

            ![结束增量迁移任务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156774846847604_zh-CN.png)

12. 将业务切换至POLARDB集群。

由于用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除相关的数据库账号。


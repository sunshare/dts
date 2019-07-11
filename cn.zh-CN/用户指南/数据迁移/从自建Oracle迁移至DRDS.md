# 从自建Oracle迁移至DRDS {#concept_268503 .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将自建Oracle数据迁移至DRDS实例。DTS支持全量数据迁移以及增量数据迁移，同时使用这两种迁移类型可以实现在自建应用不停服的情况下，平滑地完成Oracle数据库的数据迁移工作。

## 源库支持的实例类型 {#section_6up_ipp_fa6 .section}

进行数据迁移操作的Oracle数据库支持以下实例类型。

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库

本文以**有公网IP的自建数据库**为例介绍配置流程，其他实例类型的自建Oracle数据库配置流程与该案例类似。

## 前提条件 {#section_97x_2gt_pl0 .section}

-   自建Oracle数据库的版本为10g、11g或12c版本。
-   自建Oracle数据库已开启Supplemental Logging，且要求supplemental\_log\_data\_pk，supplemental\_log\_data\_ui已开启，详情请参见[Supplemental Logging](https://docs.oracle.com/database/121/SUTIL/GUID-D857AF96-AC24-4CA1-B620-8EA3DF30D72E.htm#SUTIL1582)。
-   自建Oracle数据库已开启ARCHIVELOG（归档模式），设置合理的归档日志保持周期且归档日志能够被访问，详情请参见[ARCHIVELOG](https://docs.oracle.com/database/121/ADMIN/archredo.htm#ADMIN008)。
-   DRDS实例中用于创建数据库的RDS实例的存储空间须大于自建Oracle数据库占用的存储空间。
-   自建Oracle数据库的服务端口已开放至公网。

## 注意事项 {#section_opz_6ey_01c .section}

-   DTS在迁移Oracle至DRDS实例时，不支持结构迁移。

    **说明：** 结构迁移是指将源数据库中的结构对象定义（如表结构定义）迁移至目标数据库。

-   如果数据迁移的源实例没有主键或唯一约束，且记录的全字段没有唯一性，可能会出现重复数据。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例，请务必先终止或释放该任务，避免该任务被自动恢复，导致源端数据覆盖目标实例的数据。

## 费用说明 {#section_cz9_9ax_9kg .section}

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|不收取|

## 迁移类型说明 {#section_nco_yvv_nzk .section}

-   全量数据迁移

    DTS会将自建Oracle数据库迁移对象的存量数据，全部迁移到目标DRDS实例数据库中 。

    **说明：** 为保障数据一致性，全量数据迁移期间请勿在自建Oracle数据库中写入新的数据。

-   增量数据迁移

    DTS在全量数据迁移的基础上轮询并捕获自建Oracle数据库产生的redolog，将自建Oracle数据库的增量更新数据同步到目标DRDS数据库中。通过增量数据迁移可以实现在自建应用不停服的情况下，平滑地完成Oracle数据库迁移至DRDS数据库。

    **说明：** 增量数据迁移支持同步的SQL操作为INSERT、DELETE、UPDATE，不支持DDL同步。


## 迁移账号权限要求 {#section_j4v_ygd_2hz .section}

|迁移数据源|全量迁移|增量数据迁移|
|:----|:---|:-----|
|自建Oracle数据库|schema的owner权限|SYSDBA|
|DRDS实例|待迁入数据库的写权限|待迁入数据库的写权限|

**数据库账号创建及授权方法：**

-   自建Oracle数据库请参见[CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm)和[GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm)。
-   DRDS实例请参见[账号管理](https://help.aliyun.com/document_detail/98664.html)。

## 准备工作 {#section_swj_kfm_p0p .section}

根据自建Oracle数据库中待迁移数据表的数据结构，在目标DRDS实例中创建相应的数据库和数据表，详情请参见[创建数据库](https://help.aliyun.com/document_detail/52090.html)和[SQL基本操作](https://help.aliyun.com/document_detail/117763.html)。

由于Oracle和DRDS实例的数据类型并不是一一对应的，您需要在DRDS实例中定义对应的数据类型，具体如下表所示。

|Oracle数据类型|DRDS数据类型|DTS是否支持|
|:---------|:-------|:------|
|varchar2\(n \[char/byte\]\)|varchar\(n\)|支持|
|nvarchar2\[\(n\)\]|national varchar\[\(n\)\]|支持|
|char\[\(n \[byte/char\]\)\]|char\[\(n\)\]|支持|
|nchar\[\(n\)\]\]|national char\[\(n\)\]|支持|
|number\[\(p\[,s\]\)\]|decimal\[\(p\[,s\]\)\]|支持|
|float\(p\)\]|double|支持|
|long|longtext|支持|
|date|datetime|支持|
|binary\_float|decimal\(65,8\)|支持|
|binary\_double|double|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]|datetime\[\(fractional\_seconds\_precision\)\]|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|支持|
|clob|longtext|支持|
|nclob|longtext|支持|
|blob|longblob|支持|
|raw|varbinary\(2000\)|支持|
|long raw|longblob|支持|
|bfile|-|**不支持**|
|interval year\(year\_precision\) to mongth|-|**不支持**|
|interval day\(day\_precision\)tosecond\[\(fractional\_seconds\_precision\)\]|-|**不支持**|

**说明：** 

-   对于char类型，如果长度定义超过255，需要在DRDS中对应定义为varchar\(n\)。
-   由于DRDS实例的timestamp类型不包含时区，而Oracle的timestamp with time zone和timestamp with local time zone默认带有时区信息，DTS在迁移这两种类型的数据时，会将其转换成UTC时区后存入目标DRDS实例。

## 操作步骤 {#section_f49_bmt_87d .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711733/156282693250439_zh-CN.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![源库和目标库连接配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17108/156282693247826_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**有公网IP的自建数据库**。|
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 如果您的自建Oracle数据库进行了白名单安全设置，您需要在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入自建Oracle数据库的白名单安全设置中。

 |
    |数据库类型|选择**Oracle**。|
    |主机名或IP地址|填入自建Oracle数据库的访问地址，这个地址必须为公网地址。|
    |端口|填入自建Oracle数据库的服务端口，默认为**1521**。|
    |实例类型|     -   **非RAC实例**：选择该项后，您还需要填写**SID**信息。
    -   **RAC实例**：选择该项后，您还需要填写**ServiceName**信息。
 |
    |数据库账号|填入自建Oracle数据库的连接账号，权限要求请参见[迁移账号权限要求](#section_bjn_5zq_5hb)。|
    |数据库密码|填入自建Oracle数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**DRDS实例**。|
    |实例地区|选择目标DRDS实例所属地域。|
    |DRDS实例ID|选择目标DRDS实例ID。|
    |数据库账号|填入连接目标DRDS实例数据库的账号，权限要求请参见[迁移账号权限要求](#section_bjn_5zq_5hb)。|
    |数据库密码|填入连接目标DRDS实例数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标DRDS实例的白名单中，用于保障DTS服务器能够正常连接目标DRDS实例。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156282693247602_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，则勾选**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建Oracle数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**全量数据迁移**和**增量数据迁移**。
 |
    |迁移对象| 在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156282693240698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 

    -   迁移对象选择的粒度可以为库、表、列三个粒度。
    -   默认情况下，迁移完成后，迁移对象名跟自建Oracle数据库一致。如果您需要迁移对象在目标RDS实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。
 |

8.  单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156282693247468_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
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

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156282693247604_zh-CN.png)

12. 将业务切换至DRDS实例。

## 后续操作 {#section_vfb_ynm_ga3 .section}

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除自建Oracle数据库和DRDS实例中的数据库账号。


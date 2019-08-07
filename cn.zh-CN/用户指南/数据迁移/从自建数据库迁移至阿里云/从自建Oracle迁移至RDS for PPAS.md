# 从自建Oracle迁移至RDS for PPAS {#concept_268504 .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将自建Oracle数据迁移至RDS for PPAS实例。DTS支持结构迁移、全量数据迁移以及增量数据迁移，同时使用这三种迁移类型可以实现在本地应用不停服的情况下，平滑地完成Oracle数据库的数据迁移至RDS for PPAS。

## 源库支持的实例类型 {#section_d39_x9s_zvt .section}

进行数据迁移操作的Oracle数据库支持以下实例类型：

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库

本文以**有公网IP的自建数据库**为例介绍配置流程，其他实例类型的自建Oracle数据库配置流程与该案例类似。

## 前提条件 {#section_0rk_6kk_gmy .section}

-   自建Oracle数据库的版本为10g、11g或12c版本。

    **说明：** 建议选用10版本的RDS for PPAS以获取更高的兼容性。

-   自建Oracle数据库已开启Supplemental Logging，且要求supplemental\_log\_data\_pk，supplemental\_log\_data\_ui已开启，详情请参见[Supplemental Logging](https://docs.oracle.com/database/121/SUTIL/GUID-D857AF96-AC24-4CA1-B620-8EA3DF30D72E.htm#SUTIL1582)。
-   自建Oracle数据库已开启ARCHIVELOG（归档模式），设置合理的归档日志保持周期且归档日志能够被访问，详情请参见[ARCHIVELOG](https://docs.oracle.com/database/121/ADMIN/archredo.htm#ADMIN008)。
-   RDS for PPAS实例中用于创建数据库的RDS实例的存储空间须大于自建Oracle数据库占用的存储空间。
-   自建Oracle数据库的服务端口已开放至公网。

## 注意事项 {#section_qsw_r3s_5rl .section}

-   如果数据迁移的源实例没有主键或唯一约束，且记录的全字段没有唯一性，可能会出现重复数据。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例，请务必先终止或释放该任务，避免该任务被自动恢复，导致源端数据覆盖目标实例的数据。

## 数据迁移限制 {#section_5up_fo3_t9l .section}

-   结构迁移时，源库的reverse index和位图索引将被存储为RDS for PPAS的普通索引。
-   结构迁移时，源库的分区索引被转化为在RDS for PPAS的每个分区上创建独立的索引。
-   增量数据迁移只支持有主键，或有非空唯一索引的表。
-   增量数据迁移不支持long类型。
-   增量数据迁移过程中，不支持DDL操作。
-   不支持物化视图的迁移。

## 费用说明 {#section_2rq_4e0_dhz .section}

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|不收取|

## 迁移类型说明 {#section_s3e_ewk_rq8 .section}

-   结构迁移

    DTS将迁移对象的结构定义迁移到目标实例。目前DTS支持的对象包括：表、视图、同义词、触发器、存储过程、存储函数、包、自定义类型。

    **说明：** 为保障数据一致性，全量数据迁移期间请勿在自建Oracle数据库中写入新的数据。

-   全量数据迁移

    DTS会将自建Oracle数据库迁移对象的存量数据，全部迁移到目标RDS for PPAS实例数据库中 。

-   增量数据迁移

    DTS在全量数据迁移的基础上轮询并捕获自建Oracle数据库产生的redolog，将自建Oracle数据库的增量更新数据同步到目标RDS for PPAS实例数据库中。通过增量数据迁移可以实现在自建应用不停服的情况下，平滑地完成Oracle数据库迁移至RDS for PPAS实例。


## 迁移账号权限要求 {#section_1p6_06x_xey .section}

|迁移数据源|结构迁移|全量迁移|增量数据迁移|
|:----|:---|:---|:-----|
|自建Oracle数据库|schema的owner权限|schema的owner权限|SYSDBA|
|RDS for PPAS实例|schema的owner权限|schema的owner权限|schema的owner权限|

**数据库账号创建及授权方法：**

-   自建Oracle数据库请参见[CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm)和[GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm)。
-   RDS for PPAS实例请参见[创建账号](https://help.aliyun.com/document_detail/96933.html)。

## 数据类型映射关系 {#section_bhf_hqm_fi1 .section}

由于Oracle和PPAS的数据类型并不是一一对应的，所以DTS在进行结构迁移时，会根据数据类型定义进行类型映射，数据类型映射关系如下表所示。

|Oracle数据类型|PPAS数据类型|DTS是否支持|
|:---------|:-------|:------|
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
|bfile|-|**不支持**|
|interval year\(year\_precision\) to month|interval year to month|**不支持**|
|interval day\(day\_precision\) to second\[\(fractional\_seconds\_precision\)\]|interval day to second\[\(fractional\_seconds\_precision\)\]|**不支持**|

**说明：** 由于RDS for PPAS不支持timestamp\[\(fractional\_seconds\_precision\)\]with local time zone，DTS在迁移这种类型的数据时，会将其转换成UTC时区后存入目标RDS for PPAS的timestamp\[\(fractional\_seconds\_precision\)\]with time zone中。

## 操作步骤 {#section_j8n_qo1_7ay .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711733/156514035250439_zh-CN.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![源库和目标库连接配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156514035247598_zh-CN.png)

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
    |目标库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择目标RDS for PPAS实例所属地域。|
    |RDS实例ID|选择目标RDS for PPAS实例ID。|
    |数据库名称|填入待迁移的目标数据库名称。|
    |数据库账号|填入连接目标RDS for PPAS实例数据库的账号，权限要求请参见[迁移账号权限要求](#section_bjn_5zq_5hb)。|
    |数据库密码|填入连接目标RDS for PPAS实例数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标RDS for PPAS实例的白名单中，用于保障DTS服务器能够正常连接目标RDS实例。迁移完成后如不再需要可手动删除，详情请参见[设置白名单](https://help.aliyun.com/document_detail/43188.html)。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156514035247602_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，则同时勾选**结构迁移**和**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建Oracle数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。

**说明：** 

        -   增量数据迁移只支持有主键，或有非空唯一索引的表。
        -   增量数据迁移不支持long类型。
 |
    |迁移对象| 在迁移对象框中将想要迁移的数据库选中，单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156514035240698_zh-CN.png)移动到已选择对象框。

 **说明：** 

    -   迁移对象选择的粒度可以为库、表、列三个粒度。
    -   默认情况下，迁移完成后，迁移对象名跟自建Oracle数据库一致。如果您需要迁移对象在目标RDS实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。
 |

8.  单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156514035247468_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
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

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156514035247604_zh-CN.png)

12. 将业务切换至RDS for PPAS数据库。

## 后续操作 {#section_fhf_ysx_s3k .section}

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除自建Oracle数据库和RDS for PPAS实例中的数据库账号。


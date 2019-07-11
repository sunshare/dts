# 从自建PostgreSQL全量迁移至RDS for PostgreSQL {#concept_304058 .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将自建PostgreSQL全量迁移至RDS for PostgreSQL实例。DTS支持结构迁移、全量数据迁移和增量数据迁移，同时使用结构迁移和全量数据迁移可以实现自建PostgreSQL数据库的全量数据迁移。

## 源库支持的实例类型 {#section_bvz_aa6_faz .section}

执行数据迁移操作的PostgreSQL数据库支持以下实例类型：

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库

本文以**有公网IP的自建数据库**为例介绍全量数据迁移的配置流程。为保障数据一致性，全量数据迁移期间请勿在自建SQL Server数据库中写入新的数据。如果要实现不停机迁移，请参见[从自建PostgreSQL增量迁移至RDS for PostgreSQL](cn.zh-CN/用户指南/数据迁移/从自建PostgreSQL增量迁移至RDS for PostgreSQL.md#)。

## 前提条件 {#section_i7g_jgl_mom .section}

-   自建PostgreSQL数据库版本为9.2、9.3、9.4、9.5、9.6或10.0版本。
-   RDS for PostgreSQL实例的存储空间须大于自建PostgreSQL数据库占用的存储空间。
-   自建PostgreSQL数据库的服务端口已开放至公网。

## 注意事项 {#section_edb_qol_ib1 .section}

-   不支持迁移使用C语言编写的function。
-   一个数据迁移任务只能对一个数据库进行数据迁移，如果有多个数据库需要迁移，则需要为每个数据库创建数据迁移任务。
-   如果待迁移的数据库在目标RDS for PostgreSQL中不存在，DTS会自动创建。但是对于如下两种情况，您需要在配置迁移任务之前在目标RDS for PostgreSQL中[创建数据库](https://help.aliyun.com/document_detail/96758.html)。
    -   数据库名称不符合RDS定义规范。具体规范请参见[创建数据库](https://help.aliyun.com/document_detail/96758.html)。
    -   待迁移数据库在源PostgreSQL数据库与目标RDS for PostgreSQL实例中的名称不同。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例，请务必先终止或释放该任务，避免该任务被自动恢复后，导致源端数据覆盖目标实例的数据。

## 迁移类型说明 {#section_e30_p01_e7o .section}

-   结构迁移

    DTS将迁移对象的结构定义迁移到目标实例，目前DTS支持结构迁移的对象为table、trigger、view、sequence、function、user defined type、rule、domain、operation、aggregate。

-   全量数据迁移

    DTS会将自建PostgreSQL数据库迁移对象的存量数据，全部迁移到目标RDS for PostgreSQL数据库中。


## 费用说明 {#section_kik_j1d_uq9 .section}

|迁移类型|链路配置费用|公网流量费用|
|:---|:-----|:-----|
|全量数据迁移|不收取|不收取|

## 数据库账号的权限要求 {#section_0u5_y38_oi7 .section}

|迁移数据源|结构迁移|全量迁移|
|:----|:---|:---|
|自建PostgreSQL|pg\_catalog的usage权限|迁移对象的select权限|
|RDS for PostgreSQL|迁移对象的create、usage权限|schema的owner权限|

**数据库账号创建及授权方法：**

-   自建PostgreSQL数据库请参见[CREATE USER](https://www.postgresql.org/docs/10/sql-createuser.html)和[GRANT](https://www.postgresql.org/docs/10/sql-grant.html)语法。
-   RDS for PostgreSQL实例请参见[创建账号](https://help.aliyun.com/document_detail/96753.html)。

## 全量数据迁移流程 {#section_cft_5th_zts .section}

为解决对象间的依赖，提高迁移成功率，DTS对PostgreSQL结构和数据的迁移流程如下：

1.  进行Table、view、sequence、function、user defined type、rule、domain、operation、aggregate的结构迁移。
2.  进行全量数据迁移。
3.  进行trigger、foreign key的结构迁移。

## 操作步骤 {#section_5vz_1p4_b5n .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711733/156283371150439_zh-CN.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![源库和目标库连接配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249414/156283371247957_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**有公网IP的自建数据库**。|
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 如果您的自建PostgreSQL数据库进行了白名单安全设置，您需要在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入自建PostgreSQL数据库的白名单安全设置中。

 |
    |数据库类型|选择**PostgreSQL**。|
    |主机名或IP地址|填入自建PostgreSQL数据库的访问地址，本案例中填入公网地址。|
    |端口|填入自建PostgreSQL数据库的服务端口，默认为**5432**。|
    |数据库名称|填入自建PostgreSQL数据库中待迁移的数据库名。|
    |数据库账号|填入自建PostgreSQL数据库的连接账号，权限要求请参见[数据库账号的权限要求](#section_0u5_y38_oi7)。|
    |数据库密码|填入自建PostgreSQL数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择目标RDS实例所属地域。|
    |RDS实例ID|选择目标RDS实例ID。|
    |数据库名称|填入RDS实例中待迁入数据的目标数据库名，可以和自建PostgreSQL中待迁移的数据库名不同。|
    |数据库账号|填入连接目标RDS实例数据库的账号，权限要求请参见[数据库账号的权限要求](#section_0u5_y38_oi7)。|
    |数据库密码|填入连接目标RDS实例数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标RDS实例的白名单中，用于保障DTS服务器能够正常连接目标RDS实例。迁移完成后如不再需要可手动删除，详情请参见[设置白名单](https://help.aliyun.com/document_detail/43187.html)。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/249414/156283371247963_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量数据迁移，则同时勾选**结构迁移**和**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建PostgreSQL数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。
 本案例为全量数据迁移，迁移类型中勾选**结构迁移**和**全量数据迁移**。

 |
    |迁移对象| 在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156283371240698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 

    -   迁移对象选择的粒度可以为库、表、列三个粒度。
    -   默认情况下，迁移完成后，迁移对象名跟自建PostgreSQL数据库一致。如果您需要迁移对象在目标RDS实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。
 |

8.  单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156283371247468_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
9.  预检查通过后，单击**下一步**。
10. 在购买配置确认页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
11. 单击**购买并启动**，迁移任务正式开始。

    **说明：** 请勿手动结束迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

12. 将业务切换至RDS实例。

## 后续操作 {#section_jb5_zfl_29k .section}

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除自建PostgreSQL数据库和RDS for PostgreSQL实例中的数据库账号。


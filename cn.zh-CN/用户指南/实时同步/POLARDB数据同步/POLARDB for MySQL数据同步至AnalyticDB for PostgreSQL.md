# POLARDB for MySQL数据同步至AnalyticDB for PostgreSQL {#concept_263148 .concept}

[数据传输服务](https://dts.console.aliyun.com/)（Data Transmission Service，简称DTS）支持将POLARDB for MySQL实例中的数据同步至AnalyticDB for PostgreSQL。通过DTS提供的数据同步功能，可以轻松实现数据的流转，将企业数据集中分析。

## 前提条件 {#section_ohv_35z_nhb .section}

-   数据同步的源POLARDB实例的数据库类型为**MySQL**，数据库引擎版本为**5.6**。
-   源库中待同步的数据表，必须有主键。
-   数据同步的目标AnalyticDB for PostgreSQL实例已存在，如不存在请[创建AnalyticDB for PostgreSQL实例](https://help.aliyun.com/document_detail/50200.html)。

## 数据同步功能限制 {#section_dwq_cwz_nhb .section}

-   同步对象仅支持数据表，暂不支持非数据表的对象。
-   暂不支持结构迁移功能。
-   不支持JSON、GEOMETRY、CURVE、SURFACE、MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、GEOMETRYCOLLECTION类型的数据同步。

## 支持的同步语法 {#section_czl_s5z_nhb .section}

-   DML操作：INSERT、UPDATE、DELETE。
-   DDL操作：ALTER TABLE、ADD COLUMN、DROP COLUMN、RENAME COLUMN。

    **说明：** 不支持CREATE TABLE和DROP TABLE操作。如您需要新增表，则需要通过修改同步对象操作来新增对应表的同步，详情请参考[新增同步对象](https://help.aliyun.com/document_detail/26634.html)。


## 支持的同步架构 {#section_yy4_q5z_nhb .section}

-   1对1单向同步。
-   1对多单向同步。
-   多对1单向同步。

## 术语/概念对应关系 {#section_gl3_vfr_3xz .section}

|POLARDB for MySQL中的术语/概念|AnalyticDB for PostgreSQL中的术语/概念|
|:-----------------------|:-------------------------------|
|Database|Schema|
|Table|Table|

## 数据同步前准备工作 {#section_b5t_tv4_rhb .section}

开启POLARDB实例的binlog功能，详情请参考[如何开启binlog](https://help.aliyun.com/document_detail/113546.html)。

## 操作步骤一 在目标实例中创建对应的数据结构 {#section_hqy_lbs_mqm .section}

根据源RDS实例中待迁移数据表的数据结构，在目标AnalyticDB for PostgreSQL实例中创建数据库、Schema及数据表，详情请参考[AnalyticDB for PostgreSQL基础操作](https://help.aliyun.com/document_detail/35427.html)。

## 操作步骤二 购买数据同步实例 {#section_icf_frm_4hb .section}

1.  登录[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据同步**。
3.  在页面右上角，单击**创建同步作业**。
4.  在数据传输服务购买页面，选择付费类型为**预付费**或**按量付费**。
    -   预付费：属于预付费，即在新建实例时需要支付费用。适合长期需求，价格比按量付费更实惠，且购买时长越长，折扣越多。
    -   按量付费：属于后付费，即按小时扣费。适合短期需求，用完可立即释放实例，节省费用。
5.  选择数据同步实例的参数配置信息，参数说明如下表所示。

    |参数配置区|参数项|说明|
    |:----|:--|:-|
    |基本配置|功能|选择**数据同步**。|
    |源实例|选择**MySQL**。|
    |源实例地域|选择数据同步链路中源POLARDB实例所属地域。 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |目标实例|选择**AnalyticDB for PostgreSQL**。|
    |目标实例地域|选择数据同步链路中目标AnalyticDB for PostgreSQL实例所属地域。 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |同步拓扑|数据同步支持的拓扑类型，选择**单向同步**。 **说明：** 当前仅支持单向同步。

 |
    |网络类型|数据同步服务使用的网络类型，目前固定为**专线**。|
    |同步链路规格|数据传输为您提供了不同性能的链路规格，以同步的记录数为衡量标准。详情请参考[数据同步规格说明](https://help.aliyun.com/document_detail/26605.html)。 **说明：** 建议生产环境选择**small**及以上规格。

 |
    |购买量|购买数量|一次性购买数据同步实例的数量，默认为**1**，如果购买的是按量付费实例，一次最多购买9条链路。|

6.  单击**立即购买**，根据提示完成支付流程。

## 操作步骤三 配置数据同步 {#section_dz3_xsm_4hb .section}

1.  登录[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据同步**。
3.  定位至已购买的数据同步实例，单击**配置同步链路**。
4.  配置同步通道的源实例及目标实例信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217462/155780020046871_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**通过专线/VPN网关/智能网关接入的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源POLARDB实例的地域信息，不可变更。|
    |对端专有网络|选择POLARDB实例所属的VPC ID。 **说明：** 您可以通过POLARDB控制台的基本信息页面获取VPCID。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217462/155780020046873_zh-CN.png)

 |
    |数据库类型|固定为**MySQL**，不可变更。|
    |IP地址|填入POLARDB实例的私网IP地址。 **说明：** 您可以在ECS或本地设备中，通过ping该POLARDB集群的**主地址（私网）**获取IP地址。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217462/155780020046870_zh-CN.png)

 |
    |端口|填入POLARDB实例的监听端口，默认为3306。|
    |数据库账号|填入POLARDB的数据库账号。|
    |数据库密码|填入POLARDB数据库账号对应的密码。|
    |目标实例信息|实例类型|固定为**AnalyticDB forPostgreSQL**，无需设置。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择作为数据同步目标的AnalyticDB for PostgreSQL实例ID。|
    |数据库名称|填入目标AnalyticDB for PostgreSQL实例中待同步的目标表所属的数据库名称。|
    |数据库账号|填入目标AnalyticDB for PostgreSQL实例的数据库账号。 **说明：** 数据库账号须具备SELECT、INSERT、UPDATE、DELETE、COPY、TRUNCATE、ALTERTABLE权限。

 |
    |数据库密码|填入数据库账号对应的密码。|

5.  单击页面右下角的**授权白名单并进入下一步**。
6.  配置同步策略及对象信息。

    ![配置同步策略和对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217462/155780020046872_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |同步策略配置|同步初始化|选择**全量数据初始化**。 **说明：** 将源实例中已经存在同步对象的数据在目标实例中初始化，作为后续增量同步数据的基线数据。

 |
    |目标已存在表的处理模式|     -   **预检查检测并拦截**（默认勾选）

在预检查阶段执行**目标表是否为空**的检查项目，如果有数据直接在预检查的目标表是否为空的检查项中检测并拦截报错。

    -   **清空目标表的数据** 

在预检查阶段跳过**目标表是否为空**的检查项目。全量初始化之前将目标表的数据清空。适用于完成同步任务测试后的正式同步场景。

    -   **不做任何操作** 

在预检查阶段跳过**目标表是否为空**的检查项目。全量初始化时直接追加迁移数据。适用于多张表同步到一张表的汇总同步场景。

 |
    |同步操作类型|     -   **Insert**
    -   **Update**
    -   **Delete**
    -   **AlterTable**
 **说明：** 根据业务需求选择数据同步的操作类型。

 |
    |选择同步对象|-|同步对象的选择粒度为表。 如果需要目标表中列信息与源表不同，则需要使用DTS的字段映射功能，详情请参考[库表列映射](https://help.aliyun.com/document_detail/26628.html)。

 **说明：** 不支持CREATE TABLE操作，您需要通过修改同步对象操作来新增对应表的同步，详情请参考[新增同步对象](https://help.aliyun.com/document_detail/26634.html)。

 |

7.  上述配置完成后单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步任务。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/188230/155780020045708_zh-CN.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
8.  在预检查对话框中显示**预检查通过**后，关闭预检查对话框，该同步作业的同步任务正式开始。
9.  等待该同步作业的链路初始化完成，直至状态处于**同步中**。

    您可以在 数据同步页面，查看数据同步状态。

    ![数据同步状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217462/155780020046874_zh-CN.png)



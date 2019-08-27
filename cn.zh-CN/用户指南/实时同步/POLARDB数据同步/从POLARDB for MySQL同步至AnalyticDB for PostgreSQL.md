# 从POLARDB for MySQL同步至AnalyticDB for PostgreSQL {#concept_263148 .task}

分析型数据库PostgreSQL版（原HybridDB for PostgreSQL）为您提供简单、快速、经济高效的PB级云端数据仓库解决方案。通过数据传输服务DTS（Data Transmission Service），您可以将POLARDB for MySQL同步至AnalyticDB for PostgreSQL，帮助您快速实现对海量数据的即席查询分析、ETL处理和可视化探索。

-   源POLARDB for MySQL实例已开启Binlog，详情请参见[如何开启Binlog](https://help.aliyun.com/document_detail/113546.html)。
-   已购买目标AnalyticDB for PostgreSQL实例，详情请参见[创建AnalyticDB for PostgreSQL实例](https://help.aliyun.com/document_detail/50200.html)。

## 注意事项 {#section_vw1_0b5_f7g .section}

-   全量初始化过程中，并发INSERT会导致目标实例的表碎片，全量初始化完成后，目标实例的表空间比源集群的表空间大。
-   如果数据同步的源实例没有主键或唯一约束，且记录的全字段没有唯一性，可能会出现重复数据。
-   在同步的过程中，如果在源库中创建一个表，要将其作为同步对象，那么您需要为数据同步作业[新增同步对象](cn.zh-CN/用户指南/实时同步/新增同步对象.md#)。

## 功能限制 {#section_dwq_cwz_nhb .section}

-   仅支持表级别的数据同步。
-   不支持结构同步，详情请参见[名词解释](../../../../cn.zh-CN/产品简介/名词解释.md#)。
-   不支持同步JSON、GEOMETRY、CURVE、SURFACE、MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、GEOMETRYCOLLECTION类型的数据。

## 支持同步的SQL操作 {#section_czl_s5z_nhb .section}

-   DML操作：INSERT、UPDATE、DELETE。
-   DDL操作：ALTER TABLE、ADD COLUMN、DROP COLUMN、RENAME COLUMN。

## 支持的同步架构 {#section_yy4_q5z_nhb .section}

-   1对1单向同步。
-   1对多单向同步。
-   多对1单向同步。

关于各类同步架构的介绍及注意事项，请参见[数据同步拓扑介绍](cn.zh-CN/用户指南/实时同步/数据同步拓扑介绍.md#)。

## 术语/概念对应关系 {#section_gl3_vfr_3xz .section}

|POLARDB for MySQL|AnalyticDB for PostgreSQL|
|:----------------|:------------------------|
|Database|Schema|
|Table|Table|

## 操作步骤一 在目标实例中创建对应的数据结构 {#section_hqy_lbs_mqm .section}

根据源RDS实例中待迁移表的数据结构，在目标AnalyticDB for PostgreSQL实例中创建数据库、Schema及数据表，详情请参考[AnalyticDB for PostgreSQL基础操作](https://help.aliyun.com/document_detail/35427.html)。

## 操作步骤二 配置数据同步 {#section_dz3_xsm_4hb .section}

1.  [购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。 

    **说明：** 购买时，选择源实例为**MySQL**、目标实例为**AnalyticDB for PostgreSQL**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。 

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156689045450604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置同步通道的源实例及目标实例信息。 

    ![配置源实例和目标实例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217462/156689045446871_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**通过专线/VPN网关/智能网关接入的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源POLARDB实例的地域信息，不可变更。|
    |对端专有网络|选择POLARDB实例所属的VPC ID。 您可以登录[POLARDB控制台](https://polardb.console.aliyun.com/)，单击目标实例ID，进入该实例的**基本信息**页面来获取。

 ![获取VPC Id](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1227086/156689045454382_zh-CN.png)

|
    |数据库类型|固定为**MySQL**，不可变更。|
    |IP地址|配置POLARDB主实例的私网IP地址，本案例中填入**172.16.20.36**。 您可以在电脑中ping目标POLARDB实例的**主地址（私网）**来获取私网IP地址。

 ![获取POLARDB的私网IP地址](images/54390_zh-CN.gif)

|
    |端口|填入POLARDB实例的服务端口，默认为**3306**。|
    |数据库账号|填入连接POLARDB实例的数据库账号。 **说明：** 该账号需具备待同步对象的SELECT、REPLICATION CLIENT、REPLICATION SLAVE权限。

 |
    |数据库密码|填入POLARDB数据库账号对应的密码。|
    |目标实例信息|实例类型|固定为**AnalyticDB for PostgreSQL**，无需设置。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择作为数据同步目标的AnalyticDB for PostgreSQL实例ID。|
    |数据库名称|填入目标AnalyticDB for PostgreSQL实例中，待同步的目标表所属的数据库名称。|
    |数据库账号|填入目标AnalyticDB for PostgreSQL实例的数据库账号。 **说明：** 用于数据同步的数据库账号需具备目标同步对象的ALL权限。

 |
    |数据库密码|填入数据库账号对应的密码。|

7.  单击页面右下角的**授权白名单并进入下一步**。 

    **说明：** 此步骤会将DTS服务器的IP地址自动添加POLARDB for MySQL和AnalyticDB for PostgreSQL的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  配置同步策略及对象信息。 

    ![配置同步策略和对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217462/156689045446872_zh-CN.png)

    |类别项目|选项|说明|
    |:---|:-|:-|
    |同步策略配置|同步初始化|选择**全量数据初始化**。 **说明：** 将源实例中已经存在同步对象的数据在目标实例中初始化，作为后续增量同步数据的基线数据。

 |
    |目标已存在表的处理模式|     -   **预检查检测并拦截**（默认勾选）

在预检查阶段执行**目标表是否为空**的检查项目，如果有数据直接在预检查的目标表是否为空的检查项中检测并拦截报错。

    -   **清空目标表的数据** 

在预检查阶段跳过**目标表是否为空**的检查项目。全量初始化之前将目标表的数据清空。适用于完成同步任务测试后的正式同步场景。

    -   **不做任何操作** 

在预检查阶段跳过**目标表是否为空**的检查项目。全量初始化时直接追加迁移数据。适用于多张表同步到一张表的汇总同步场景。

 |
    |同步操作类型| 根据业务需求选择需要同步的操作类型：

     -   **Insert**
    -   **Update**
    -   **Delete**
    -   **Alter Table**
 |
    |选择同步对象|-|同步对象的选择粒度为表。 如果需要目标表中列信息与源表不同，则需要使用DTS的字段映射功能，详情请参见[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)。

 **说明：** 不支持CREATE TABLE操作。在同步的过程中，如果在源库中创建一个表，要将其作为同步对象，那么您需要为数据同步作业[新增同步对象](cn.zh-CN/用户指南/实时同步/新增同步对象.md#)。

 |

9.  上述配置完成后，单击页面右下角的**预检查并启动**。 

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156689045547468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
10. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
11. 等待同步作业的链路初始化完成，直至处于**同步中**状态。 

    您可以在 数据同步页面，查看数据同步作业的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156689045541059_zh-CN.png)



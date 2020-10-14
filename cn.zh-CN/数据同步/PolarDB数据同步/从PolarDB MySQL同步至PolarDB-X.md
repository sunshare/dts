# 从PolarDB MySQL同步至PolarDB-X

云原生分布式数据库PolarDB-X（简称PolarDB-X）是阿里巴巴致力于解决单机数据库服务瓶颈而自主研发的分布式数据库产品，高度兼容MySQL协议和语法，支持自动化水平拆分、在线平滑扩缩容、弹性扩展、透明读写分离，具备数据库全生命周期运维管控能力。通过数据传输服务DTS（Data Transmission Service），您可以将PolarDB MySQL同步至PolarDB-X。

## 前提条件

-   PolarDB MySQL已开启Binlog，详情请参见[如何开启Binlog](https://help.aliyun.com/document_detail/113546.html)。
-   源库中待同步的数据表必须具备主键。
-   确保目标库具备充足的存储空间。
-   已创建分布式关系型数据库服务PolarDB-X，如未创建请参见[创建实例]()和[创建数据库]()。

    **说明：** 创建实例时需选择存储类型为RDS MySQL。


## 优惠活动

[DTS优惠活动，最低0折](/cn.zh-CN/产品定价/产品定价.md)

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据同步前评估源库和目标库的性能，同时建议您在业务低峰期执行数据同步（例如源库和目标库的CPU负载在30%以下）。
-   全量初始化过程中，并发INSERT会导致目标集群的表碎片，全量初始化完成后，目标集群的表空间比源库的表空间大。确保目标库具备充足的存储空间。
-   MySQL同步至PolarDB-X暂不支持结构初始化，在配置同步作业前，您需要在目标实例中创建对应的库和表。

## 同步限制

-   同步对象仅支持数据表。
-   不支持BIT、VARBIT、GEOMETRY、ARRAY、UUID、TSQUERY、TSVECTOR、TXID\_SNAPSHOT类型的数据同步。
-   暂不支持同步前缀索引，如果源库存在前缀索引可能导致数据同步失败。
-   在数据同步时，请勿对源库的同步对象使用gh-ost或pt-online-schema-change等类似工具执行在线DDL变更，否则会导致同步失败。

## 支持同步的SQL操作

INSERT、UPDATE、DELETE。

## 数据库账号的权限要求

|数据库|所需权限|
|---|----|
|PolarDB MySQL|REPLICATION CLIENT、REPLICATION SLAVE、SHOW VIEW和所有同步对象的SELECT权限。|
|PolarDB-X|无需填写数据库账号信息，DTS会自动创建账号并授权。|

## 支持的同步架构

-   1对1单向同步。
-   多对1单向同步。

## 准备工作

由于PolarDB MySQL同步至PolarDB-X暂不支持**结构初始化**，所以您需要根据源PloarDB MySQL集群中待同步对象的数据结构，在目标实例中创建相应的数据库和数据表，详情请参见[创建数据库](https://help.aliyun.com/document_detail/52090.html)和[SQL基本操作](https://help.aliyun.com/document_detail/117763.html)。

**说明：** **结构初始化**：将源库中待同步对象的结构定义信息，同步至目标库中。

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程](/cn.zh-CN/快速入门/购买流程.md)。

    **说明：** 购买时，选择源集群为**PolarDB**、目标实例为**PolarDB-X（原DRDS升级版）**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置同步作业的源集群及目标实例信息。

    ![配置源和目标实例](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3515502061/p171519.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|选择**PolarDB实例**。|
    |实例地区|购买数据同步实例时选择的源集群地域信息，不可变更。|
    |PolarDB实例ID|选择源PolarDB集群ID。|
    |数据库账号|填入PolarDB集群的数据库账号，权限要求请参见[数据库账号的权限要求](#section_du0_x62_x24)。|
    |数据库密码|填入该数据库账号对应的密码。|
    |目标实例信息|实例类型|固定为**DRDS实例**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |DRDS实例ID|选择目标PolarDB-X（原DRDS升级版）实例ID。|

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到源集群和目标实例的白名单中，用于保障DTS服务器能够正常连接源集群和目标实例。

8.  配置同步策略及对象信息。

    ![配置同步对象](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3697791061/p171518.png)

    |配置项目|配置说明|
    |:---|:---|
    |选择同步对象|在源库对象框中单击待同步的表，然后单击![向右小箭头](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)图标将其移动至已选择对象框。

**说明：**

    -   同步对象的选择粒度为表。
    -   默认情况下，同步对象的名称保持不变。如果您需要改变同步对象在目标实例中的名称，那么需要使用DTS提供的对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](/cn.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md)。 |

9.  单击**下一步**。

10. 选择是否要执行全量数据初始化。

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4697791061/p60676.png)

    **说明：** **全量数据初始化**：DTS将源库中待同步表的存量数据同步至目标库中，如果不选择则不同步存量数据。

11. 单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复问题后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。

13. 等待同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在数据同步页面，查看数据同步作业的状态。

    ![查看同步作业状态](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1349459951/p41059.png)



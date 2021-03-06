# 从RDS MySQL同步至自建Kafka集群

Kafka是应用较为广泛的分布式、高吞吐量、高可扩展性消息队列服务，普遍用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域，是大数据生态中不可或缺的产品之一。通过数据传输服务DTS（Data Transmission Service），您可以将RDS MySQL的数据库同步至自建Kafka集群，扩展消息处理能力。

-   Kafka集群的版本为0.10.1.0-1.0.2版本。
-   已创建RDS MySQL实例，详情请参见[创建RDS MySQL实例](/intl.zh-CN/RDS MySQL 数据库/快速入门/创建RDS MySQL实例.md)。

## 注意事项

-   DTS在执行全量数据初始化时将占用源库和目标库一定的读写资源，可能会导致数据库的负载上升，在数据库性能较差、规格较低或业务量较大的情况下（例如源库有大量慢SQL、存在无主键表或目标库存在死锁等），可能会加重数据库压力，甚至导致数据库服务不可用。因此您需要在执行数据同步前评估源库和目标库的性能，同时建议您在业务低峰期执行数据同步（例如源库和目标库的CPU负载在30%以下）。
-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。

## 功能限制

-   同步对象仅支持数据表，不支持非数据表的对象。
-   不支持自动调整同步对象，如果对同步对象中的数据表进行重命名操作，且重命名后的名称不在同步对象中，那么这部分数据将不再同步到到目标Kafka集群中。如需将修改后的数据表继续数据同步至目标Kafka集群中，您需要进行**修改同步对象**操作，详情请参见[新增同步对象](/intl.zh-CN/数据同步/同步作业管理/新增同步对象.md)。

## 支持的同步架构

-   1对1单向同步。
-   1对多单向同步。
-   多对1单向同步。
-   级联单向同步。

## 消息格式

同步到Kafka集群中的数据以avro格式存储，schema定义详情请参见[DTS avro schema定义](https://github.com/LioRoger/subscribe_example/tree/master/avro)。

**说明：** 数据同步到Kafka集群后，您需要根据avro schema定义进行数据解析。

## 操作步骤

1.  购买数据同步作业，详情请参见[购买流程](/intl.zh-CN/快速入门/购买流程.md)。

    **说明：** 购买时，选择源实例为**MySQL**、目标实例为**Kafka**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts-intl.console.aliyun.com/)。

3.  在左侧导航栏，单击**数据同步**。

4.  在同步作业列表页面顶部，选择同步的目标实例所属地域。

    ![选择地域](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7349459951/p50604.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。

6.  配置同步作业的源实例及目标实例信息。

    ![同步通道的源和目标实例配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7720649951/p39867.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |无|同步作业名称|DTS会自动生成一个同步作业名称，建议配置具有业务意义的名称（无唯一性要求），便于后续识别。|
    |源实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域，不可变更。|
    |实例ID|选择源RDS实例的实例ID。|
    |数据库账号|填入连接源RDS实例数据库的账号，需要具备REPLICATION CLIENT、REPLICATION SLAVE、SHOW VIEW和所有同步对象的SELECT权限。|
    |数据库密码|填入连接源RDS实例数据库账号的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**。如果设置为**SSL安全连接**，您需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://www.alibabacloud.com/help/zh/doc-detail/96120.htm)。 **说明：** 目前仅中国内地及中国香港地域支持设置**连接方式**。 |
    |目标实例信息|实例类型|根据Kafka集群的部署位置选择，本文以**ECS上的自建数据库**为例介绍配置流程。**说明：** 当选择为其他实例类型时，您还需要执行相应的准备工作，详情请参见[准备工作概览](/intl.zh-CN/准备工作/准备工作概览.md)。 |
    |实例地区|购买数据同步实例时选择的目标实例地域，不可变更。|
    |ECS实例ID|选择部署了Kafka集群的ECS实例ID。|
    |数据库类型|固定为**Kafka**。|
    |端口|填入Kafka集群提供服务的端口，默认为9092。|
    |数据库账号|填入Kafka集群的用户名，如Kafka集群未开启验证可不填写。|
    |数据库密码|填入Kafka集群用户名的密码，如Kafka集群未开启验证可不填写。|
    |Topic|单击右侧的**获取Topic列表**，然后在下拉框中选择目标Topic。|
    |Kafka版本|选择目标Kafka集群的版本。|
    |连接方式|根据业务及安全需求，选择**非加密连接**或**SCRAM-SHA-256**。|

7.  单击页面右下角的**授权白名单并进入下一步**。

8.  配置同步对象信息。

    ![配置同步对象](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4030649951/p39868.png)

    |配置|说明|
    |--|--|
    |同步对象|在**源库对象**区域框中，选择需要同步的对象（选择的粒度为表），然后单击![向右箭头](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8502659951/p40698.png)图标将其移动到**已选对象**区域框中。**说明：** DTS会自动将表名映射为[步骤6](#step_2ys_eus_xmy)选择的Topic名称。如果需要更换同步的目标Topic，您需要使用库表列名映射功能，详情请参见[设置同步对象在目标实例中的名称](/intl.zh-CN/数据同步/同步作业管理/设置同步对象在目标实例中的名称.md) 。 |

9.  上述配置完成后单击页面右下角的**下一步**。

10. 配置同步初始化的高级配置信息。

    ![Kafka同步初始化高级配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4030649951/p87942.png)

    |配置|说明|
    |--|--|
    |**同步初始化**|默认选择**结构初始化**和**全量数据初始化**，DTS会在增量数据同步之前，将源库中待同步对象的结构和存量数据，同步到目标库。|
    |**过滤选项**|默认选择**忽略增量同步阶段的 DDL**，即增量同步阶段源库执行的DDL操作不会被DTS同步至目标库。|

11. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：**

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8502659951/p47468.png)图标，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，数据同步作业正式开始。

    您可以在数据同步页面，查看数据同步状态。

    ![查看数据同步状态](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4030649951/p39871.png)



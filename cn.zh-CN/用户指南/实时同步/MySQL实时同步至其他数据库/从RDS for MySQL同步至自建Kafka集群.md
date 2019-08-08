# 从RDS for MySQL同步至自建Kafka集群 {#concept_nds_3dj_ygb .concept}

Kafka是应用较为广泛的分布式、高吞吐量、高可扩展性消息队列服务，普遍用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域，是大数据生态中不可或缺的产品之一。使用[数据传输服务（Data Transmission Service）](https://dts.console.aliyun.com/)（简称DTS）的数据同步功能，您可以将RDS for MySQL的数据库同步至自建Kafka集群，扩展消息处理能力。

## 前提条件 {#section_dr3_s2j_ygb .section}

-   Kafka集群的版本为0.10或1.0版本。
-   源RDS实例的数据库类型为**MySQL**。
-   确保数据同步的源RDS实例已存在，如不存在请创建[RDS实例](https://help.aliyun.com/document_detail/26117.html)。

## 注意事项 {#section_tpt_t2g_vo8 .section}

如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。

## 数据同步功能限制 {#section_ifr_sfd_zgb .section}

-   同步对象仅支持数据表，不支持非数据表的对象。
-   不支持DDL操作的数据同步。
-   不支持自动调整同步对象。

    **说明：** 如果对同步对象中的数据表进行重命名操作，且重命名后的名称不在同步对象中，那么这部分数据将不再同步到到目标Kafka集群中。如需将修改后的数据表继续数据同步至目标Kafka集群中，您需要进行**修改同步对象**操作，详情请参考[修改同步对象](https://help.aliyun.com/document_detail/26634.html)。


## 支持同步的SQL操作 {#section_yf3_2fj_ygb .section}

DML操作：Insert、Update、Delete、Replace。

## 支持的同步架构 {#section_jhp_hfj_ygb .section}

-   1对1单向同步。
-   1对多单向同步。
-   多对1单向同步。
-   级联同步。

## 消息格式 {#section_y13_zp2_zgb .section}

同步到Kafka集群中的数据以avro格式存储，schema定义详情请参考[DTS avro schema定义](https://github.com/LioRoger/subscribe_example/tree/master/avro)。

在数据同步到Kafka集群后，您需要根据avro schema定义进行数据解析。

## 费用说明 {#section_evt_ttd_zgb .section}

详情请参考[产品定价](https://help.aliyun.com/document_detail/90770.html)。

## 操作步骤一 购买数据同步实例 {#section_yvc_xgj_ygb .section}

1.  登录[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据同步**。
3.  在页面右上角，单击**创建同步作业**。
4.  购买数据同步实例，参数说明如下表所示。

    ![购买数据同步实例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134337/156523540039866_zh-CN.png)

    |参数配置区|参数项|说明|
    |:----|:--|:-|
    |基本配置|功能|选择**数据同步**。|
    |源实例|选择**MySQL**。|
    |源实例地域|选择数据同步链路中源RDS实例的地域。|
    |目标实例|选择**Kafka**。|
    |目标实例地域|选择数据同步链路中目标Kafka集群的地域。 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |同步拓扑|数据同步支持的拓扑类型，MySQL同步至Kafka仅支持单向同步。|
    |网络类型|数据同步服务使用的网络类型，目前仅支持专线。|
    |同步链路规格|数据传输为您提供了不同性能的链路规格，以同步的记录数为衡量标准。详情请参考[数据同步规格说明](https://help.aliyun.com/document_detail/26605.html)。|
    |购买量|购买数量|购买数据同步实例的数量，默认为1。|

5.  单击**立即购买**，根据提示完成支付流程。

## 操作步骤二 配置数据同步实例 {#section_v5h_m5c_zgb .section}

1.  登录[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据同步**。
3.  定位至已购买的数据同步实例，单击**配置同步链路**。
4.  配置同步通道的源实例及目标实例信息。

    ![同步通道的源和目标实例配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134337/156523540039867_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择源RDS实例的实例ID。|
    |数据库账号|填入连接源RDS实例数据库的账号，需要具备 Replication slave, Replication client 及所有同步对象的 Select 权限。|
    |数据库密码|填入连接源RDS实例数据库账号对应的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**，本案例选择为**非加密连接**。 **说明：** 选择**SSL安全连接**时，需要提前开启RDS实例的SSL加密功能，详情请参考[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。

 |
    |目标实例信息|实例类型|     -   Kafka集群部署在ECS上时，选择**ECS上的自建数据库**
    -   Kafka集群部署在本地服务器时，选择**通过专线/VPN网关/智能网关接入的自建数据库**。

**说明：** 选择**通过专线/VPN网关/智能网关接入的自建数据库**时，您需要配置**VPC ID**、**IP地址**和**端口**。

 |
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |ECS实例ID|选择部署了Kafka集群的ECS实例ID。|
    |数据库类型|选择为**Kafka**。|
    |端口|Kafka集群对外提供服务的端口，默认为9092。|
    |数据库账号|填入Kafka集群的用户名，如Kafka集群未开启验证可不填写。|
    |数据库密码|填入Kafka集群用户名对应的密码，如Kafka集群未开启验证可不填写。|
    |Topic|     1.  单击击右侧的**获取Topic列表**。
    2.  下拉选择具体的Topic名称。
 |
    |Kafka版本|根据目标Kafka集群版本，选择对应的版本信息。|

5.  单击页面右下角的**授权白名单并进入下一步**。
6.  配置同步对象信息。

    ![配置同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134337/156523540039868_zh-CN.png)

    **说明：** 

    -   同步对象的粒度为表级别。
    -   在源库对象区域框中，选择需要同步的数据表，单击[![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75938/154823638433712_zh-CN.png)](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/75938/154823638433712_zh-CN.png)移动到已选对象区域框中。
    -   默认情况下，对象迁移到Kafka集群后，对象名与RDS数据表一致。如果您迁移的对象在源数据库跟目标实例上名称不同，那么需要使用DTS提供的对象名映射功能，使用方法请参考[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
7.  上述配置完成后单击页面右下角的**下一步**。
8.  配置同步初始化的高级配置信息。

    ![配置同步初始化](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134337/156523540039869_zh-CN.png)

    **说明：** 

    -   此步骤会将源实例中已经存在同步对象的结构及数据在目标实例中初始化，作为后续增量同步数据的基线数据。
    -   同步初始化类型细分为：结构初始化，全量数据初始化。默认情况下，需要选择**结构初始化**和**全量数据初始化**。
9.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步任务。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134337/156523540039870_zh-CN.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
10. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，数据同步任务正式开始。

    您可以在数据同步页面，查看数据同步状态。

    ![查看数据同步状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/134337/156523540139871_zh-CN.png)



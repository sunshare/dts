# 创建Oracle数据订阅通道 {#concept_byv_frc_bhb .concept}

实时数据订阅功能旨在帮助用户获取RDS、DRDS和Oracle的实时增量数据，用户能够根据自身业务需求自由消费增量数据，例如实现**缓存更新策略**、**业务异步解耦**、**异构数据源数据实时同步**及**含复杂ETL的数据实时同步**等多种业务场景。

DTS提供了增量数据订阅功能，要订阅消费增量数据，需要进行如下两个操作步骤：

1.  在DTS控制台创建Oracle数据库的订阅通道；
2.  使用Kafka Connector连接这个数据订阅的消费通道，订阅并消费增量数据。

本章节主要介绍在DTS控制台创建Oracle订阅通道的流程。

## 前提条件 {#section_sbn_jml_zgb .section}

-   Oracle数据库状态为运行中；
-   支持数据库来源：
    -   有公网IP的自建数据库；
    -   通过专线接入的自建数据库；
    -   通过VPN网关接入的自建数据库；
    -   通过智能网关接入的自建数据库；
    -   ECS实例上的自建数据库。
-   支持Oracle数据库类型：
    -   9i；
    -   10g；
    -   11g；
    -   12c。
-   Oracle账号需要拥有sysdba权限。

## 操作步骤 {#section_tlq_2ql_zgb .section}

1.  登录[数据传输DTS控制台](https://dts.console.aliyun.com/#/home/)。
2.  在左侧菜单栏单击**数据订阅**，单击右上角**创建数据订阅**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/136262/155738535140414_zh-CN.png)

3.  选择对应功能、订阅实例类型、源实例地域以及购买量，单击右侧**立即购买**。

    **说明：** 数据订阅目前支持预付费和按量付费，关于计费说明，请参见[计费方式](https://cn.aliyun.com/price/product#/dts/detail)。

    基本配置和购买量说明如下。

    |类别|参数|说明|
    |--|--|--|
    |基本配置|功能|选择数据订阅。|
    |订阅实例类型|选择Oracle。|
    |源实例地域|源实例地域为要订阅的实例所在地区，订购后不支持更换地域。非ECS实例上的自建数据库可就近选择所在地区。 **说明：** 当SDK通过公网访问订阅通道并订阅数据时，会收取公网流量费用，各个地区公网流量单价，详见[价格说明](https://cn.aliyun.com/price/product#/dts/detail)。

 |
    |购买量|订购时长|设置购买时长。 **说明：** 仅针对预付费实例。

 |
    |数量|数量为一次性购买的订阅通道的数量，如果购买的是按量付费实例，一次最多购买99条链路。|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/136262/155738535140415_zh-CN.png)

4.  在数据订阅界面，单击目标订阅ID右侧的**配置订阅通道**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/136262/155738535240416_zh-CN.png)

5.  在创建数据订阅界面填写Oracle数据库信息，单击右下角**授权白名单并进入下一步**。具体配置说明如下。

    |参数|说明|
    |--|--|
    |订阅名称|由大小写字母、数字、下划线、中文组成。|
    |实例类型|根据不同的实例来源选择对应的实例类型，可选类型如下：     -   ECS上的自建数据库；
    -   通过专线/VPN网关/智能网关接入的自建数据库；
    -   有公网IP的自建数据库。
 |
    |数据库类型|默认为**Oracle**。|
    |实例地区|默认为购买时选择的地区。|
    |ECS实例ID|数据库所在的ECS实例ID。 **说明：** 当**实例类型**为**ECS上的自建数据库**，须填写该参数。

 |
    |对端专有网络|专线接入阿里云接入点的VPC ID。 **说明：** 当**实例类型**为**通过专线/VPN网关/智能网关接入的自建数据库**，须填写该参数。

 |
    |主机名或IP地址|Oracle数据库的公网IP地址或者主机名。 **说明：** 当**实例类型**为**有公网IP的自建数据库**，须填写该参数。

 |
    |端口|Oracle数据库的端口，默认为1521。|
    |SID|Oracle数据库的SID。|
    |数据库账号|Oracle数据库的数据库账号。|
    |数据库密码|Oracle数据库的数据库密码。|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/136262/155738535240417_zh-CN.png)

6.  选择订阅对象，单击右下角**保存并预检查**。

    **说明：** 

    -   DTS的订阅对象粒度细分为库、表。即用户可以选择订阅某些库或者是订阅某几张表。
    -   DTS将订阅数据类型细分为数据变更、结构变更。如果只选择订阅对象及数据变更的话，那么只能订阅到insert/delete/update三种数据变更内容，如果需要订阅结构变更（DDL），那么需要选择订阅数据类型中的结构变更。一旦订阅了结构变更，那么DTS会将整个Oracle数据库的所有结构变更拉取出来，用户需要使用SDK过滤需要的数据。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/136262/155738535240418_zh-CN.png)

7.  等待预检查完成，当进度条显示为预检查通过100%后，单击**关闭**。

    **说明：** 如果检查失败，可以根据错误项的提示进行修复，然后重新启动任务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/136262/155738535240419_zh-CN.png)

8.  数据订阅通道状态显示正常即可。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/136262/155738535240420_zh-CN.png)



# 将RDS for MySQL同步至DataHub {#concept_744352 .concept}

本文介绍如何使用数据传输服务（Data Transmission Service，简称DTS），将RDS for MySQL同步至DataHub，可用于流计算等大数据产品对数据进行实时分析等场景。

## 源库支持的实例类型 {#section_mym_hbb_d3b .section}

执行数据同步操作的MySQL数据库支持以下实例类型：

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库
-   同一或不同云账号下的RDS for MySQL实例

本文以**RDS for MySQL实例**为例介绍配置流程，当源库为其他实例类型时，配置流程与该案例类似。

**说明：** 如果源库为自建MySQL数据库，您还需要对源库进行配置，详情请参见[为自建MySQL配置账号与binlog](cn.zh-CN/用户指南/实时同步/为自建MySQL创建账号并设置binlog.md#)。

## 前提条件 {#section_s4r_vyb_d3b .section}

-   DataHub实例的地域为华东1、华东2、华北2或华南1。
-   DataHub实例中，已创建用作接收同步数据的Project，详情请参见[创建Project](https://help.aliyun.com/document_detail/47448.html)。

## 功能限制 {#section_rqq_2cb_d3b .section}

-   不支持全量数据初始化，即DTS不会将源RDS实例中同步对象的存量数据同步至目标DataHub实例中。
-   仅支持表级别的数据同步。

## 操作步骤 {#section_qck_cbc_d3b .section}

1.  购买数据同步作业，详情请参见[购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_l2l_34b_rhb)。

    **说明：** 购买时，选择源实例为**MySQL**，选择目标实例为**DataHub**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156463648850604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置同步通道的源实例及目标实例信息。

    ![DataHub同步源目信息配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17129/156463648849690_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择作为数据同步源的RDS实例ID。|
    |数据库账号|填入源RDS的数据库账号。 **说明：** 当源RDS实例的数据库类型为**MySQL 5.5**或**MySQL 5.6**时，无需配置**数据库账号**和**数据库密码**。

 |
    |数据库密码|填入数据库账号对应的密码。|
    |连接方式|根据需求选择为**非加密连接**或**SSL安全连接**，本案例选择为**非加密连接**。 **说明：** 选择 **SSL安全连接**时，需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。

 |
    |目标实例信息|实例类型|固定为**DataHub**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |Project|选择DataHub实例的**Project**。|

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标RDS实例的白名单中，用于保障DTS服务器能够正常连接源RDS实例和目标DataHub实例。

8.  配置同步策略及对象信息。

    ![配置同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17129/156463648949691_zh-CN.png)

    |配置项目|配置说明|
    |:---|:---|
    |同步初始化|勾选**结构初始化**。 **说明：** 勾选**结构初始化**后，在数据同步作业的初始化阶段，DTS会将同步对象的结构信息（例如表结构）同步至目标DataHub实例。

 |
    |选择同步对象| 在源库对象框中单击待迁移的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156463648940698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 

    -   同步对象的选择粒度为表。
    -   默认情况下，同步对象的名称保持不变。如果您需要同步对象在目标实例上名称不同，那么需要使用DTS提供的对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)。
 |

9.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156463648947468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
10. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
11. 等待该同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在 数据同步页面，查看数据同步作业的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156463648941059_zh-CN.png)


## Topic结构定义说明 {#section_ezq_3a3_xro .section}

DTS在将RDS for MySQL产生的增量数据，同步至DataHub实例的Topic时，Topic中除了存储更新的数据，还会存储一些元信息，示例如下。

|dts\_record\_id|dts\_instance\_id|dts\_db\_name|dts\_table\_name|dts\_operation\_flag|dts\_utc\_timestamp|dts\_before\_flag|dts\_after\_flag|dts\_col1|…|dts\_colN|
|:--------------|:----------------|:------------|:---------------|:-------------------|:------------------|:----------------|:---------------|:--------|:-|:--------|
|1|234|db1|sbtest1|I|1476258462|N|Y|1|…|JustInsert|
|2|234|db1|sbtest1|U|1476258463|Y|N|1|…|JustUpdate|
|2|234|db1|sbtest1|U|1476258463|N|Y|1|…|JustUpdate|
|3|234|db1|sbtest1|D|1476258464|Y|N|1|…|JustDelete|

**结构定义说明**

|字段|说明|
|:-|:-|
|dts\_record\_id|增量日志的记录id，为该日志唯一标识。 **说明：** 

-   id的值唯一且递增。
-   如果增量日志的操作类型为UPDATE，那么增量更新会被拆分成两条记录，一条为DELETE，一条为INSERT，并且这两条记录的dts\_record\_id的值相同。

 |
|dts\_instance\_id|数据库的server id|
|dts\_db\_name|数据库名称。|
|dts\_table\_name|表名。|
|dts\_operation\_flag|操作类型，取值： -   **I**：INSERT操作。
-   **D**：DELETE操作。
-   **U**：UPDATE操作。

 |
|dts\_utc\_timestamp|操作时间戳，即binlog的时间戳（UTC 时间）。|
|dts\_before\_flag|所有列的值是否更新前的值，取值：**Y**或**N**。|
|dts\_after\_flag|所有列的值是否更新后的值，取值：**Y**或**N**。|

## 关于dts\_before\_flag和dts\_after\_flag的补充说明 {#section_tmd_rsg_m3b .section}

对于不同的操作类型，增量日志中的**dts\_before\_flag**和**dts\_after\_flag**定义如下：

-   INSERT

    当操作类型为INSERT时，所有列的值为新插入的记录值，即为更新后的值，所以before\_flag取值为**N**，after\_flag取值为**Y**，示例如下。

    |dts\_record\_id|dts\_instance\_id|dts\_db\_name|dts\_table\_name|dts\_operation\_flag|dts\_utc\_timestamp|dts\_before\_flag|dts\_after\_flag|dts\_col1|….|dts\_colN|
    |:--------------|:----------------|:------------|:---------------|:-------------------|:------------------|:----------------|:---------------|:--------|:-|:--------|
    |1|234|db1|sbtest1|I|1476258462|N|Y|1|…..|JustInsert|

-   UPDATE

    当操作类型为UPDATE时，DTS会将UPDATE操作拆为两条增量日志。这两条增量日志的dts\_record\_id、dts\_operation\_flag及 dts\_utc\_timestamp对应的值相同。

    第一条增量日志记录了更新前的值，所以dts\_before\_flag取值为**Y**，dts\_after\_flag取值为**N**。第二条增量日志记录了更新后的值，所以 dts\_before\_flag取值为**N**，dts\_after\_flag取值为**Y**，示例如下。

    |dts\_record\_id|dts\_instance\_id|dts\_db\_name|dts\_table\_name|dts\_operation\_flag|dts\_utc\_timestamp|dts\_before\_flag|dts\_after\_flag|dts\_col1|….|dts\_colN|
    |:--------------|:----------------|:------------|:---------------|:-------------------|:------------------|:----------------|:---------------|:--------|:-|:--------|
    |2|234|db1|sbtest1|I|1476258463|Y|N|1|…..|JustInsert|
    |2|234|db1|sbtest1|I|1476258463|N|Y|1|…..|JustUpdate|

-   DELETE

    当操作类型为DELETE时，增量日志中所有的列值为被删除的值，即列值不变，所以dts\_before\_flag取值为**Y**， dts\_after\_flag取值为**N**，示例如下。

    |dts\_record\_id|dts\_instance\_id|dts\_db\_name|dts\_table\_name|dts\_operation\_flag|dts\_utc\_timestamp|dts\_before\_flag|dts\_after\_flag|dts\_col1|….|dts\_colN|
    |:--------------|:----------------|:------------|:---------------|:-------------------|:------------------|:----------------|:---------------|:--------|:-|:--------|
    |3|234|db1|sbtest1|D|1476258464|Y|N|1|…..|JustDelete|


## 后续操作 {#section_zdu_1d7_hn1 .section}

配置完数据同步作业后，您可以对同步到DataHub实例中的数据进行计算分析。更多详情，请参见[阿里云实时计算](https://help.aliyun.com/document_detail/110778.html)。


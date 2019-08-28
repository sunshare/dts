# 从RDS for MySQL同步到AnalyticDB for MySQL {#concept_1216438 .task}

分析型数据库MySQL版（AnalyticDB for MySQL），是阿里巴巴自主研发的海量数据实时高并发在线分析（Realtime OLAP）云计算服务，使得您可以在毫秒级针对千亿级数据进行即时的多维分析透视和业务探索。通过数据传输服务DTS（Data Transmission Service），您可以将RDS for MySQL同步到AnalyticDB for MySQL，帮助您快速构建企业内部BI、交互查询、实时报表等系统。

-   已创建目标AnalyticDB for MySQL实例，详情请参见[创建AnalyticDB for MySQL（2.0）](https://help.aliyun.com/document_detail/98724.html)或[创建AnalyticDB for MySQL（3.0）](https://help.aliyun.com/document_detail/122234.html)。

    **说明：** 不支持将青岛、美国（弗吉尼亚）、英国（伦敦）地域的AnalyticDB for MySQL（2.0）实例作为同步的目标实例；不支持将美国（硅谷）地域的AnalyticDB for MySQL（3.0）实例作为同步的目标实例。

-   确保目标AnalyticDB for MySQL具备充足的存储空间。

## 注意事项 {#section_j8b_80m_38o .section}

-   请勿在数据同步时，对源库的同步对象使用gh-ost或pt-online-schema-change等类似工具执行在线DDL变更，否则会导致同步失败。
-   由于AnalyticDB for MySQL（3.0）本身的使用限制，当AnalyticDB for MySQL（3.0）实例中的节点磁盘空间使用量超过80%，该实例将被锁定。请提前根据待同步的对象预估所需空间，确保目标实例具备充足的存储空间。

## 源库支持的实例类型 {#section_ige_lue_rua .section}

执行数据同步操作的源MySQL数据库支持以下实例类型：

-   RDS for MySQL
-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库

本文以**RDS for MySQL**为例介绍配置流程，当源数据库为自建MySQL数据库时，配置流程与该案例类似。

**说明：** 如果您的源数据库为自建MySQL数据库，您还需要[为自建MySQL创建账号并设置binlog](cn.zh-CN/用户指南/准备工作（自建库）/为自建MySQL创建账号并设置binlog.md#)。

## 术语/概念对应关系 {#section_b35_5cc_ukj .section}

|MySQL|AnalyticDB for MySQL|
|-----|--------------------|
|数据库| -   AnalyticDB for MySQL（2.0）：[表组](https://help.aliyun.com/document_detail/93885.html#h2-u8868u7EC43)
-   AnalyticDB for MySQL（3.0）：[数据库](https://help.aliyun.com/document_detail/122187.html#h2-u6570u636Eu5E935)

 |
|表| -   AnalyticDB for MySQL（2.0）：[表](https://help.aliyun.com/document_detail/93885.html#h2-u88684)
-   AnalyticDB for MySQL（3.0）：[表](https://help.aliyun.com/document_detail/122187.html#h2-u88687)

 |

## 支持同步的SQL操作 {#section_s1c_w58_ncx .section}

|目标数据库版本|支持的SQL操作|
|:------|:-------|
|AnalyticDB for MySQL 2.0| -   DDL操作：CREATE TABLE、ALTER TABLE、DROP TABLE
-   DML操作：INSERT、UPDATE、DELETE

 |
|AnalyticDB for MySQL 3.0| -   DDL操作：CREATE TABLE、ALTER TABLE、ADD COLUMN、DROP TABLE、TRUNCATE TABLE
-   DML操作：INSERT、UPDATE、DELETE

 |

## 数据库账号的权限要求 {#section_w01_xz7_hp4 .section}

|数据库实例|所需权限|
|-----|----|
|RDS for MySQL|Replication slave、Replication client及待同步对象的Select权限。|
|AnalyticDB for MySQL（2.0）|无需填写数据库账号信息，DTS会自动创建账号并授权。|
|AnalyticDB for MySQL（3.0）|读写权限。|

## 数据类型映射关系 {#section_c40_ugn_ueo .section}

由于MySQL和AnalyticDB for MySQL的数据类型并不是一一对应的，所以DTS在进行结构迁移时，会根据数据类型定义进行类型映射。数据类型映射关系如下表所示。

|MySQL数据类型|AnalyticDB数据类型|
|:--------|:-------------|
|INTEGER|INT|
|INT|INT|
|SMALLINT|SMALLINT|
|TINYINT|SMALLINT|
|MEDIUMINT|INT|
|BIGINT|BIGINT|
|DECIMAL|DECIMAL|
|NUMERIC|DECIMAL|
|FLOAT|REAL|
|DOUBLE|DOUBLE|
|BIT|BOOLEAN|
|DATE|DATE|
|TIMESTAMP|TIMESTAMP|
|DATETIME|TIMESTAMP|
|TIME|TIME|
|YEAR|INTEGER|
|CHAR|VARCHAR|
|VARCHAR|VARCHAR|
|TINYTEXT/TEXT/MEDIUMTEXT/LONGTEXT|VARCHAR|
|ENUM|VARCHAR|
|SET|VARCHAR|
|JSON|JSON|

## 操作步骤 {#section_ie9_1j9_xxs .section}

1.  [购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。 

    **说明：** 购买时，选择源实例为**MySQL**，目标实例为**分析型数据库AnalyticDB**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。 

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156696256950604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置同步通道的源实例及目标实例信息。 

    ![源目实例信息配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17127/156696256955263_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |同步作业名称|-|     -   DTS为每个同步作业自动生成一个名称，该名称没有唯一性要求。
    -   您可以根据需要修改同步作业的名称，建议配置具有业务意义的名称，便于后续的识别。
 |
    |源实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择作为数据同步源的RDS实例ID。|
    |数据库账号|填入源RDS的数据库账号。 **说明：** 当源RDS实例的数据库类型为**MySQL 5.5**或**MySQL 5.6**时，无需配置**数据库账号**和**数据库密码**。

 |
    |数据库密码|填入数据库账号对应的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**，本案例选择为**非加密连接**。 **说明：** 选择 **SSL安全连接**时，需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。

 |
    |目标实例信息|实例类型|固定为**ADS**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |版本|根据目标AnalyticDB for MySQL实例的版本，选择**2.0**或**3.0**。 **说明：** 

    -   选择为**2.0**后，无需配置**数据库账号**和**数据库密码**。
    -   选择为**3.0**后，您还需要配置**数据库账号**和**数据库密码**。
 |
    |数据库|选择作为数据同步目标的AnalyticDB for MySQL实例ID。|
    |数据库账号|填入链接AnalyticDB for MySQL的数据库账号。|
    |数据库密码|填入数据库账号对应的密码。|

7.  单击页面右下角的**授权白名单并进入下一步**。
8.  配置同步策略及对象信息。 

    ![配置同步策略和对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17127/156696256955267_zh-CN.png)

    |配置项目|配置说明|
    |:---|:---|
    |同步初始化|默认情况下，您需要同时勾选**结构初始化**和**全量数据初始化**。预检查完成后，DTS会将源实例中待同步对象的结构及数据在目标实例中初始化，作为后续增量同步数据的基线数据。|
    |目标已存在表的处理模式|     -   **预检查并报错拦截**：检查目标数据库中是否有同名的表。如果目标数据库中没有同名的表，则通过该检查项目；如果目标数据库中有同名的表，则在预检查阶段提示错误，数据同步作业不会被启动。

**说明：** 如果目标库中同名的表不方便删除或重命名，您可以[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)来避免表名冲突。

    -   **无操作**：跳过目标数据库中是否有同名表的检查项。

**警告：** 选择为**无操作**，可能导致数据不一致，给业务带来风险，例如：

        -   表结构一致的情况下，在目标库遇到与源库主键的值相同的记录，则会保留目标实例中的该条记录，即源库中的该条记录不会同步至目标数据库中。
        -   表结构不一致的情况下，可能会导致无法初始化数据、只能同步部分列的数据或同步失败。
 |
    |多表归并|     -   选择为**是**：DTS将在每个表中增加`__dts_data_source`列来存储数据来源，且不再支持DDL同步。
    -   选择为**否**：默认选项，支持DDL同步。
 |
    |同步操作类型|根据业务勾选需要同步的操作类型，默认情况下都处于勾选状态。|
    |选择同步对象| 在源库对象框中单击待迁移的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156696256940698_zh-CN.png)将其移动至已选择对象框。

 同步对象的选择粒度为库、表。

 **说明：** 

    -   如果选择整个库作为同步对象，那么该库中所有对象的结构变更操作都会同步至目标库。
    -   如果选择某个表作为同步对象，那么只有这个表的DROP/ALTER/TRUNCATE/RENAME TABLE、CREATE/DROP INDEX操作会同步至目标库。
    -   默认情况下，同步对象的名称保持不变。如果您需要同步对象在目标实例上名称不同，那么需要使用DTS提供的对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)。
 |

9.  上述配置完成后，单击页面右下角的**下一步**。
10. 设置待同步的表在目标库中类型。 

    ![设置表类型](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17127/156696257055270_zh-CN.png)

    **说明：** 选择了**结构初始化**后，您需要定义待同步的表在AnalyticDB for MySQL中的**类型**、主**键列**、**分区列**等信息，详情请参见[ADB 2.0 SQL手册](https://help.aliyun.com/document_detail/94860.html)和[ADB 3.0 SQL手册](https://help.aliyun.com/document_detail/123333.html)。

11. 上述配置完成后，单击页面右下角的**预检查并启动**。 

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156696257047468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
13. 等待同步作业的链路初始化完成，直至处于**同步中**状态。 您可以在 数据同步页面，查看数据同步作业的状态。

    ![数据同步状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156696257041059_zh-CN.png)



# ECS上的自建数据库数据同步至RDS for MySQL实例 {#concept_263741 .concept}

数据传输服务DTS（Data Transmission Service）支持ECS上的自建MySQL数据库数据同步至RDS for MySQL实例，实现增量数据的实时同步。

## 前提条件 {#section_r5g_vyx_2jv .section}

-   自建MySQL数据库版本为5.1、5.5、5.6、5.7版本。
-   数据同步的目标RDS实例已存在，如不存在请[创建RDS实例](https://help.aliyun.com/document_detail/26117.html)。

## 注意事项 {#section_iaj_pbc_xyv .section}

-   如果同步对象为单个或多个表（非整库），那么在数据同步时，请勿对源库的同步对象使用gh-ost或pt-online-schema-change等类似工具执行在线DDL变更，否则会导致同步失败。
-   暂不支持中国（香港）可用区A的RDS for MySQL实例配置数据同步。
-   目标实例不支持访问模式为标准模式且只有外网连接地址的RDS for MySQL实例。
-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。
-   全量初始化过程中，并发insert导致目标实例的表碎片，全量初始化完成后，目标实例的表空间比源实例的表空间大。
-   为保证同步延迟显示的准确性，DTS会在源实例新增一张心跳表，心跳表的表名为：`_##dts_mysql_heartbeat##_`。
-   DTS暂不支持XA Transaction，当同步过程中遇到XA Transaction会导致同步失败。

## 支持的同步架构 {#section_jvv_5zc_crw .section}

-   一对一单向同步
-   一对多单向同步
-   多对一单向同步
-   级联单向同步
-   一对一双向同步

    **说明：** 如需实现双向同步，请参考[创建RDS for MySQL实例间的双向数据同步](cn.zh-CN/用户指南/实时同步/MySQL实时同步至MySQL/RDS for MySQL实例间的双向同步.md#)。


## 支持同步语法 {#section_loc_jg5_p1u .section}

RDS for MySQL实例的数据同步支持所有DML语法和部分DDL语法的同步，支持的DDL语法如下。

-   ALTER TABLE、ALTER VIEW、ALTER FUNCTION、ALTER PROCEDURE。
-   CREATE DATABASE、CREATE SCHEMA、CREATE INDEX、CREATE TABLE、CREATE PROCEDURE、CREATE FUNCTION、CREATE TRIGGER、CREATE VIEW、CREATE EVENT。
-   DROP FUNCTION、DROP EVENT、DROP INDEX、DROP PROCEDURE、DROP TABLE、DROP TRIGGER、DROP VIEW。
-   RENAME TABLE、TRUNCATE TABLE。

## 功能限制 {#section_4eh_aed_ews .section}

-   不兼容触发器

    同步对象为整个库且这个库中包含了会更新同步表内容的触发器，那么可能导致同步数据不一致。例如数据库中存在了两个表A和B。表A上有一个触发器，触发器内容为在insert一条数据到表A之后，在表B中插入一条数据。这种情况在同步过程中，如果源实例表A上进行了Insert操作，则会导致表B在源实例跟目标实例数据不一致。

    此类情况须要将目标实例中的对应触发器删除掉，表B的数据由源实例同步过去，详情请参考[触发器存在情况下如何配置同步作业](https://help.aliyun.com/document_detail/26655.html) 。

-   rename table限制

    rename table操作可能导致同步数据不一致。例如同步对象只包含表A，如果同步过程中源实例将表A重命名为表B，那么表B将不会被同步到目标库。为避免该问题，您可以在数据同步配置时，选择同步表A和表B所在的整个数据库作为同步对象。


## 数据同步前准备工作 {#section_kdc_ex9_41y .section}

在正式配置数据同步作业之前，您需要[为自建MySQL创建账号并设置binlog](cn.zh-CN/用户指南/准备工作（自建库）/为自建MySQL创建账号并设置binlog.md#)。

## 操作步骤 {#section_ebx_ogb_wiy .section}

1.  [购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。

    **说明：** 购买时，选择源实例和目标实例均为**MySQL**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  定位至已购买的数据同步实例，单击该实例的**配置同步链路**。

    ![配置MySQL单向同步任务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17124/156643681846141_zh-CN.png)

5.  配置同步通道的源实例及目标实例信息。

    ![MySQL单向同步源目实例信息配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/217806/156643681847056_zh-CN.png)

    |类别|配置项|说明|
    |:-|:--|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**ECS上的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |对端专有网络|选择作为同步数据源的ECS实例ID。|
    |数据库类型|购买数据同步实例时选择的数据库类型：**MySQL**，不可变更。|
    |端口|填入自建MySQL数据库的服务端口，默认为**3306**。|
    |数据库账号|填入自建MySQL数据库的账号，需要具备Replicationslave, Replication client及所有同步对象的Select权限。|
    |数据库密码|填入自建MySQL数据库账号对应的密码。|
    |目标实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择作为数据同步目标的RDS实例ID。|
    |数据库账号|填入目标RDS的数据库账号。 **说明：** 当目标RDS实例的数据库类型为**MySQL5.5**或**MySQL5.6**时，无需配置**数据库账号**和**数据库密码**。

 |
    |数据库密码|填入数据库账号对应的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**，本案例选择为**非加密连接**。 **说明：** 选择 **SSL安全连接**时，需要提前开启RDS实例的SSL加密功能，详情请参考[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。

 |

6.  单击页面右下角的**授权白名单并进入下一步**。
7.  配置同步策略及对象信息。

    ![MySQL单向同步配置同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17124/156643681846145_zh-CN.png)

    |配置项目|配置说明|
    |:---|:---|
    |选择同步对象| 同步对象的选择粒度为库、表。

     -   如果选择整个库作为同步对象，那么该库中所有对象的结构变更操作都会同步至目标库。
    -   如果选择某个表作为同步对象，那么只有这个表的DROP/ALTER/TRUNCATE/RENAMETABLE、CREATE/DROP INDEX操作会同步至目标库。
 **说明：** 默认情况下，同步对象的名称保持不变。如果您需要同步对象在目标实例上名称不同，那么需要使用DTS提供的对象名映射功能，详情请参考[库表列映射](https://help.aliyun.com/document_detail/26628.html)。

 |

8.  上述配置完成后单击页面右下角的**下一步**。
9.  配置同步初始化的高级配置信息。

    ![数据同步高级设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156643681841055_zh-CN.png)

    -   此步骤会将源实例中已经存在同步对象的结构及数据在目标实例中初始化，作为后续增量同步数据的基线数据。
    -   同步初始化类型细分为：结构初始化，全量数据初始化。默认情况下，需要选择**结构初始化**和**全量数据初始化**。
10. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步任务。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156643681841056_zh-CN.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
11. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，该同步作业的同步任务正式开始。
12. 等待该同步作业的链路初始化完成，直至状态处于**同步中**。

    您可以在 数据同步页面，查看数据同步状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156643681841059_zh-CN.png)



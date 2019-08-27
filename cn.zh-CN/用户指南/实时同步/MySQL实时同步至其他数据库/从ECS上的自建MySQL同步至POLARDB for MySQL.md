# 从ECS上的自建MySQL同步至POLARDB for MySQL {#task_1563864 .task}

POLARDB是阿里巴巴自主研发的下一代关系型分布式云原生数据库，可完全兼容MySQL，具备简单易用、高性能、高可靠、高可用等优势。通过数据传输服务DTS（Data Transmission Service），您可以将自建的MySQL数据库同步至POLARDB for MySQL，本文以ECS上的自建MySQL为例介绍配置流程。

已购买POLARDB for MySQL集群，详情请参见[创建POLARDB for MySQL集群](https://help.aliyun.com/document_detail/58769.html)。

## 注意事项 {#section_w16_7kf_hcx .section}

-   如果同步对象为单个或多个表（非整库），那么在数据同步时，请勿对源库的同步对象使用gh-ost或pt-online-schema-change等类似工具执行在线DDL变更，否则会导致同步失败。
-   全量初始化过程中，并发INSERT会导致目标集群的表碎片，全量初始化完成后，目标集群的表空间比源集群的表空间大。
-   如果数据同步的源集群没有主键或唯一约束，且记录的全字段没有唯一性，可能会出现重复数据。

## 支持同步的SQL操作 {#section_6pv_o92_x0r .section}

-   INSERT、UPDATE、DELETE、REPLACE
-   ALTER TABLE、ALTER VIEW、ALTER FUNCTION、ALTER PROCEDURE
-   CREATE DATABASE、CREATE SCHEMA、CREATE INDEX、CREATE TABLE、CREATE PROCEDURE、CREATE FUNCTION、CREATE TRIGGER、CREATE VIEW、CREATE EVENT
-   DROP FUNCTION、DROP EVENT、DROP INDEX、DROP PROCEDURE、DROP TABLE、DROP TRIGGER、DROP VIEW
-   RENAME TABLE、TRUNCATE TABLE

## 功能限制 {#section_nsb_ifu_b1c .section}

-   不兼容触发器

    同步对象为整个库且这个库中包含了会更新同步表内容的触发器，那么可能导致同步数据不一致。例如数据库中存在了两个表A和B。表A上有一个触发器，触发器内容为在INSERT一条数据到表A之后，在表B中插入一条数据。这种情况在同步过程中，如果源集群表A上进行了INSERT操作，则会导致表B在源集群跟目标集群数据不一致。

    此类情况须要将目标集群中的对应触发器删除掉，表B的数据由源集群同步过去，详情请参见[触发器存在情况下如何配置同步作业](https://help.aliyun.com/document_detail/26655.html) 。

-   RENAME TABLE限制

    RENAME TABLE操作可能导致同步数据不一致。例如同步对象只包含表A，如果同步过程中源集群将表A重命名为表B，那么表B将不会被同步到目标库。为避免该问题，您可以在数据同步配置时，选择同步表A和表B所在的整个数据库作为同步对象。


## 同步前准备工作 {#section_3xd_f4c_lgk .section}

 [为自建MySQL创建账号并设置binlog](cn.zh-CN/用户指南/准备工作（自建库）/为自建MySQL创建账号并设置binlog.md#) 

**说明：** 用于数据同步的数据库账号需具备待同步对象的SELECT、REPLICATION CLIENT、REPLICATION SLAVE权限。

## 支持的同步架构 {#section_8do_j28_5bb .section}

-   一对一单向同步
-   一对多单向同步
-   级联单向同步
-   多对一单向同步

关于各类同步架构的介绍及注意事项，请参见[数据同步拓扑介绍](cn.zh-CN/用户指南/实时同步/数据同步拓扑介绍.md#)。

## 操作步骤 {#section_tqw_tml_6br .section}

1.  [购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。 

    **说明：** 购买时，选择源实例为**MySQL**、目标实例为**POLARDB**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。 

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156689028950604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置同步通道的源实例及目标实例信息。 

    ![配置源和目标实例信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1227086/156689028954380_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |同步作业名称|-|     -   DTS为每个任务自动生成一个同步作业名称，该名称没有唯一性要求。
    -   您可以根据需要修改同步作业名称，建议配置具有业务意义的名称，便于后续识别。
 |
    |源实例信息|实例类型|选择**MySQL**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |ECS实例ID|选择自建MySQL数据库所属的ECS实例ID。|
    |数据库类型|固定为**MySQL**，不可变更。|
    |端口|填入自建MySQL的数据库服务端口。|
    |数据库账号|填入连接自建MySQL的数据库账号。 **说明：** 用于数据同步的数据库账号需具备待同步对象的SELECT、REPLICATION CLIENT、REPLICATION SLAVE权限。

 |
    |数据库密码|填入数据库账号对应的密码。|
    |目标实例信息|实例类型|固定为**POLARDB**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |POLARDB实例ID|选择目标POLARDB集群ID。|
    |数据库账号|填入连接POLARDB集群的数据库账号。 **说明：** 用于数据同步的数据库账号需具备目标同步对象的ALL权限。

 |
    |数据库密码|填入数据库账号对应的密码。|

7.  单击页面右下角的**授权白名单并进入下一步**。 

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到ECS实例的内网入方向安全组规则和目标POLARDB集群的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  配置目标已存在表的处理模式和同步对象。 

    ![配置处理模式和同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/89979/156689029054325_zh-CN.png)

    |配置项目|配置说明|
    |:---|:---|
    |目标已存在表的处理模式|     -   **预检查并报错拦截**：检查目标数据库中是否有同名的表。如果目标数据库中没有同名的表，则通过该检查项目；如果目标数据库中有同名的表，则在预检查阶段提示错误，数据同步作业不会被启动。

**说明：** 如果目标库中同名的表不方便删除或重命名，您可以[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)来避免表名冲突。

    -   **无操作**：跳过目标数据库中是否有同名表的检查项。

**警告：** 选择为**无操作**，可能导致数据不一致，给业务带来风险，例如：

        -   表结构一致的情况下，如果在目标库遇到与源库主键的值相同的记录，在初始化阶段会保留目标库中的该条记录；在增量同步阶段则会覆盖目标库的该条记录。
        -   表结构不一致的情况下，可能会导致无法初始化数据、只能同步部分列的数据或同步失败。
 |
    |选择同步对象| 在源库对象框中单击待同步的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156689029040698_zh-CN.png)将其移动至已选择对象框。

 同步对象的选择粒度为库、表。

 **说明：** 

    -   如果选择整个库作为同步对象，那么该库中所有对象的结构变更操作都会同步至目标库。
    -   默认情况下，同步对象的名称保持不变。如果您需要同步对象在目标集群上名称不同，那么需要使用DTS提供的对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)。
 |

9.  上述配置完成后，单击页面右下角的**下一步**。
10. 配置同步初始化的高级配置信息。 

    ![数据同步高级设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156689029041055_zh-CN.png)

    **说明：** 同步初始化类型细分为：结构初始化，全量数据初始化。选择**结构初始化**和**全量数据初始化**后，DTS会在增量数据同步之前，将源数据库中待同步对象的结构和存量数据，同步到目标数据库。

11. 上述配置完成后，单击页面右下角的**预检查并启动**。 

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156689029047468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
13. 等待同步作业的链路初始化完成，直至处于**同步中**状态。 

    您可以在 数据同步页面，查看数据同步作业的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156689029041059_zh-CN.png)



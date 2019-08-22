# 从RDS for MySQL同步至MaxCompute {#concept_727969 .task}

大数据计算服务（MaxCompute，原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案。通过数据传输服务DTS（Data Transmission Service），您可以将MySQL的数据同步至MaxCompute，帮助您快速搭建数据实时分析系统。

-   已[开通MaxCompute](https://help.aliyun.com/document_detail/58226.html)。
-   已在MaxCompute中[创建项目](https://help.aliyun.com/document_detail/27815.html)。

## 注意事项 {#section_ibi_s6k_n4b .section}

-   仅支持表级别的数据同步。
-   在数据同步时，请勿对源库的同步对象使用gh-ost或pt-online-schema-change等类似工具执行在线DDL变更，否则会导致同步失败。
-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。

## 源库支持的实例类型 {#section_33d_1wz_jbj .section}

执行数据同步操作的MySQL数据库支持以下实例类型：

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库
-   同一或不同阿里云账号下的RDS for MySQL实例

本文以**RDS for MySQL实例**为例介绍配置流程，当源库为其他实例类型时，配置流程与该案例类似。

**说明：** 如果源库为自建MySQL数据库，您还需要[为自建MySQL创建账号并设置binlog](cn.zh-CN/用户指南/准备工作（自建库）/为自建MySQL创建账号并设置binlog.md#)。

## 同步过程介绍 {#section_lmn_2fm_b0h .section}

1.  结构初始化。 DTS将源库中待同步表的结构定义信息同步至MaxCompute中，初始化时DTS会将表名变更为*同步的目标表名*\_base，例如customer\_base。
2.  全量数据初始化。 DTS将源库中待同步表的存量数据，全部同步至Maxcompute的*同步的目标表名*\_base表中，作为后续增量同步数据的基线数据。

    **说明：** 该表也被称为全量基线表。

3.  增量数据同步。 DTS在Maxcompute中创建一个增量日志表，表名为*同步的目标表名*\_log，例如customer\_log，然后将源库产生的增量数据实时同步到该表中。

    **说明：** 关于增量日志表结构的详细信息，请参见[增量日志表结构定义说明](#section_5kn_hla_atd)。


## 操作步骤 {#section_m0z_t28_awf .section}

1.  [购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。 

    **说明：** 购买时，选择源实例为**MySQL**，目标实例为**MaxCompute**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。 

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156643735550604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置同步通道的源实例及目标实例信息。 

    ![源和目标实例配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735555482_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |同步作业名称|-|     -   DTS为每个同步作业自动生成一个名称，该名称没有唯一性要求。
    -   您可以根据需要修改同步作业名称，建议配置具有业务意义的名称，便于后续的识别。
 |
    |源实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |实例ID|选择作为数据同步源的RDS实例ID。|
    |数据库账号|填入源RDS的数据库账号。 **说明：** 当源RDS实例的数据库类型为**MySQL 5.5**或**MySQL 5.6**时，无需配置**数据库账号**和**数据库密码**。

 |
    |数据库密码|填入数据库账号对应的密码。|
    |连接方式|根据需求选择为**非加密连接**或**SSL安全连接**，本案例选择为**非加密连接**。 **说明：** 选择**SSL安全连接**时，需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。

 |
    |目标实例信息|实例类型|固定为**MaxCompute**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |Project|填入MaxCompute实例的**Project**，您可以在[MaxCompute工作空间列表](https://workbench.data.aliyun.com/consolenew#/projectlist)页面中查询。![MaxCompute工作空间列表](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735655484_zh-CN.png)

|

7.  单击页面右下角的**授权白名单并进入下一步**。 

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到RDS实例和MaxCompute实例的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  单击页面右下角的**下一步**，允许将MaxCompute中项目的下述权限授予给DTS同步账号。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735655487_zh-CN.png)

9.  配置同步策略和同步对象。 

    ![配置同步策略和对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735655488_zh-CN.png)

    |配置项目|配置说明|
    |:---|:---|
    |增量日志表分区定义|根据业务需求，选择分区名称。关于分区的相关介绍请参见[分区](https://help.aliyun.com/document_detail/27820.html)。|
    |同步初始化| 同步初始化类型细分为：结构初始化，全量数据初始化。

 此处同时勾选**结构初始化**和**全量数据初始化**，DTS会在增量数据同步之前，将源数据库中待同步对象的结构和存量数据，同步到目标数据库。

 |
    |目标已存在表的处理模式|     -   **预检查并报错拦截**：检查目标数据库中是否有同名的表。如果目标数据库中没有同名的表，则通过该检查项目；如果目标数据库中有同名的表，则在预检查阶段提示错误，数据同步作业不会被启动。

**说明：** 如果目标库中同名的表不方便删除或重命名，您可以[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)来避免表名冲突。

    -   **无操作**：跳过目标数据库中是否有同名表的检查项。

**警告：** 选择为**无操作**，可能导致数据不一致，给业务带来风险，例如：

        -   表结构一致的情况下，如果在目标库遇到与源库主键的值相同的记录，在初始化阶段会保留目标库中的该条记录；在增量同步阶段则会覆盖目标库的该条记录。
        -   表结构不一致的情况下，可能会导致无法初始化数据、只能同步部分列的数据或同步失败。
 |
    |选择同步对象| 在源库对象框中单击待同步的表，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156643735640698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 

    -   同步对象支持选择的粒度仅为表，您可以从多个库中选择表作为同步对象。
    -   默认情况下，同步对象的名称保持不变。如果您需要在目标实例上名称不同，那么需要使用DTS提供的对象名映射功能，详情请参见[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)。
 |

10. 上述配置完成后，单击页面右下角的**预检查并启动**。 

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156643735647468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
11. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
12. 等待同步作业的链路初始化完成，直至处于**同步中**状态。 

    您可以在 数据同步页面，查看数据同步作业的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156643735641059_zh-CN.png)


## 增量日志表结构定义说明 {#section_5kn_hla_atd .section}

DTS在将MySQL产生的增量数据同步至MaxCompute的增量日志表时，除了存储增量数据，还会存储一些元信息，示例如下。

![增量日志表结构](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735755989_zh-CN.png)

**说明：** 示例中的`modifytime_year string`、`modifytime_month string`、`modifytime_day string`、`modifytime_hour string`为分区字段，是在[配置同步策略和同步对象](#step_pze_gpg_5gx)步骤中指定的。

**结构定义说明**

|字段|说明|
|:-|:-|
|record\_id|增量日志的记录id，为该日志唯一标识。 **说明：** 

-   id的值唯一且递增。
-   如果增量日志的操作类型为UPDATE，那么增量更新会被拆分成两条记录，一条为DELETE，一条为INSERT，并且这两条记录的record\_id的值相同。

 |
|operation\_flag|操作类型，取值： -   **I**：INSERT操作。
-   **D**：DELETE操作。
-   **U**：UPDATE操作。

 |
|utc\_timestamp|操作时间戳，即binlog的时间戳（UTC 时间）。|
|before\_flag|所有列的值是否为更新前的值，取值：**Y**或**N**。|
|after\_flag|所有列的值是否为更新后的值，取值：**Y**或**N**。|

## 关于before\_flag和after\_flag的补充说明 {#section_zh0_9sn_4xf .section}

对于不同的操作类型，增量日志中的**before\_flag**和**after\_flag**定义如下：

-   INSERT

    当操作类型为INSERT时，所有列的值为新插入的记录值，即为更新后的值，所以before\_flag取值为**N**，after\_flag取值为**Y**，示例如下。

    ![INSERT操作示例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735755992_zh-CN.png)

-   UPDATE

    当操作类型为UPDATE时，DTS会将UPDATE操作拆为两条增量日志。这两条增量日志的record\_id、operation\_flag及utc\_timestamp对应的值相同。

    第一条增量日志记录了更新前的值，所以before\_flag取值为**Y**，after\_flag取值为**N**。第二条增量日志记录了更新后的值，所以before\_flag取值为**N**，after\_flag取值为**Y**，示例如下。

    ![UPDATE操作示例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735755993_zh-CN.png)

-   DELETE

    当操作类型为DELETE时，增量日志中所有的列值为被删除的值，即列值不变，所以before\_flag取值为**Y**，after\_flag取值为**N**，示例如下。

    ![DELETE操作示例](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735755994_zh-CN.png)


## 全量数据合并示例 {#section_ipe_0k0_469 .section}

执行数据同步的操作后，DTS会在MaxCompute中分别创建该表的全量基线表和增量日志表。您可以通过MaxCompute的SQL命令，对这两个表执行合并操作，得到某个时间点的全量数据。

本案例以customer表为例（表结构如下），介绍操作流程。

![customer表结构](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735756270_zh-CN.png)

1.  根据源库中待同步表的结构，在Maxcompute中创建用于存储合并结果的表。 

    例如，需要获取customer表在`1565944878`时间点的全量数据。为方便业务识别，创建如下数据表：

    ``` {#codeblock_4ji_l9w_uoj .lanuage-sql}
    CREATE TABLE `customer_1565944878` (
        `id` bigint NULL,
        `register_time` datetime NULL,
        `address` string);
    ```

    **说明：** 

    -   您可以在[Maxcompute的临时查询](https://help.aliyun.com/document_detail/111535.html)中，运行SQL命令。
    -   关于MaxCompute支持的数据类型与相关说明，请参见[数据类型](https://help.aliyun.com/document_detail/27821.html)。
2.  在MaxCompute中执行如下SQL命令，合并全量基线表和增量日志表，获取该表在某一时间点的全量数据。 

    SQL命令

    ``` {#codeblock_dzo_ndt_si7 .lanuage-sql}
    set odps.sql.allow.fullscan=true;
    insert overwrite table <result_storage_table>
    select <col1>,
           <col2>,
           <colN>
      from(
    select row_number() over(partition by t.<primary_key_column>
     order by record_id desc, after_flag desc) as row_number, record_id, operation_flag, after_flag, <col1>, <col2>, <colN>
      from(
    select incr.record_id, incr.operation_flag, incr.after_flag, incr.<col1>, incr.<col2>,incr.<colN>
      from <table_log> incr
     where utc_timestamp< <timestmap>
     union all
    select 0 as record_id, 'I' as operation_flag, 'Y' as after_flag, base.<col1>, base.<col2>,base.<colN>
      from <table_base> base) t) gt
    where record_num=1 
      and after_flag='Y'
    ```

    **说明：** 

    -   <result\_storage\_table\>：存储全量merge结果集的表名。
    -   <col1\>/<col2\>/<colN\>：同步表中的列名。
    -   <primary\_key\_column：同步表中的主键列名。
    -   <table\_log\>：增量日志表名。
    -   <table\_base\>：全量基线表名。
    -   <timestmap\>：需要获取全量数据的时间点。
    合并数据表，获取customer表在`1565944878`时间点的全量数据，示例如下：

    ``` {#codeblock_x05_j9c_qah}
    set odps.sql.allow.fullscan=true;
    insert overwrite table customer_1565944878
    select id,
           register_time,
           address
      from(
    select row_number() over(partition by t.id
     order by record_id desc, after_flag desc) as row_number, record_id, operation_flag, after_flag, id, register_time, address
      from(
    select incr.record_id, incr.operation_flag, incr.after_flag, incr.id, incr.register_time, incr.address
      from customer_log incr
     where utc_timestamp< 1565944878
     union all
    select 0 as record_id, 'I' as operation_flag, 'Y' as after_flag, base.id, base.register_time, base.address
      from customer_base base) t) gt
     where gt.row_number= 1
       and gt.after_flag= 'Y';
    ```

3.  上述命令执行完成后，可在customer\_1565944878表中查询合并后的数据。 

    ![查询merge后的数据](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17128/156643735756256_zh-CN.png)



# 从ECS上的自建MySQL同步至Elasticsearch {#task_1925734 .task}

阿里云Elasticsearch完全兼容开源Elasticsearch的功能及Security、Machine Learning、Graph、APM等商业功能，致力于数据分析、数据搜索等场景服务，支持企业级权限管控、安全监控告警、自动报表生成等功能。通过数据传输服务DTS（Data Transmission Service），您可以将自建ECS上的自建MySQL同步至Elasticsearch，帮助您快速构建数据。

-   已创建目标Elasticsearch实例，详情请参见[开通阿里云Elasticsearch服务](https://help.aliyun.com/document_detail/69055.html)。
-   自建MySQL数据库版本为5.1、5.5、5.6、5.7、8.0版本。

## 注意事项 {#section_i9c_1we_9f4 .section}

-   不支持同步DDL操作，如果源库中待同步的表在同步的过程中，已经执行了DDL操作，您需要先[移除同步对象](cn.zh-CN/用户指南/实时同步/移除同步对象.md#)，然后在Elasticsearch实例中移除该表对应的索引，最后[新增同步对象](cn.zh-CN/用户指南/实时同步/新增同步对象.md#)。
-   如果源库中待同步的表需要执行增加列的操作，您只需先在Elasticsearch实例中修改对应表的mapping，然后在源MySQL数据库中执行相应的DDL操作，最后暂停并启动DTS同步实例。

## 支持同步的SQL操作 {#section_ew3_eav_ksv .section}

INSERT、DELETE、UPDATE

## 数据类型映射关系 {#section_xtl_uxo_ua9 .section}

由于MySQL和Elasticsearch实例支持的数据类型不同，数据类型无法一一对应。所以DTS在进行结构初始化时，会根据目标库支持的数据类型进行类型映射，详情请参见[结构初始化涉及的数据类型映射关系](cn.zh-CN/.md#)。

## 准备工作 {#section_s97_xil_vn6 .section}

 [为自建MySQL创建账号并设置binlog](cn.zh-CN/用户指南/准备工作（自建库）/为自建MySQL创建账号并设置binlog.md#)

## 操作步骤 {#section_lzq_cjg_ojb .section}

1.  [购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。 

    **说明：** 购买时，选择源实例为**MySQL**、目标实例为**Elasticsearch**，并选择同步拓扑为**单向同步**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。 

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156766246450604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置同步通道的源实例及目标实例信息。 

    ![配置源和目标实例信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1526887/156766246458414_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |同步作业名称|-|     -   DTS为每个任务自动生成一个同步作业名称，该名称没有唯一性要求。
    -   建议配置具有业务意义的名称，便于后续的识别。
 |
    |源实例信息|实例类型|选择**ECS上的自建MySQL**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |ECS实例ID|选择自建MySQL数据库所属的ECS实例ID。|
    |数据库类型|固定为**MySQL**，不可变更。|
    |端口|填入自建MySQL的数据库服务端口。|
    |数据库账号|填入自建MySQL的数据库账号。 **说明：** 该账号需具备待同步对象的SELECT、REPLICATION CLIENT、REPLICATION SLAVE权限。

 |
    |数据库密码|填入数据库账号对应的密码。|
    |目标实例信息|实例类型|固定为**Elasticsearch**，不可变更。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |Elasticsearch|选择Elasticsearch实例ID。|
    |数据库账号|填入连接Elasticsearch实例的账号，默认账号为elastic。|
    |数据库密码|填入该账号对应的密码。|

7.  单击页面右下角的**授权白名单并进入下一步**。 

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到ECS实例的内网入方向安全组规则和目标Elasticsearch实例的白名单中，用于保障DTS服务器能够正常连接源和目标实例。

8.  配置索引名称、目标已存在表的处理模式和同步对象。 

    ![配置同步对象信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1526887/156766246458418_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |索引名称|     -   **表名** 

选择为**表名**后，在目标Elasticsearch实例中创建的索引名称和表名一致，在本案例中即为customer。

    -   **库名\_表名** 

选择为**库名\_表名**后，在目标Elasticsearch实例中创建的索引名称为*库名*\_*表名*，在本案例中即为dtstestdata\_customer。

 |
    |目标已存在表的处理模式|     -   **预检查并报错拦截**：检查目标数据库中是否有同名的索引。如果目标数据库中没有同名的索引，则通过该检查项目；如果目标数据库中有同名的索引，则在预检查阶段提示错误，数据同步作业不会被启动。

**说明：** 如果目标库中同名的索引不方便删除或重命名，您可以[设置同步对象在目标实例中的名称](cn.zh-CN/用户指南/实时同步/设置同步对象在目标实例中的名称.md#)来避免表名冲突。

    -   **无操作**：跳过目标数据库中是否有同名索引的检查项。

**警告：** 选择为**无操作**，可能导致数据不一致，给业务带来风险，例如：

        -   mapping结构一致的情况下，如果在目标库遇到与源库主键的值相同的记录，在初始化阶段会保留目标库中的该条记录；在增量同步阶段则会覆盖目标库的该条记录。
        -   mapping结构不一致的情况下，可能会导致无法初始化数据、只能同步部分列的数据或同步失败。
 |
    |选择同步对象| 在源库对象框中单击待同步的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156766246440698_zh-CN.png)将其移动至已选择对象框。

 同步对象的选择粒度为库、表。

 |

9.  在已选择对象区域框中，将鼠标指针放置在待同步的表上，并单击表名后出现的**编辑**，设置该表在目标Elasticsearch实例中的索引名称、Type名称等信息。 

    ![设置索引名称等信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1526887/156766246458425_zh-CN.png)

    |配置|说明|
    |--|--|
    |索引名称|详情请参见[Elasticsearch基本概念](https://help.aliyun.com/document_detail/58107.html)。|
    |Type名称|
    |过滤条件|您可以设置SQL过滤条件，过滤待同步的数据，只有满足过滤条件的数据才会被同步到目标实例，详情请参见[通过SQL条件过滤待同步数据](cn.zh-CN/用户指南/实时同步/通过SQL条件过滤待同步数据.md#)。|
    |是否分区|选择是否设置分区，如果您选择为**是**，您还需要设置**分区列**和**分区数量**。|
    |\_id取值|如果选择为**业务主键**，那么您还需要设置对应的**业务主键列**。|
    |添加参数|选择所需的**字段参数**和**字段参数值**，字段参数及取值介绍请参见[Elasticsearch官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-params.html)。|

10. 上述配置完成后，单击页面右下角的**预检查并启动**。 

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156766246447468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
11. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
12. 等待同步作业的链路初始化完成，直至处于**同步中**状态。 

    您可以在 数据同步页面，查看数据同步作业的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156766246441059_zh-CN.png)


## 查看同步后的索引和数据 {#section_9ym_pon_kjz .section}

数据同步作业处于**同步中**状态后，您可以连接Elasticsearch实例（本案例使用[Head插件](https://help.aliyun.com/document_detail/120755.html)进行连接），确认创建的索引和同步的数据是否符合业务的预期。

**说明：** 如果不符合业务预期，您可以删除该索引及对应的数据，然后重新配置数据同步作业。

![查看Elasticsearch数据](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1526887/156766246458790_zh-CN.png)


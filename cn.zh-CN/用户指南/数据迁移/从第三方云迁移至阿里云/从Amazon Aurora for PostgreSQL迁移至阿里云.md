# 从Amazon Aurora for PostgreSQL迁移至阿里云 {#concept_xhn_mpw_fhb .concept}

使用数据传输服务DTS（Data Transmission Service）， 您可以将Amazon Aurora for PostgreSQL的数据全量迁移至阿里云。

## 前提条件 {#section_nnr_lkv_fhb .section}

-   Amazon Aurora for PostgreSQL的数据库版本为9.2、9.3、9.4、9.5、9.6 或10.0版本。
-   为保障DTS的正常连接，Amazon Aurora for PostgreSQL的公开可用性功能须设置为**是**。
-   已创建阿里云RDS for PostgreSQL，如未创建请参见[创建RDS for PostgreSQL](https://help.aliyun.com/document_detail/53730.html)。
-   阿里云RDS for PostgreSQL的存储空间须大于Amazon Aurora for PostgreSQL的数据库占用空间。

## 注意事项 {#section_abn_bfq_fhb .section}

-   为避免影响您的正常业务使用，请在业务低峰期进行数据迁移。
-   不支持使用DTS进行[增量迁移](https://help.aliyun.com/knowledge_detail/39252.html)。

    **说明：** 使用DTS进行全量数据迁移操作前，须停止Amazon Aurora for PostgreSQL数据库的相关业务，同时为了保障数据一致性，迁移期间请勿在Amazon Aurora for PostgreSQL数据库中写入新的数据。

-   不支持迁移使用C语言编写的function。

## 费用说明 {#section_zfk_nfq_fhb .section}

|迁移类型|链路配置费用|公网流量费用|
|:---|:-----|:-----|
|全量数据迁移|不收取|不收取|

## 迁移类型说明 {#section_mx2_pfq_fhb .section}

-   结构迁移

    DTS将迁移对象的结构定义迁移到阿里云RDS for PostgreSQL，支持结构迁移的对象包含table、trigger、view、sequence、function、user defined type、rule、domain、operation及aggregate。

-   全量数据迁移

    将迁移对象的存量数据全部迁移到阿里云RDS for PostgreSQL数据库中。


## 迁移账号权限要求 {#section_eby_wfq_fhb .section}

|迁移数据源|结构迁移|全量迁移|
|:----|:---|:---|
|Amazon Aurora for PostgreSQL|pg\_catalog的usage权限|迁移对象的select权限|
|阿里云RDS for PostgreSQL|迁移对象的create、usage权限|schema的owner权限|

## 迁移顺序 {#section_icq_zfq_fhb .section}

为解决对象间的依赖，提高迁移成功率，DTS对结构对象及数据的迁移顺序如下。

1.  迁移结构对象，包含Table、view、sequence、function、user defined type、rule、domain、operation、aggregate 。
2.  全量数据迁移。
3.  迁移结构对象，包含trigger、foreign key。

## 迁移前准备工作 {#section_idp_1gq_fhb .section}

1.  登录Amazon RDS控制台。
2.  进入Amazon Aurora for PostgreSQL的基本信息页面。
3.  选择角色为**写入器**的节点。
4.  在连接和安全性区域框，单击VPC安全组名称进入安全组配置页面。

    ![安全组规则](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150447/156255173241943_zh-CN.png)

5.  在安全组设置页面，将DTS服务器地址添加至入站规则中。

    **说明：** 

    -   在[DTS IP段](https://help.aliyun.com/document_detail/84900.html)文档中，根据目标实例的地域信息，选择需要添加的IP地址段。例如，源数据库的地域为新加坡，目标实例的地域为杭州，那么需要将杭州地区的DTS IP地址段加入至入站规则中。
    -   在加入IP地址段时，您可以一次性加入所需的IP地址，无需逐条添加入站规则。
    ![编辑AWS入站规则](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150145/156255173241849_zh-CN.png)


## 数据迁移操作步骤 {#section_y5w_bgq_fhb .section}

1.  登录[DTS数据传输服务控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  单击数据迁移页面右侧的**创建迁移任务**。
4.  配置迁移任务的**源库及目标库**信息。

    ![源库及目标库配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150145/156255173241886_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**有公网IP的自建数据库**。|
    |实例地区|源实例所在的地区，当实例类型选择为**有公网IP的自建数据库**时，该选项无需设置。|
    |数据库类型|选择**PostgreSQL**。|
    |主机名或IP地址|填入Amazon Aurora for PostgreSQL的连接地址。 **说明：** 您可以在Amazon Aurora for PostgreSQL的基本信息页面，获取数据库的连接信息。

 ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150145/156255173241853_zh-CN.png)

|
    |端口|填入Amazon Aurora for PostgreSQL的服务端口，默认为**5432**。|
    |数据库名称|填入Amazon Aurora for PostgreSQL中待迁移的数据库名。|
    |数据库账号|填入AWS RDS for PostgreSQL数据库的连接账号，权限要求请参见[迁移账号权限要求](#section_eby_wfq_fhb)。|
    |数据库密码|填入AWS RDS for PostgreSQL数据库账号对应的密码。|
    |目标实例信息|实例类型|选择**RDS实例**。|
    |实例地区|选择迁移目标阿里云RDS for PostgreSQL所在地区。|
    |RDS实例ID|选择阿里云RDS for PostgreSQL实例ID。|
    |数据库名称|填入阿里云RDS for PostgreSQL中迁移的目标数据库名。|
    |数据库账号|填入连接阿里云RDS for PostgreSQL数据库的账号，权限要求请参见[迁移账号权限要求](#section_eby_wfq_fhb)。|
    |数据库密码|填入阿里云RDS for PostgreSQL数据库账号对应的密码。|

5.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会自动将DTS服务器的IP地址自动添加阿里云RDS for PostgreSQL的白名单中，用于保障DTS服务器能够正常连接。迁移完成后如不再需要可手动删除，详情请参见[白名单设置](https://help.aliyun.com/document_detail/43187.html)。

6.  选择**迁移对象**和**迁移类型**。

    ![配置迁移对象和类型](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150145/156255173241890_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|同时勾选**结构迁移**和**全量数据迁移**。 **说明：** 为保障数据一致性，迁移期间请勿在Amazon Aurora for PostgreSQL数据库中写入新的数据。

 |
    |迁移对象|     -   在迁移对象框中将想要迁移的数据库选中，单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156255173340698_zh-CN.png)移动到已选择对象框。
    -   迁移对象选择的粒度可以为、表、列三个粒度。
    -   默认情况下，对象迁移到阿里云RDS for PostgreSQL数据库后，对象名与源数据库中的迁移对象一致。 如果您迁移的对象在目标数据库中名称不同，那么需要使用DTS提供的对象名映射功能，使用方法请参见[库表列映射](cn.zh-CN/用户指南/数据迁移/库表列映射.md#)。

**说明：** 如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。

 |

7.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156255173341056_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
8.  预检查通过后，单击**下一步**。
9.  在购买配置确认页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
10. 单击**购买并启动**，迁移任务正式开始。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150145/156255173341894_zh-CN.png) 

    **说明：** 请勿手动结束任务，否则可能会造成迁移数据丢失，等待迁移状态显示为**已完成**，迁移任务将自动停止。

11. 将业务切换至阿里云RDS for PostgreSQL。


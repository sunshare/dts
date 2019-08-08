# 从Amazon RDS for Oracle迁移至阿里云RDS for MySQL {#concept_354108 .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将Amazon RDS for Oracle迁移至阿里云RDS for MySQL。DTS支持结构迁移、全量数据迁移以及增量数据迁移，同时使用这三种迁移类型可以实现在自建应用不停服的情况下，平滑地完成数据库迁移。

## 前提条件 {#section_8vl_bru_fm7 .section}

-   为保障DTS能够通过公网连接至Amazon RDS for Oracle，需要将Amazon RDS for Oracle的**公开可用性**设置为**是**。
-   Amazon RDS for Oracle的版本为10g、11g或12c版本。
-   阿里云RDS for MySQL版本为5.6或5.7版本。
-   阿里云RDS for MySQL的存储空间是Amazon RDS for Oracle中待迁移数据占用的存储空间的两倍以上。

    **说明：** 迁移数据库时产生的Binlog将占用一定的空间，迁移完成后会自动清理。


## 注意事项 {#section_3e9_huw_b09 .section}

-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。
-   RDS for MySQL实例对表名的英文大小写不敏感，如果使用大写英文建表，RDS for MySQL会先把表名转为小写再执行建表操作。

    如果源Oracle数据库中存在表名相同仅大小写不同的表，可能会导致迁移对象重名并在结构迁移中提示“对象已经存在”。如果出现这种情况，请在配置迁移对象的时候，使用DTS提供的对象名映射功能对重名的对象进行重命名，详情请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。

-   如果待迁移的数据库在目标RDS for MySQL实例中不存在，DTS会自动创建。但是对于如下两种情况，用户需要在配置迁移任务之前在目标RDS for MySQL实例中[创建数据库](https://help.aliyun.com/document_detail/96105.html)。
    -   数据库名称不符合RDS定义规范，详细规范请参见[创建数据库](https://help.aliyun.com/document_detail/96105.html)。
    -   待迁移数据库在源Oracle数据库与目标RDS for MySQL实例中的名称不同。

## 费用说明 {#section_md8_hm9_8ox .section}

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|不收取|

## 迁移类型说明 {#section_w8i_cpf_jb6 .section}

-   结构迁移

    DTS支持结构迁移的对象为表、索引、约束、序列。不支持视图、同义词、触发器、存储过程、存储函数、包、自定义类型等。

-   全量数据迁移

    DTS会将Amazon RDS for Oracle数据库迁移对象的存量数据，全部迁移到目标RDS for MySQL实例数据库中 。

-   增量数据迁移

    在全量迁移的基础上，DTS会轮询并捕获Amazon RDS for Oracle数据库产生的redolog，将Amazon RDS for Oracle数据库的增量更新数据同步到目标RDS for MySQL实例数据库中。通过增量数据迁移可以实现在本地应用不停服的情况下，平滑地完成Oracle数据库的数据迁移工作。


## 增量数据迁移支持同步的SQL操作 {#section_s2x_f4m_1yb .section}

-   INSERT、DELETE、UPDATE
-   CREATE TABLE

    **说明：** 不支持分区表、表内定义包含函数的表。

-   ALTER TABLE，仅包含ADD COLUMN、DROP COLUMN、RENAME COLUMN和ADD INDEX
-   DROP TABLE
-   RENAME TABLE、TRUNCATE TABLE、CREATE INDEX

## 迁移账号权限要求 {#section_14w_e9e_1h5 .section}

|迁移数据源|结构迁移|全量迁移|增量数据迁移|
|:----|:---|:---|:-----|
|Amazon RDS for Oracle数据库|schema的owner权限|schema的owner权限|MASTER USER 具备的权限|
|RDS for MySQL实例|待迁入数据库的读写权限|待迁入数据库的读写权限|待迁入数据库的读写权限|

**数据库账号创建及授权方法：**

-   Amazon RDS for Oracle数据库请参见[CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm)和[GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm)。
-   RDS for MySQL实例请参见[创建账号](https://help.aliyun.com/document_detail/96089.html)和[修改账号权限](https://help.aliyun.com/document_detail/96101.html)。

## 数据类型映射关系 {#section_8mi_nzd_9af .section}

由于Oracle和MySQL的数据类型并不是一一对应的，所以DTS在进行结构迁移时，会根据数据类型定义进行类型映射，数据类型映射关系如下表所示。

|Oracle 数据类型|MySQL 数据类型|DTS 是否支持|
|:----------|:---------|:-------|
|varchar2\(n \[char/byte\]\)|varchar\(n\)|支持|
|nvarchar2\[\(n\)\]|national varchar\[\(n\)\]|支持|
|char\[\(n \[byte/char\]\)\]|char\[\(n\)\]|支持|
|nchar\[\(n\)\]|national char\[\(n\)\]|支持|
|number\[\(p\[,s\]\)\]|decimal\[\(p\[,s\]\)\]|支持|
|float\(p\)\]|double|支持|
|long|longtext|支持|
|date|datetime|支持|
|binary\_float|decimal\(65,8\)|支持|
|binary\_double|double|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]|datetime\[\(fractional\_seconds\_precision\)\]|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|支持|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|支持|
|clob|longtext|支持|
|nclob|longtext|支持|
|blob|longblob|支持|
|raw|varbinary\(2000\)|支持|
|long raw|longblob|支持|
|bfile|-|**不支持**|
|interval year\(year\_precision\) to month|-|**不支持**|
|interval day\(day\_precision\)to second\[\(fractional\_seconds\_precision\)\]|-|**不支持**|

**说明：** 

-   对于char类型，当长度定义超过255时，DTS会将类型转换为varchar\(n\)。
-   由于MySQL本身不支持类似Oracle中的bfile、interval year to month和interval day tosecond数据类型，DTS在进行结构迁移时，无法在MySQL中找到合适的数据类型进行映射，因此这三种类型不会进行转化。

    迁移时如果表中含有这三种类型，会导致结构迁移失败，在选择迁移对象时，对需要迁移的对象中这三种类型的列进行排除。

-   由于MySQL的timestamp类型不包含时区，而Oracle的timestamp with time zone和timestamp with local time zone默认带有时区信息，DTS在迁移这两种类型的数据时，会将其转换成UTC时区后存入目标RDS for MySQL实例。
-   由于MySQL存在Row Size的限制，迁移过程中可能会出现部分表无法被迁移的情况。如果遇到此类情况，请在迁移对象中排除这些表，或者调整该表中的字段使其符合MySQL的要求。

## 数据迁移前准备工作 {#section_rpp_s1n_ni7 .section}

1.  登录Amazon RDS控制台。
2.  进入Amazon RDS for Oracle实例的基本信息页面。
3.  在安全组规则区域框，单击入站规则对应的安全组名称。

    ![安全组规则](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150145/156523504841899_zh-CN.png)

4.  在安全组设置页面，将对应区域的DTS服务器地址添加至入站规则中，IP地址段详情请参见[DTS IP地址段](https://help.aliyun.com/document_detail/84900.html)。

    **说明：** 

    -   您只需添加目标数据库所在区域对应的DTS IP地址段。本案例中，源数据库地区为新加坡，目标数据库地区为杭州，您只需要添加杭州地区的DTS IP地址段。
    -   在加入IP地址段时，您可以一次性添加所需的IP地址，无需逐条添加入站规则。
    ![编辑AWS入站规则](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/287980/156523504847942_zh-CN.png)

5.  调整Amazon RDS for Oracle日志配置。如您不需要**增量数据迁移**可跳过本步骤。
    1.  使用Master User账号，通过SQL\*Plus工具连接Amazon RDS for Oracle数据库。
    2.  执行`archive log list;`命令，确认Amazon RDS for Oracle已经处于归档状态。

        **说明：** 如果该实例尚处于非归档状态，请打开归档，详情请参见[Managing Archived Redo Logs](https://docs.oracle.com/cd/E11882_01/server.112/e25494/archredo.htm#ADMIN008)。

    3.  打开强制日志模式。

        ``` {#codeblock_j30_tln_my5}
        exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);
        ```

    4.  打开主键附加日志。

        ``` {#codeblock_00y_w7j_bnk}
        begin rdsadmin.rdsadmin_util.alter_supplemental_logging(p_action => 'ADD',p_type => 'PRIMARY KEY');end;/
        ```

    5.  打开唯一键附加日志。

        ``` {#codeblock_m1e_ckd_4s1}
        begin rdsadmin.rdsadmin_util.alter_supplemental_logging(p_action => 'ADD',p_type => 'UNIQUE');end;/
        ```

    6.  设置归档日志的保存周期。

        **说明：** 建议归档日志的保存周期至少设置为24个小时。

        ``` {#codeblock_0dj_app_pzd}
        begin rdsadmin.rdsadmin_util.set_configuration(name => 'archivelog retention hours', value => '24');end;/
        ```

    7.  提交修改。

        ``` {#codeblock_br1_yzc_oa2}
        commit;
        ```


## 操作步骤 {#section_tm5_nqk_12x .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  单击页面右上角的**创建迁移任务**。
4.  配置迁移任务的**源库及目标库**信息。

    ![源库和目标库连接配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156523504847598_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**有公网IP的自建数据库**。|
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入Amazon RDS for Oracle的入站规则中，详情请参见[数据迁移前准备工作](#section_rpp_s1n_ni7)。

 |
    |数据库类型|选择**Oracle**。|
    |主机名或IP地址|填入Amazon RDS for Oracle数据库的访问地址。 **说明：** 您可以在Amazon RDS for Oracle的基本信息页面，获取数据库的连接信息。

 ![连接地址](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/287980/156523504847946_zh-CN.png)

|
    |端口|填入Amazon RDS for Oracle数据库的服务端口，默认为**1521**。|
    |实例类型|     -   非RAC实例：选择该项后，您还需要填写**SID**信息。
    -   RAC实例：选择该项后，您还需要填写**ServiceName**信息。
 本案例选择为非RAC实例并填写SID信息。

 |
    |数据库账号|填入Amazon RDS for Oracle数据库的连接账号，权限要求请参见[迁移账号权限要求](#section_14w_e9e_1h5)。|
    |数据库密码|填入Amazon RDS for Oracle数据库账号对应的密码。 **说明：** 源库信息完全填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择目标RDS实例所属地域。|
    |RDS实例ID|选择目标RDS实例ID。|
    |数据库账号|填入连接目标RDS实例数据库的账号，权限要求请参见[迁移账号权限要求](#section_14w_e9e_1h5)。|
    |数据库密码|填入连接目标RDS实例数据库账号对应的密码。 **说明：** 目标库信息完全填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

5.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标RDS实例的白名单中，用于保障DTS服务器能够正常连接目标RDS实例。迁移完成后如不再需要可手动删除，详情请参见[设置白名单](https://help.aliyun.com/document_detail/43185.html)。

6.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156523504947602_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，在迁移类型选择时勾选**结构迁移**和**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在Amazon RDS for Oracle数据库中写入新的数据。

    -   如果需要进行不停机迁移，在迁移类型选择时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。
 |
    |迁移对象| 在迁移对象框中将想要迁移的数据库选中，单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156523504940698_zh-CN.png)移动到已选择对象框。

 **说明：** 

    -   迁移对象选择的粒度可以为库、表、列三个粒度。
    -   默认情况下，迁移完成后，迁移对象名跟Amazon RDS for Oracle数据库一致。如果您需要迁移对象在目标RDS实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
 |

7.  单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156523504947468_zh-CN.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
8.  预检查通过后，单击**下一步**。
9.  在购买配置确认页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
10. 单击**购买并启动**，迁移任务正式开始。
    -   全量数据迁移

        请勿手动停止迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动停止。

    -   增量数据迁移迁移任务不会自动结束，需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至RDS实例时。

        1.  观察迁移任务的状态显示为**增量迁移无延迟**的状态时，将源库停写几分钟，此时迁移任务的状态可能会显示延迟的时间。
        2.  等待增量迁移再次进入**增量迁移无延迟**状态，手动停止迁移任务。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156523504947604_zh-CN.png)

        3.  将业务切换至RDS实例。

## 后续操作 {#section_g9e_88u_81v .section}

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除Amazon RDS for Oracle数据库和RDS for MySQL实例中的数据库账号。


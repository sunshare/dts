# 从自建MySQL迁移至RDS for MySQL {#concept_268502 .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将自建MySQL迁移至RDS for MySQL实例。DTS支持结构迁移、全量数据迁移以及增量数据迁移，同时使用这三种迁移类型可以实现在自建应用不停服的情况下，平滑地完成自建MySQL数据库的迁移上云。

## 源库支持的实例类型 {#section_hwn_jf1_vhb .section}

执行数据迁移操作的MySQL数据库支持以下实例类型：

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库

本文以**有公网IP的自建数据库**为例介绍配置流程，当自建MySQL数据库为其他实例类型时，配置流程与该案例类似。

## 前提条件 {#section_830_ewm_05y .section}

-   自建MySQL数据库版本为5.1、5.5、5.6、5.7、8.0版本。
-   RDS for MySQL实例的存储空间须大于自建MySQL数据库占用的存储空间。
-   自建MySQL数据库的服务端口已开放至公网。

## 注意事项 {#section_pw1_w3w_n0l .section}

-   如果源数据库没有主键或唯一约束，且所有字段没有唯一性，可能会导致目标数据库中出现重复数据。
-   对于数据类型为FLOAT或DOUBLE的列，DTS会通过`ROUND(COLUMN,PRECISION)`来读取该列的值。如果没有明确定义其精度，DTS对FLOAT的迁移精度为38位，对DOUBLE的迁移精度为308位，请确认迁移精度是否符合业务预期。
-   如果待迁移的数据库在目标RDS for MySQL实例中不存在，DTS会自动创建。但是对于如下两种情况，您需要在配置迁移任务之前在目标RDS for MySQL实例中[创建数据库](https://help.aliyun.com/document_detail/96105.html)。
    -   数据库名称不符合RDS定义规范，详细规范请参见[创建数据库](https://help.aliyun.com/document_detail/96105.html)。
    -   待迁移数据库在源MySQL数据库与目标RDS for MySQL实例中的名称不同。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例，请务必先终止或释放该任务，避免该任务被自动恢复后，导致源端数据覆盖目标实例的数据。

## 费用说明 {#section_1h5_oqk_ok3 .section}

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|不收取|

## 迁移类型说明 {#section_mjh_vxq_5hb .section}

-   结构迁移

    DTS将迁移对象的结构定义迁移到目标实例，目前DTS支持结构迁移的对象为表、视图、触发器、存储过程、存储函数，不支持event的结构迁移。

    **说明：** 

    -   在结构迁移时，DTS会将视图、存储过程和函数中的DEFINER转换为INVOKER。
    -   由于DTS不迁移user信息，因此在调用目标库的视图、存储过程和函数时需要对调用者授予读写权限。
-   全量数据迁移

    DTS会将自建MySQL数据库迁移对象的存量数据，全部迁移到目标RDS for MySQL实例数据库中。

    **说明：** 

    -   由于全量数据迁移会并发INSERT导致目标实例的表存在碎片，全量迁移完成后目标实例的表空间会比源实例大。
    -   为保障数据一致性，全量数据迁移期间请勿在自建MySQL数据库中写入新的数据。
-   增量数据迁移

    在全量迁移的基础上，DTS会读取自建MySQL数据库的binlog信息，将自建MySQL数据库的增量更新数据同步到目标RDS for MySQL实例中。通过增量数据迁移可以实现在自建应用不停服的情况下，平滑地完成MySQL数据库的迁移上云。


## 增量数据迁移支持同步的SQL操作 {#section_et9_esk_7dn .section}

-   Insert、Update、Delete、Replace
-   ALTER TABLE、ALTER VIEW、ALTER FUNCTION、ALTER PROCEDURE
-   CREATE DATABASE、CREATE SCHEMA、CREATE INDEX、CREATE TABLE、CREATE PROCEDURE、CREATE FUNCTION、CREATE TRIGGER、CREATE VIEW、CREATE EVENT
-   DROP FUNCTION、DROP EVENT、DROP INDEX、DROP PROCEDURE、DROP TABLE、DROP TRIGGER、DROP VIEW
-   RENAME TABLE、TRUNCATE TABLE

## 数据库账号的权限要求 {#section_bjn_5zq_5hb .section}

|迁移数据源|结构迁移|全量迁移|增量迁移|
|:----|:---|:---|:---|
|自建MySQL实例|select权限|select权限|select、replication slave和replication client权限|
|RDS for MySQL实例|读写权限|读写权限|读写权限|

**数据库账号创建及授权方法：**

-   自建MySQL数据库请参见[数据迁移前准备工作](#section_xzp_eb2_x9q)。
-   RDS for MySQL实例请参见[创建账号](https://help.aliyun.com/document_detail/96089.html)和[修改账号权限](https://help.aliyun.com/document_detail/96101.html)。

## 数据迁移前准备工作 {#section_xzp_eb2_x9q .section}

在正式配置数据迁移任务之前，需要在自建MySQL数据库上进行账号与Binlog的配置。

1.  在自建MySQL数据库中创建用于数据迁移的账号。

    ``` {#codeblock_0fp_jze_mw5}
    CREATE USER 'username'@'host' IDENTIFIED BY 'password';
    ```

    参数说明：

    -   username：要创建的账号名称。
    -   host：指定该账号登录数据库的主机。如果是本地用户可以使用localhost，如需该用户从任意主机登录，可以使用百分号（%）。
    -   password：该账号的登录密码。
    例如，创建账号为dtsmigration，密码为Dts123456的账号从任意主机登录本地数据库，命令如下。

    ``` {#codeblock_7xp_luo_gw6}
    CREATE USER 'dtsmigration'@'%' IDENTIFIED BY 'Dts123456';
    ```

    **说明：** 更多信息请参见[CREATE USER](https://dev.mysql.com/doc/refman/5.7/en/create-user.html)语法说明。

2.  给用于数据迁移的数据库账号进行授权操作。

    ``` {#codeblock_n5m_iyq_3sv}
    GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
    ```

    参数说明：

    -   privileges：该账号的操作权限，如SELECT、INSERT、UPDATE等。如果要授权该账号所有权限，则使用ALL。
    -   databasename：数据库名。如果要授权该账号所有的数据库权限，则使用星号（\*）。
    -   tablename：表名。如果要授权该账号所有的表权限，则使用星号（\*）。
    -   username：要授权的账号名。
    -   host：授权登录数据库的主机名。如果是本地用户可以使用localhost，如果想让该用户从任意主机登录，可以使用百分号（%）。
    -   WITH GRANT OPTION：授权该账号使用GRANT命令，该参数为可选。
    例如，授权账号dtsmigration对所有数据库和表的所有权限，并可以从任意主机登录本地数据库，命令如下。

    ``` {#codeblock_kh1_8yb_5ow}
    GRANT ALL ON *.* TO 'dtsmigration'@'%';
    ```

    **说明：** 更多信息请参见[GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html?spm=a2c4g.11186623.2.20.42f069a7RdYBer)语法说明。

3.  开启自建的MySQL数据库的binlog，如您不需要**增量数据迁移**可跳过本步骤。

    **说明：** 您可以使用如下命令查询数据库是否开启了binlog。如果查询结果为 log\_bin=ON，那么数据库已开启binlog，您可以跳过本步骤。

    ``` {#codeblock_t5z_znj_c6m}
    show global variables like "log_bin";
    ```

    1.  修改配置文件my.cnf中的如下参数。

        ``` {#codeblock_nts_hmz_xvb}
        log_bin=mysql_bin
        binlog_for Mat=row
        server_id=2 //大于1的整数。
        binlog_row_image=full //当本地 MySQL 版本大于 5.6 时，则需设置该项。
        ```

    2.  修改完成后，重启MySQL进程。

        ``` {#codeblock_ddy_t01_5b6}
        $mysql_dir/bin/mysqladmin -u root -p shutdown
        $mysql_dir/bin/safe_mysqld &
        ```

        **说明：** 将`mysql_dir`替换为您MySQL实际的安装目录。


## 操作步骤 {#section_sff_dcr_5hb .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711733/156523475550439_zh-CN.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![源库和目标库连接配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17105/156523475547746_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**有公网IP的自建数据库**。|
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 如果您的自建MySQL数据库进行了白名单安全设置，您需要在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入自建MySQL数据库的白名单安全设置中。

 |
    |数据库类型|选择**MySQL**。|
    |主机名或IP地址|填入自建MySQL数据库的访问地址，本案例中填入公网地址。|
    |端口|填入自建MySQL数据库的服务端口，默认为**3306**。|
    |数据库账号|填入自建MySQL数据库的连接账号，权限要求请参见[数据库账号的权限要求](#section_bjn_5zq_5hb)。|
    |数据库密码|填入自建MySQL数据库账号对应的密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**RDS实例**。|
    |实例地区|选择目标RDS实例所属地域。|
    |RDS实例ID|选择目标RDS实例ID。|
    |数据库账号|填入连接目标RDS实例数据库的账号，权限要求请参见[数据库账号的权限要求](#section_bjn_5zq_5hb)。|
    |数据库密码|填入连接目标RDS实例数据库账号对应的密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**，本案例选择为**非加密连接**。 **说明：** 选择**SSL安全连接**时，需要提前开启RDS实例的SSL加密功能，详情请参见[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。

 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标RDS实例的白名单中，用于保障DTS服务器能够正常连接目标RDS实例。迁移完成后如不再需要可手动删除，详情请参见[设置白名单](https://help.aliyun.com/document_detail/43185.html)。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17105/156523475547745_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，则同时勾选**结构迁移**和**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建MySQL数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**结构迁移**、**全量数据迁移**和**增量数据迁移**。
 |
    |迁移对象| 在迁移对象框中单击待迁移的对象，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156523475640698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 

    -   迁移对象选择的粒度可以为库、表、列三个粒度。
    -   默认情况下，迁移完成后，迁移对象名跟自建MySQL数据库一致。如果您需要迁移对象在目标RDS实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参见[库表列映射](https://help.aliyun.com/document_detail/26628.html)。
    -   如果使用了对象名映射功能，可能会导致依赖这个对象的其他对象迁移失败。
 |

8.  单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156523475647468_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
9.  预检查通过后，单击**下一步**。
10. 在购买配置确认页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
11. 单击**购买并启动**，迁移任务正式开始。
    -   全量数据迁移

        请勿手动结束迁移任务，否则可能会导致数据不完整。您只需等待迁移任务完成即可，迁移任务会自动结束。

    -   增量数据迁移

        迁移任务不会自动结束，您需要手动结束迁移任务。

        **说明：** 请选择合适的时间手动结束迁移任务，例如业务低峰期或准备将业务切换至目标实例时。

        1.  观察迁移任务的进度变更为**增量迁移**，并显示为**无延迟**状态时，将源库停写几分钟，此时**增量迁移**的状态可能会显示延迟的时间。
        2.  等待迁移任务的**增量迁移**再次进入**无延迟**状态后，手动结束迁移任务。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156523475647604_zh-CN.png)

12. 将业务切换至RDS实例。

## 后续操作 {#section_fit_to5_l8p .section}

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，删除自建MySQL数据库和RDS for MySQL实例中的数据库账号。


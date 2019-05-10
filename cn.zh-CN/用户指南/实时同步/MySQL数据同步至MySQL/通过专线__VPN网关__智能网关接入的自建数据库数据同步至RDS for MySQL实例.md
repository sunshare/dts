# 通过专线/VPN网关/智能网关接入的自建数据库数据同步至RDS for MySQL实例 {#concept_66730_zh .concept}

[数据传输服务](https://dts.console.aliyun.com/)（Data Transmission Service，简称DTS）支持通过专线/VPN网关/智能网关接入的自建MySQL数据同步至RDS for MySQL实例，实现增量数据的实时同步。

## 前提条件 {#section_spx_dpj_dhb .section}

-   数据同步的目标RDS实例已存在，如不存在请[创建RDS实例](https://help.aliyun.com/document_detail/26117.html)。
-   自建MySQL数据库版本为5.1、5.5、5.6、5.7版本。
-   已经将自建MySQL数据库通过专线/VPN网关/智能网关接入至阿里云专有网络，详情请参考[连接本地IDC](https://help.aliyun.com/document_detail/99972.html)。

    **说明：** 接入至阿里云专有网络后，还需要放通DTS的IP地址访问自建数据库所属的网络，详情请参考[放通DTS访问通过专线/VPN网关/智能网关接入的网络](cn.zh-CN/.md#)。


## 注意事项 {#section_lxf_c6c_12j .section}

-   暂不支持香港可用区A的RDS for MySQL实例配置数据同步。
-   目标实例不支持访问模式为标准模式且只有外网连接地址的RDS for MySQL实例。
-   全量初始化过程中，并发insert导致目标实例的表碎片，全量初始化完成后，目标实例的表空间比源实例的表空间大。
-   为保证同步延迟显示的准确性，DTS会在源实例新增一张心跳表，心跳表的表名为：`_##dts_mysql_heartbeat##_`。
-   DTS暂不支持XA Transaction，当同步过程中遇到XA Transaction会导致同步失败。

## 支持的同步架构 {#section_obd_dw3_dhb .section}

-   一对一单向同步
-   一对多单向同步
-   多对一单向同步
-   级联单向同步
-   一对一双向同步

    **说明：** 如需实现双向同步，请参考[创建RDS for MySQL实例间的双向数据同步](cn.zh-CN/用户指南/实时同步/MySQL数据同步至MySQL/创建RDS for MySQL实例间的双向数据同步.md#)。


## 支持同步语法 {#section_dsz_1w3_dhb .section}

RDS for MySQL实例的数据同步支持所有DML语法和部分DDL语法的同步，支持的DDL语法如下。

-   ALTER TABLE、ALTER VIEW、ALTER FUNCTION、ALTER PROCEDURE。
-   CREATE DATABASE、CREATE SCHEMA、CREATE INDEX、CREATE TABLE、CREATE PROCEDURE、CREATE FUNCTION、CREATE TRIGGER、CREATE VIEW、CREATE EVENT。
-   DROP FUNCTION、DROP EVENT、DROP INDEX、DROP PROCEDURE、DROP TABLE、DROP TRIGGER、DROP VIEW。
-   RENAME TABLE、TRUNCATE TABLE。

## 功能限制 {#section_mkt_dw3_dhb .section}

-   不兼容触发器

    同步对象为整个库且这个库中包含了会更新同步表内容的触发器，那么可能导致同步数据不一致。例如数据库中存在了两个表A和B。表A上有一个触发器，触发器内容为在insert一条数据到表A之后，在表B中插入一条数据。这种情况在同步过程中，如果源实例表A上进行了Insert操作，则会导致表B在源实例跟目标实例数据不一致。

    此类情况须要将目标实例中的对应触发器删除掉，表B的数据由源实例同步过去，详情请参考[触发器存在情况下如何配置同步作业](https://help.aliyun.com/document_detail/26655.html) 。

-   rename table限制

    rename table操作可能导致同步数据不一致。例如同步对象只包含表A，如果同步过程中源实例将表A重命名为表B，那么表B将不会被同步到目标库。为避免该问题，您可以在数据同步配置时，选择同步表A和表B所在的整个数据库作为同步对象。


## 数据同步前准备工作 {#section_h5w_rdl_zgb .section}

在正式操作数据同步之前，需要在自建MySQL数据库上进行账号与Binlog的配置。

1.  在自建MySQL数据库中创建用于数据同步的账号。

    ```
    CREATE USER 'username'@'host' IDENTIFIED BY 'password';
    ```

    参数说明：

    -   username：要创建的账号。
    -   host：指定该账号登录数据库的主机。如果是本地用户可以使用 localhost，如需该用户从任意主机登录，可以使用百分号（%）。
    -   password：该账号的登录密码。
    例如，创建账号为dtsmigration，密码为Dts123456的账号从任意主机登录本地数据库，命令如下。

    ```
    CREATE USER 'dtsmigration'@'%' IDENTIFIED BY 'Dts123456';
    ```

2.  给用于数据同步的数据库账号进行授权操作。

    ```
    GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
    ```

    参数说明：

    -   privileges：该账号的操作权限，如SELECT、INSERT、UPDATE等。如果要授权该账号所有权限，则使用ALL。
    -   databasename：数据库名。如果要授权该账号所有的数据库权限，则使用星号（\*）。
    -   tablename：表名。如果要授权该账号所有的表权限，则使用星号（\*）。
    -   username：要授权的账号名。
    -   host：授权登录数据库的主机名。如果是本地用户可以使用 localhost，如果想让该用户从任意主机登录，可以使用百分号（%）。
    -   WITH GRANT OPTION：授权该账号能使用GRANT命令，该参数为可选。
    例如，授权账号dtsmigration对所有数据库和表的所有权限，并可以从任意主机登录本地数据库，命令如下。

    ```
    GRANT ALL ON *.* TO 'dtsmigration'@'%';
    ```

3.  开启自建的MySQL数据库的binlog。

    **说明：** 您可以使用如下命令查询数据库是否开启了binlog。如果查询结果为 log\_bin=ON，那么数据库已开启binlog，您可以跳过本步骤。

    ```
    show global variables like "log_bin";
    ```

    1.  修改配置文件my.cnf中的如下参数。

        ```
        log_bin=mysql_bin
        binlog_format=row
        server_id=大于 1 的整数
        binlog_row_image=full //当本地 MySQL 版本大于 5.6 时，则需设置该项。
        ```

    2.  修改完成后，重启MySQL进程。

        ```
        $mysql_dir/bin/mysqladmin -u root -p shutdown
        $mysql_dir/bin/safe_mysqld &
        ```

        **说明：** “mysql\_dir”替换为您MySQL实际的安装目录。


## 操作步骤一 购买数据同步实例 {#section_rfw_tpl_qhb .section}

1.  登录[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据同步**。
3.  在页面右上角，单击**创建同步作业**。
4.  在数据传输服务购买页面，选择付费类型为**预付费**或**按量付费**。

    -   预付费：属于预付费，即在新建实例时需要支付费用。适合长期需求，价格比按量付费更实惠，且购买时长越长，折扣越多。
    -   按量付费：属于后付费，即按小时扣费。适合短期需求，用完可立即释放实例，节省费用。
    **说明：** 关于产品价格，请参考[DTS产品定价](https://help.aliyun.com/document_detail/90770.html)。

5.  选择数据同步实例的参数配置信息，参数说明如下表所示。

    |参数配置区|参数项|说明|
    |:----|:--|:-|
    |基本配置|功能|选择**数据同步**。|
    |源实例|选择**MySQL**。|
    |源实例地域|选择数据同步链路中源RDS实例的地域。 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |目标实例|选择**MySQL**。|
    |目标实例地域|选择数据同步链路中目标RDS实例的地域。 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |同步拓扑|数据同步支持的拓扑类型，选择**单向同步**。 **说明：** 如需实现双向同步，请参考[创建RDS for MySQL实例间的双向数据同步](cn.zh-CN/用户指南/实时同步/MySQL数据同步至MySQL/创建RDS for MySQL实例间的双向数据同步.md#)。

 |
    |网络类型|数据同步服务使用的网络类型，目前固定为**专线**。|
    |同步链路规格|数据传输为您提供了不同性能的链路规格，以同步的记录数为衡量标准。详情请参考[数据同步规格说明](https://help.aliyun.com/document_detail/26605.html)。|
    |购买量|购买数量|一次性购买数据同步实例的数量，默认为1，如果购买的是按量付费实例，一次最多购买 99 条链路。|

6.  单击**立即购买**，根据提示完成支付流程。

## 操作步骤二 配置数据同步 {#section_nvf_vpl_qhb .section}

1.  登录[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据同步**。
3.  定位至已购买的数据同步实例，单击该实例的**配置同步链路**。

    ![配置MySQL单向同步任务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17124/155747037946141_zh-CN.png)

4.  配置同步通道的源实例及目标实例信息。

    ![MySQL单向同步源目实例信息配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17126/155747037946162_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**通过专线/VPN网关/智能网关接入的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |对端专有网络|选择自建数据库接入的VPC ID。|
    |数据库类型|购买数据同步实例时选择的数据库类型：MySQL，不可变更。|
    |IP地址|填入自建MySQL数据库的服务器IP地址。|
    |端口|填入自建MySQL数据库的服务端口，默认为**3306**。|
    |数据库账号|填入自建MySQL数据库的账号，需要具备 Replication slave, Replication client 及所有同步对象的 Select 权限。|
    |数据库密码|填入自建MySQL数据库账号对应的密码。|
    |目标实例信息|实例类型|选择**RDS实例**。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择作为数据同步目标的RDS实例ID。|
    |数据库账号|填入目标RDS的数据库账号。 **说明：** 当目标RDS实例的数据库类型为**MySQL 5.5**或**MySQL 5.6**时，无需配置**数据库账号**和**数据库密码**。

 |
    |数据库密码|填入数据库账号对应的密码。|
    |连接方式|根据需求选择**非加密连接**或**SSL安全连接**，本案例选择为**非加密连接**。 **说明：** 选择 **SSL安全连接**时，需要提前开启RDS实例的SSL加密功能，详情请参考[设置SSL加密](https://help.aliyun.com/document_detail/96120.html)。

 |

5.  单击页面右下角的**授权白名单并进入下一步**。
6.  配置同步策略及对象信息。

    ![MySQL单向同步配置同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17124/155747037946145_zh-CN.png)

    |配置项目|配置说明|
    |:---|:---|
    |选择同步对象| 同步对象的选择粒度为库、表。

     -   如果选择整个库作为同步对象，那么该库中所有对象的结构变更操作都会同步至目标库。
    -   如果选择某个表作为同步对象，那么只有这个表的DROP/ALTER/TRUNCATE/RENAME TABLE、CREATE/DROP INDEX操作会同步至目标库。
 **说明：** 默认情况下，同步对象的名称保持不变。如果您需要同步对象在目标实例上名称不同，那么需要使用DTS提供的对象名映射功能，详情请参考[库表列映射](https://help.aliyun.com/document_detail/26628.html)。

 |

7.  上述配置完成后单击页面右下角的**下一步**。
8.  配置同步初始化的高级配置信息。

    ![数据同步高级设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/155747037941055_zh-CN.png)

    -   此步骤会将源实例中已经存在同步对象的结构及数据在目标实例中初始化，作为后续增量同步数据的基线数据。
    -   同步初始化类型细分为：结构初始化，全量数据初始化。默认情况下，需要选择**结构初始化**和**全量数据初始化**。
9.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步任务。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/155747037941056_zh-CN.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
10. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，该同步作业的同步任务正式开始。
11. 等待该同步作业的链路初始化完成，直至状态处于**同步中**。

    您可以在 数据同步页面，查看数据同步状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/155747038041059_zh-CN.png)



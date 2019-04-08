# 从本地MongoDB数据库迁移至云数据库MongoDB {#concept_26625_zh .concept}

本文介绍如何使用[数据传输服务DTS](https://help.aliyun.com/document_detail/26592.html)（Data Transmission Service），将本地自建的MongoDB数据库数据迁移至云数据库MongoDB中。DTS支持全量数据迁移及增量数据迁移MongoDB数据库，使用增量数据迁移可以实现在本地应用不停服的情况下，平滑地完成MongoDB数据库的迁移上云工作。

## 前提条件 {#section_tql_qpw_pgb .section}

-   本文操作适用于副本集架构的本地MongoDB数据库。

    分片集群架构请参考[本地MongoDB分片集群数据库迁移上云](../../../../../cn.zh-CN/分片集群快速入门/数据迁移/使用DTS工具迁移分片集群数据库上云.md#)或[使用 mongodump 和 mongorestore 工具迁移数据库](../../../../../cn.zh-CN/分片集群快速入门/数据迁移/使用MongoDB工具迁移自建数据库上云.md#)。

    单节点架构请参考[使用DTS工具迁移单节点架构的自建MongoDB数据库上云](../../../../../cn.zh-CN/单节点快速入门/数据迁移/使用DTS工具迁移单节点架构的自建MongoDB数据库上云.md#)。

-   本地MongoDB数据库的服务端口已开放至公网。
-   本地MongoDB数据库版本为3.2、3.4或3.6版本，暂不支持4.0版本。4.0版本的本地MongoDB数据库请参考[使用DTS工具迁移单节点架构的自建MongoDB数据库上云](../../../../../cn.zh-CN/单节点快速入门/数据迁移/使用DTS工具迁移单节点架构的自建MongoDB数据库上云.md#)。
-   阿里云MongoDB实例的存储空间须大于本地MongoDB数据库占用的存储空间。

## 注意事项 {#section_a3z_x11_kfb .section}

-   为避免影响您的正常业务使用，请在业务低峰期进行数据迁移。
-   阿里云MongoDB实例支持的数据库版本为3.2、3.4和4.0版本。
-   阿里云MongoDB实例支持的存储引擎为 WiredTiger、RocksDB 和 TerarkDB。
-   不支持迁移admin数据库，即使被选择为迁移对象，该库中的数据也不会被迁移。

## 费用说明 {#section_m3c_t5f_cgb .section}

|迁移类型|链路配置费用|公网流量费用|
|:---|:-----|:-----|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参考[DTS产品定价](https://cn.aliyun.com/price/product#/dts/detail)。|不收取|

## 迁移类型说明 {#section_zjf_5fs_zfb .section}

-   全量数据迁移：将本地MongoDB数据库迁移对象的存量数据，全部迁移到目标MongoDB实例数据库中。
    -   支持database迁移。
    -   支持collection迁移。
    -   支持index迁移。
-   增量数据迁移：在全量迁移的基础上，将本地MongoDB数据库的增量更新数据同步到目标MongoDB实例数据库中。
    -   支持database新建、删除操作的同步。
    -   支持document新增、删除、更新操作的同步。
    -   支持collection新建、删除操作的同步。
    -   支持index新建、删除操作的同步。

## 迁移账号权限要求 {#section_kvw_kb1_kfb .section}

使用DTS迁移MongoDB数据时，选用不同的迁移类型所需的账号权限不同，具体如下。

|数据迁移对象|全量数据迁移|增量数据迁移|
|:-----|:-----|------|
|本地MongoDB数据库|read| -   待迁移库的read
-   admin的read权限
-   local的read权限

 |
|阿里云MongoDB数据库|readWrite|readWrite|

**说明：** 

-   关于MongoDB云数据库迁移账号的创建及授权操作请参考[使用DMS管理MongoDB数据库用户](../../../../../cn.zh-CN/用户指南/账号管理/使用DMS管理MongoDB数据库用户.md#)。
-   关于本地MongoDB数据库迁移账号的创建及授权操作请参考[MongoDB Create User说明](https://docs.mongodb.com/manual/reference/method/db.createUser/)。

## MongoDB数据迁移上云的操作步骤 {#section_njl_2c1_kfb .section}

1.  登录[DTS控制台](http://dts.aliyun.com)。
2.  在左侧导航栏，单击**数据迁移**。
3.  单击数据迁移页面右侧的**创建迁移任务**。
4.  配置迁移任务的**源库及目标库**信息。

    ![MongoDB迁移源目数据库配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17111/155469347843466_zh-CN.png)

    |配置选项|配置说明|
    |:---|:---|
    |任务名称|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|     -   实例类型：选择**有公网IP的自建数据库**。
    -   数据库类型：选择**MongoDB**。
    -   主机名或IP地址：填入本地MongoDB数据库的访问地址，这个地址必须为公网地址。
    -   端口：填入本地MongoDB数据库的服务端口。
    -   数据库名称：填入本地MongoDB数据库中，用于账号验证的鉴权数据库名称。
    -   数据库账号：填入本地MongoDB数据库的连接账号，权限要求请参见[迁移账号权限要求](#section_kvw_kb1_kfb)。
    -   数据库密码：填入本地MongoDB数据库账号对应的密码。
 |
    |目标实例信息|     -   实例类型：选择**MongoDB实例**。
    -   实例地区：选择目标MongoDB实例所在地域。
    -   MongoDB实例ID：选择目标MongoDB实例的实例ID。
    -   数据库名称：填入目标MongoDB实例中，用于账号验证的鉴权数据库名称，默认为admin。
    -   数据库账号：填入连接目标MongoDB实例数据库的账号，权限要求请参见[迁移账号权限要求](#section_kvw_kb1_kfb)。
    -   数据库密码：填入连接目标MongoDB实例数据库账号对应的密码。
 |

5.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 

    -   此步骤会将DTS服务器的IP地址自动添加到目标MongoDB实例的白名单中，用于保障DTS服务器能够正常连接目标MongoDB实例。迁移完成后如不再需要可手动删除，详情请参考[白名单设置](cn.zh-CN/副本集快速入门/设置白名单.md#)。
    -   如果您的本地MongoDB数据库进行了白名单安全设置，您需要在**源库信息**栏目中，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入本地MongoDB数据库的白名单安全设置中。
6.  选择迁移对象及迁移类型。

    ![MongoDB迁移对象迁移类型选择](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17111/155469347843464_zh-CN.png)

    |配置选项|配置说明|
    |:---|:---|
    |迁移类型|     -   如果只需要进行全量迁移，那么迁移类型选择**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在本地MongoDB数据库中写入新的数据。

    -   如果需要进行不停机迁移，迁移类型同时选择**全量数据迁移**和**增量数据迁移**。
 |
    |迁移对象|     -   在**迁移对象**框中将想要迁移的数据库选中，单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/155469347840698_zh-CN.png)移动到**已选择对象**框。

**说明：** 不支持迁移admin数据库，即使被选择为迁移对象，该库中的数据也不会被迁移。

    -   迁移对象选择的粒度可以为：库、collection/function 两个粒度。
    -   默认情况下，迁移对象迁移到MongoDB实例后，对象名跟本地MongoDB数据库一致。

**说明：** 如果您迁移的对象在本地MongoDB数据库跟目标实例上名称不同，那么需要使用DTS提供的对象名映射功能。使用方法请参考[库表列映射](https://help.aliyun.com/document_detail/26628.html)。

 |

7.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![](https://dts.console.aliyun.com/styles/images/info.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
8.  预检查通过后，单击**下一步**。
9.  在购买配置确认页面，选择**链路规格**并勾选**数据传输（按量付费）服务条款**。
10. 单击**购买并启动**，迁移任务正式开始。
    -   全量数据迁移

        等待迁移任务完成即可，迁移任务会自动停止。

    -   增量数据迁移

        迁移任务不会自动结束，观察迁移任务的状态显示为**增量迁移无延迟**的状态时，将源库停写几分钟，等待增量迁移再次进入**增量迁移无延迟**状态，手动停止迁移任务。

        ![MongoDB增量迁移无延迟](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17111/155469347843467_zh-CN.png)


检查校验数据无误后即可将业务切换至云数据库MongoDB实例。

## 更多信息 {#section_v5g_lrw_pgb .section}

[MongoDB数据迁移方案概览](../../../../../cn.zh-CN/用户指南/数据迁移/MongoDB数据迁移方案概览.md#)


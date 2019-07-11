# 从自建Redis迁移至阿里云Redis {#concept_508426 .concept}

本文介绍如何使用数据传输服务DTS（Data Transmission Service），将自建Redis迁移至阿里云Redis实例。DTS支持全量数据迁移以及增量数据迁移，同时使用这两种迁移类型可以实现在自建应用不停服的情况下，平滑地完成自建Redis数据库的迁移上云。

## 源库支持的实例类型 {#section_qlg_vz2_zhb .section}

执行数据迁移操作的Redis数据库支持以下实例类型：

-   有公网IP的自建数据库
-   ECS上的自建数据库
-   通过专线/VPN网关/智能网关接入的自建数据库

本文以**有公网IP的自建数据库**介绍配置流程，当自建Redis数据库为其他实例类型时，配置流程与该案例类似。

## 前提条件 {#section_wtg_k1f_zhb .section}

-   自建Redis数据库版本为2.8、3.0、3.2或4.0版本。
-   自建Redis数据库可正常运行`psync`或`sync`命令。
-   阿里云Redis实例的存储空间须大于自建Redis数据库占用的存储空间。
-   自建Redis数据库的服务端口已开放至公网。

## 注意事项 {#section_gbm_hbf_zhb .section}

-   对于通过EVAL或EVALSHA调用的Lua脚本，在增量数据迁移时，由于目标端在执行脚本时不会明确返回执行结果，所以DTS无法确保该类型脚本是否执行成功。
-   对于List列表，由于DTS在调用`psync`或`sync`进行重传时，不会对目标端已有的数据执行`Flush`操作，所以可能出现重复的数据。
-   对于迁移失败的任务，DTS会触发自动恢复。当您需要将业务切换至目标实例时，请务必先结束或释放该任务，避免该任务被自动恢复后，导致源端数据覆盖目标实例的数据。

## 费用说明 {#section_jue_dyz_nfm .section}

|迁移类型|链路配置费用|公网流量费用|
|----|------|------|
|全量数据迁移|不收取|不收取|
|增量数据迁移|收取，费用详情请参见[产品定价](../../../../cn.zh-CN/产品定价/产品定价.md#)。|不收取|

## 迁移类型说明 {#section_n5m_p2f_zhb .section}

-   全量数据迁移

    DTS将自建Redis数据库迁移对象的存量数据，全部迁移到阿里云Redis实例中。

    **说明：** 为保障数据一致性，全量数据迁移期间请勿在自建Redis数据库中写入新的数据。

-   增量数据迁移

    在全量数据迁移的基础上，DTS将自建Redis数据库的增量更新数据到阿里云Redis实例中。通过增量数据迁移可以实现在应用不停服的情况下，平滑地完成Redis数据库的迁移上云。


## 增量数据迁移支持同步的命令 {#section_kbv_cff_zhb .section}

-   APPEND
-   BITOP、BLPOP、BRPOP、BRPOPLPUSH
-   DECR、DECRBY、DEL
-   EVAL、EVALSHA、EXEC、EXPIRE、EXPIREAT
-   FLUSHALL、FLUSHDB
-   GEOADD、GETSET
-   HDEL、HINCRBY、HINCRBYFLOAT、HMSET、HSET、HSETNX
-   INCR、INCRBY、INCRBYFLOAT
-   LINSERT、LPOP、LPUSH、LPUSHX、LREM、LSET、LTRIM
-   MOVE、MSET、MSETNX、MULTI
-   PERSIST、PEXPIRE、PEXPIREAT、PFADD、PFMERGE、PSETEX、PUBLISH
-   RENAME、RENAMENX、RESTORE、RPOP、RPOPLPUSH、RPUSH、RPUSHX
-   SADD、SDIFFSTORE、SELECT、SET、SETBIT、SETEX、SETNX、SETRANGE、SINTERSTORE、SMOVE、SPOP、SREM、SUNIONSTORE
-   ZADD、ZINCRBY、ZINTERSTORE、ZREM、ZREMRANGEBYLEX、ZUNIONSTORE、ZREMRANGEBYRANK、ZREMRANGEBYSCORE

## 增量数据迁移前的准备工作 {#section_grp_vcf_zhb .section}

为保障增量数据迁移任务的正常执行，建议对自建Redis数据库的配置进行调整。本文以Linux操作系统的服务器为例进行演示。

**说明：** 如您只须要进行全量数据迁移，可跳过本步骤。

1.  登录自建Redis数据库所属服务器。
2.  进入Redis文件夹中。

    ``` {#codeblock_t34_wxd_0tc}
    cd redis-4.0.14
    ```

3.  使用`vim`命令修改redis.conf配置文件。

    ``` {#codeblock_3zu_p7c_iwl}
    vim redis.conf
    ```

4.  在配置文件中，将`client-output-buffer-limit slave 256mb 64mb 60`替换为`client-output-buffer-limit slave 0 0 0`，并保存修改。
5.  重新启动Redis服务。

## 操作步骤 {#section_a5k_rff_zhb .section}

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据迁移**。
3.  在迁移任务列表页面顶部，选择迁移的目标实例所属地域。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/711733/156283188750439_zh-CN.png)

4.  单击页面右上角的**创建迁移任务**。
5.  配置迁移任务的源库及目标库信息。

    ![配置源库及目标库信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17112/156283188848773_zh-CN.png)

    |类别|配置|说明|
    |:-|:-|:-|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源库信息|实例类型|选择**有公网IP的自建数据库**。|
    |实例地区|当实例类型选择为**有公网IP的自建数据库**时，**实例地区**无需设置。 **说明：** 如果您的自建Redis数据库进行了白名单安全设置，您需要在**实例地区**配置项后，单击**获取DTS IP段**来获取到DTS服务器的IP地址，并将获取到的IP地址加入自建Redis数据库的白名单安全设置中。

 |
    |数据库类型|选择**Redis**。|
    |实例模式|固定为**单机版**，暂不支持集群版。|
    |主机名或IP地址|填入自建Redis数据库的访问地址，本案例中填入公网地址。|
    |端口|填入自建Redis数据库的服务端口，默认为**6379**。|
    |数据库密码|填入自建Redis数据库密码。 **说明：** 源库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的源库信息是否正确。源库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的源库信息。

 |
    |目标库信息|实例类型|选择**Redis实例**。|
    |实例地区|选择目标Redis实例所属地域。|
    |Redis实例ID|选择目标Redis实例ID。|
    |数据库密码|填入连接目标Redis实例的数据库密码。 **说明：** 目标库信息填写完毕后，您可以单击**数据库密码**后的**测试连接**来验证填入的目标库信息是否正确。目标库信息填写正确则提示**测试通过**，如提示**测试失败**，单击**测试失败**后的**诊断**，根据提示调整填写的目标库信息。

 |

6.  配置完成后，单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址自动添加到目标Redis实例的白名单中，用于保障DTS服务器能够正常连接目标Redis实例。迁移完成后如不再需要可手动删除，详情请参见[设置白名单](https://help.aliyun.com/document_detail/107043.html)。

7.  选择迁移对象及迁移类型。

    ![选择迁移类型和迁移对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17112/156283188848776_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |迁移类型|     -   如果只需要进行全量迁移，则勾选**全量数据迁移**。

**说明：** 为保障数据一致性，全量数据迁移期间请勿在自建Redis数据库中写入新的数据。

    -   如果需要进行不停机迁移，则同时勾选**全量数据迁移**和**增量数据迁移**。
 |
    |迁移对象| 在迁移对象框中单击待迁移的数据库，然后单击![向右小箭头](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156283188840698_zh-CN.png)将其移动至已选择对象框。

 **说明：** 迁移对象选择的粒度为库。

 |

8.  单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在迁移任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动迁移任务。
    -   如果预检查失败，单击具体检查项后的![提示](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156283188847468_zh-CN.png)，查看失败详情。根据失败原因修复后，重新进行预检查。
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

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156283188847604_zh-CN.png)

12. 将业务切换至Redis实例。

## 后续操作 {#section_2qu_igh_wrq .section}

用于数据迁移的数据库账号拥有读写权限，为保障数据库安全性，请在数据迁移完成后，修改自建Redis数据库和阿里云Redis实例中的数据库密码。


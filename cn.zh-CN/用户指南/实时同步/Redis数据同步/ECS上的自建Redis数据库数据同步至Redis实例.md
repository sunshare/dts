# ECS上的自建Redis数据库数据同步至Redis实例 {#concept_228260 .concept}

使用[数据传输服务](https://www.aliyun.com/product/dts)可以实现Redis数据库的单向同步。本文以ECS上的自建数据库数据同步至Redis实例为例，详细介绍数据同步任务的配置流程。

## 注意事项 {#section_uzt_h5s_qhb .section}

-   为保证同步链路稳定性，需要适当调大源Redis数据库中的repl-backlog-size配置。
-   为保证同步质量，DTS会在源Redis数据库中插入一个key：**DTS\_REDIS\_TIMESTAMP\_HEARTBEAT**，用于记录更新时间点。

## 支持的数据源 {#section_u4x_g5s_qhb .section}

|同步源数据库|同步目标数据库|
|:-----|:------|
| -   通过专线/VPN网关/智能网关接入的自建Redis数据库
-   ECS上的自建Redis数据库

 | -   Redis实例
-   通过专线/VPN网关/智能网关接入的自建Redis数据库
-   ECS上的自建Redis数据库

 |

**说明：** 同步的源和目标Redis版本为2.8、3.0、3.2或4.0。

## 支持的同步命令 {#section_bxj_j5s_qhb .section}

-   APPEND
-   BITOP, BLPOP, BRPOP, BRPOPLPUSH
-   DECR, DECRBY, DEL
-   EVAL, EVALSHA,EXEC, EXPIRE, EXPIREAT
-   GEOADD, GETSET
-   HDEL, HINCRBY, HINCRBYFLOAT, HMSET, HSET, HSETNX
-   INCR, INCRBY, INCRBYFLOAT
-   LINSERT, LPOP, LPUSH, LPUSHX, LREM, LSET, LTRIM
-   MOVE, MSET, MSETNX, MULTI
-   PERSIST, PEXPIRE, PEXPIREAT, PFADD, PFMERGE, PSETEX,PUBLISH
-   RENAME, RENAMENX, RESTORE,RPOP, RPOPLPUSH, RPUSH, RPUSHX
-   SADD, SDIFFSTORE, SELECT, SET, SETBIT, SETEX, SETNX, SETRANGE, SINTERSTORE, SMOVE, SPOP, SREM, SUNIONSTORE
-   ZADD, ZINCRBY, ZINTERSTORE, ZREM, ZREMRANGEBYLEX, ZUNIONSTORE, ZREMRANGEBYRANK, ZREMRANGEBYSCORE

**说明：** 

-   对于通过EVAL或者EVALSHA调用Lua脚本，在增量数据同步时，由于目标端在执行脚本时不会明确返回执行结果，DTS无法确保该类型脚本的执行成功。
-   对于List，由于DTS在调用sync或psync进行重传时，不会对目标端已有的数据进行清空，可能导致重复数据的出现。
-   当源端Redis集群的Node节点的版本为4.0时，还支持swapdb及unlink。

## 支持的同步架构 {#section_lhf_mf5_qhb .section}

-   一对一单向同步

    要求实例 B 中同步的对象必须为只读，否则会导致同步链路异常，出现数据不一致的情况。

-   一对多单向同步

    对目标Redis实例个数没有限制，但是要求目标实例中的同步对象必须为只读，否则会导致同步链路异常，出现数据不一致的情况。

    需要使用多个DTS同步实例支持，例如：需要实现实例A-\>实例B/实例C的数据同步，那么需要购买两个DTS同步实例，一个用于配置实例A-\>实例B的同步，另外一个用于配置实例A-\>实例C的数据同步。

-   级联单向同步

    需要使用多个DTS同步实例支持，例如：需要实现实例A-\>实例B-\>实例C的数据同步，那么需要购买两个DTS同步实例，一个用于配置实例A-\>实例B的数据同步，另外一个用于配置实例B-\>实例C的数据同步。


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
    |源实例|选择**Redis**。|
    |源实例地域|选择数据同步链路中源ECS实例所属地域。 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |目标实例|固定为**Redis**。|
    |目标实例地域|选择数据同步链路中目标Redis实例的地域。 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |同步拓扑|数据同步支持的拓扑类型，选择**单向同步**。|
    |网络类型|数据同步服务使用的网络类型，目前固定为**专线**。|
    |同步链路规格|数据传输为您提供了不同性能的链路规格，以同步的记录数为衡量标准。详情请参考[数据同步规格说明](https://help.aliyun.com/document_detail/26605.html)。 **说明：** 建议生产环境选择**small**及以上规格。

 |
    |购买量|购买数量|一次性购买数据同步实例的数量，默认为1，如果购买的是按量付费实例，一次最多购买 99 条链路。|

6.  单击**立即购买**，根据提示完成支付流程。

## 操作步骤二 配置数据同步 {#section_nvf_vpl_qhb .section}

1.  登录[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据同步**。
3.  定位至已购买的数据同步实例，单击该实例的**配置同步链路**。

    ![配置Redis单向同步任务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746968846323_zh-CN.png)

4.  配置同步通道的源实例及目标实例信息。

    ![同步源目实例信息配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190895/155746968846417_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**ECS上的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |ECS实例ID|选择作为同步数据源的ECS实例ID。|
    |数据库类型|固定为**Redis**。|
    |端口|填入自建Redis数据库的服务端口，默认为6379。|
    |数据库密码|填入自建Redis数据库的访问密码。 **说明：** 非必填项，如果没有设置密码可以不填。

 |
    |目标实例信息|实例类型|选择**Redis实例**。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择作为数据同步目标的Redis实例ID。|
    |数据库密码|填入Redis实例的访问密码。|

5.  单击页面右下角的**授权白名单并进入下一步**。
6.  配置同步对象。

    ![配置同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746968846332_zh-CN.png)

    **说明：** 

    -   在源库对象框中单击选择想要同步的数据库，单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/155746968840698_zh-CN.png)移动到已选择对象框。
    -   同步对象的选择粒度为库，暂不支持Key粒度的选择。
7.  上述配置完成后单击页面右下角的**下一步**。
8.  配置同步初始化的高级配置信息。

    ![Redis全量初始化](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746968846333_zh-CN.png)

    选择**全量数据初始化**，DTS会将源Redis数据库中的历史key同步至目标Redis数据库。

9.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步任务。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/155746968841056_zh-CN.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
10. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，该同步作业的同步任务正式开始。
11. 等待同步作业的链路初始化完成，直至状态处于**同步中**。

    您可以在 数据同步页面，查看数据同步状态。

    ![同步状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190895/155746968946419_zh-CN.png)



# 通过专线/VPN网关/智能网关接入的自建Redis集群数据同步至ECS上的自建Redis集群 {#concept_227929 .concept}

使用[数据传输服务](https://www.aliyun.com/product/dts)可以实现Redis集群的单向同步。本文以通过专线/VPN网关/智能网关接入的自建Redis集群数据同步至ECS上的自建Redis集群为例，介绍如何通过DTS实现Redis集群实例间的单向数据同步。

## 前提条件 {#section_ujy_ss7_aco .section}

-   源Redis集群中每个Node必须能够执行psync命令。
-   源Redis集群中每个Node的访问密码一致。

## 注意事项 {#section_ehp_eb7_ibt .section}

-   为保证同步链路稳定性，需要适当调大源Redis集群中每个Node的repl-backlog-size配置。
-   为保证同步质量，DTS会在源Redis集群插入一个key：**DTS\_REDIS\_TIMESTAMP\_HEARTBEAT**，用于记录更新时间点。

## 支持的数据源 {#section_u4x_g5s_qhb .section}

|同步源数据库|同步目标数据库|
|------|-------|
| -   通过专线/VPN网关/智能网关接入的自建Redis集群
-   ECS上的自建Redis集群

 | -   Redis实例
-   通过专线/VPN网关/智能网关接入的自建Redis集群
-   ECS上的自建Redis集群

 |

**说明：** 同步的源和目标Redis版本为3.2或4.0。

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

## 支持的同步架构 {#section_y3q_l5s_qhb .section}

目前DTS只支持源端Redis集群底层的Master Node到目标Redis集群的数据同步任务的配置。在进行数据同步配置时，需要按照同步数据源Redis集群中的Master Node个数拆分成多个DTS任务，每个Master Node对应一个DTS任务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746967546347_zh-CN.jpg)

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
    |源实例地域|选择数据同步链路中源Redis集群所属的地域。     -   如果源实例是阿里云ECS实例上的自建Redis集群，则选择ECS实例所属地域。
    -   如果源实例是通过专线/VPN网关/智能网关接入的自建Redis集群，则选择接入的VPC所属地域。
 **说明：** 订购后不支持更换地域，请谨慎选择。

 |
    |目标实例|固定为**Redis**。|
    |目标实例地域|选择数据同步链路中目标Redis集群实例的地域。     -   如果目标实例为云Redis实例，那么选择云Redis实例所属地域。
    -   如果目标实例是阿里云ECS实例上的自建Redis集群，则选择ECS实例所属地域。
    -   如果目标实例是通过专线/VPN网关/智能网关接入的自建Redis集群，则选择接入的VPC所属地域。
 **说明：** 订购后不支持更换地域，请谨慎选择。

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

    ![配置Redis单向同步任务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746967546323_zh-CN.png)

4.  配置同步通道的源实例及目标实例信息。

    ![Redis集群同步源目实例信息配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746967546325_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**通过专线/VPN网关/智能网关接入的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |对端专有网络|选择自建数据库接入的VPC ID。|
    |数据库类型|固定为**Redis**。|
    |IP地址|填入自建Redis集群中Master Node的IP地址。 **说明：** DTS通过同步Redis集群的各Master Node的数据来实现集群整体的数据同步，，此处先填入第一个Master Node的地址。稍后创建第二个同步任务时，此处填入第二个Master Node的IP地址。以此类推，直至同步所有的Master Node。

 |
    |端口|填入自建Redis集群中Master Node的监听端口。|
    |数据库密码|填入自建Redis集群中Master Node的访问密码。 **说明：** 非必填项，如果没有设置密码可以不填。

 |
    |目标实例信息|实例类型|选择**ECS上的自建数据库**。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择自建Redis集群中Master Node所在的ECS实例ID。|
    |实例模式|选择**集群版**。|
    |端口|填入自建Redis集群中Master Node的监听端口。|
    |数据库密码|填入自建Redis集群中Master Node的访问密码。 **说明：** 非必填项，如果没有设置密码可以不填。

 |

5.  单击页面右下角的**授权白名单并进入下一步**。
6.  配置同步对象。

    ![Redis集群同步配置同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746967546332_zh-CN.png)

    **说明：** 

    -   在源库对象框中单击选择想要同步的数据库，单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/155746967540698_zh-CN.png)移动到已选择对象框。
    -   同步对象的选择粒度为库，暂不支持Key粒度的选择。
7.  上述配置完成后单击页面右下角的**下一步**。
8.  配置同步初始化的高级配置信息。

    ![Redis全量初始化](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746967546333_zh-CN.png)

    选择**全量数据初始化**，DTS会将源Redis集群中的历史key同步至目标Redis集群。

9.  上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步任务正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步任务。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/155746967541056_zh-CN.png)，查看具体的失败详情。根据失败原因修复后，重新进行预检查。
10. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，该同步作业的同步任务正式开始。

    至此，完成源Redis集群底层单个Master Node的数据同步任务的配置。

11. 重复[操作步骤一 购买数据同步实例](#section_rfw_tpl_qhb)的所有步骤和[操作步骤二 配置数据同步](#section_nvf_vpl_qhb)中的第1步到第10步的操作步骤，为集群中其他Master Node创建数据同步任务。
12. 等待所有Master Node同步作业的链路初始化完成，直至状态处于**同步中**。

    您可以在 数据同步页面，查看数据同步状态。

    ![同步状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/155746967546355_zh-CN.png)



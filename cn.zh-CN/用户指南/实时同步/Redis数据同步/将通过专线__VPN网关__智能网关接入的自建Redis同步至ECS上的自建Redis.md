# 将通过专线/VPN网关/智能网关接入的自建Redis同步至ECS上的自建Redis {#concept_227929 .concept}

数据传输服务DTS（Data Transmission Service）支持Redis数据库的单向同步，适用于异地多活、数据异地容灾等多种应用场景。本文以通过专线/VPN网关/智能网关接入的自建Redis同步至ECS上的自建Redis为例，介绍数据同步作业的配置流程。

**说明：** 完成数据同步作业的配置后，请勿变更源数据库或目标数据库的[架构类型](https://help.aliyun.com/document_detail/86132.html)（例如将主从架构变更为集群架构），否则会导致数据同步失败。

## 前提条件 {#section_9xy_9ax_i7j .section}

-   源Redis数据库的版本为2.8、3.0、3.2或4.0版本。

    **说明：** 目标Redis数据库支持的版本为2.8、3.0、3.2、4.0或5.0版本，如需跨版本同步请提前确认兼容性。

-   目标Redis数据库的存储空间需大于源Redis数据库已使用的存储空间。
-   当源Redis数据库为集群架构时，集群的每个节点必须能够执行`psync`命令，且连接的密码一致。

## 注意事项 {#section_ax3_cgy_0uj .section}

-   为保障同步链路稳定性，建议将配置文件redis.conf中`repl-backlog-size`参数的值适当调大。
-   为保障同步质量，DTS会在源Redis数据库中插入一个key：**DTS\_REDIS\_TIMESTAMP\_HEARTBEAT**，用于记录更新时间点。

## 支持的数据源 {#section_k67_vyj_pgx .section}

|源数据库|目标数据库|
|----|-----|
| -   [Redis实例](https://help.aliyun.com/document_detail/26342.html) 

包含标准版、集群版和读写分离版

-   ECS上的自建数据库

包含单机版和集群版。

-   通过专线/VPN网关/智能网关接入的自建数据库

包含单机版和集群版。


 | -   [Redis实例](https://help.aliyun.com/document_detail/26342.html) 

包含标准版、集群版和读写分离版

-   ECS上的自建数据库

包含单机版和集群版。

-   通过专线/VPN网关/智能网关接入的自建数据库

包含单机版和集群版。


 |

## 支持的同步拓扑 {#section_n5v_xe9_d9c .section}

-   一对一单向同步
-   一对多单向同步
-   级联单向同步

关于各类同步拓扑的介绍及注意事项，请参见[数据同步拓扑介绍](cn.zh-CN/用户指南/实时同步/数据同步拓扑介绍.md#)。

## 支持的同步命令 {#section_thp_uyw_ups .section}

-   APPEND
-   BITOP、BLPOP、BRPOP、BRPOPLPUSH
-   DECR、DECRBY、DEL
-   EVAL、EVALSHA、EXEC、EXPIRE、EXPIREAT
-   GEOADD、GETSET
-   HDEL、HINCRBY、HINCRBYFLOAT、HMSET、HSET、HSETNX
-   INCR、INCRBY、INCRBYFLOAT
-   LINSERT、LPOP、LPUSH、LPUSHX、LREM、LSET、LTRIM
-   MOVE、MSET、MSETNX、MULTI
-   PERSIST、PEXPIRE、PEXPIREAT、PFADD、PFMERGE、PSETEX、PUBLISH
-   RENAME、RENAMENX、RESTORE、RPOP、RPOPLPUSH、RPUSH、RPUSHX
-   SADD、SDIFFSTORE、SELECT、SET、SETBIT、SETEX、SETNX、SETRANGE、SINTERSTORE、SMOVE、SPOP、SREM、SUNIONSTORE
-   ZADD、ZINCRBY、ZINTERSTORE、ZREM、ZREMRANGEBYLEX、ZUNIONSTORE、ZREMRANGEBYRANK、ZREMRANGEBYSCORE
-   SWAPDB、UNLINK（仅当源端Redis集群的版本为4.0时支持）

**说明：** 

-   对于通过EVAL或者EVALSHA调用Lua脚本，在增量数据同步时，由于目标端在执行脚本时不会明确返回执行结果，DTS无法确保该类型脚本能够执行成功。
-   对于List，由于DTS在调用sync或psync进行重传时，不会对目标端已有的数据进行清空，可能导致出现重复数据。

## 操作步骤 {#section_nvf_vpl_qhb .section}

1.  购买数据同步实例，详情请参见[购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。

    **说明：** 购买时，源实例和目标实例均选择为**Redis**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156445776950604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置数据同步的源实例及目标实例信息。

    ![Redis集群同步源目实例信息配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190476/156445777046325_zh-CN.png)

    |配置项目|配置选项|配置说明|
    |:---|:---|:---|
    |任务名称|-|     -   DTS为每个任务自动生成一个任务名称，任务名称没有唯一性要求。
    -   您可以根据需要修改任务名称，建议为任务配置具有业务意义的名称，便于后续的任务识别。
 |
    |源实例信息|实例类型|选择**通过专线/VPN网关/智能网关接入的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源实例地域信息，不可变更。|
    |对端专有网络|选择自建数据库接入的VPC ID。|
    |数据库类型|固定为**Redis**。|
    |实例模式|根据源Redis数据库的架构选择**单机版**或**集群版**。|
    |IP地址|填入源Redis数据库的IP地址。 **说明：** 当源Redis数据库为集群架构时，填入任一节点的Master所属服务器的IP地址。

 |
    |端口|填入源Redis数据库的服务端口，默认为**6379**。 **说明：** 当源Redis数据库为集群架构时，填入任一节点的Master的服务端口。

 |
    |数据库密码|填入连接源Redis数据库的密码。 **说明：** 非必填项，如果没有设置密码可以不填。

 |
    |目标实例信息|实例类型|选择**ECS上的自建数据库**。|
    |实例地区|购买数据同步实例时选择的目标实例地域信息，不可变更。|
    |实例ID|选择作为同步目标的ECS实例ID。 **说明：** 当目标Redis数据库为集群架构时，选择任一节点的Master所在的ECS实例ID。

 |
    |实例模式|根据目标Redis数据库的架构选择**单机版**或**集群版**。|
    |端口|填入目标Redis数据库的服务端口，默认为**6379**。 **说明：** 当目标Redis数据库为集群架构时，填入任一节点的Master的服务端口。

 |
    |数据库密码|填入连接目标Redis数据库的密码。 **说明：** 非必填项，如果没有设置密码可以不填。

 |

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址，自动添加到目标ECS实例的内网入方向规则中，用于保障DTS服务器能够正常连接目标ECS实例。

8.  配置目标已存在表的处理模式和同步对象。

    ![配置处理模式和同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156445777050649_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |目标已存在表的处理模式|     -   **预检查并报错拦截**：检查目标库是否为空。如果待同步的目标库为空，则通过该检查项目；如果不为空，则在预检查阶段提示错误，数据同步作业不会被启动。
    -   **无操作**：跳过目标库是否为空的检查项。

**警告：** 选择为**无操作**后，在同步过程中如果遇到目标库中的Key与源库中的Key相同，会将源库的数据覆盖写入目标库中，请谨慎选择。

 |
    |同步对象|     -   在源库对象框中单击待同步的数据库库名，然后单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156445777040698_zh-CN.png)移动到已选择对象框。
    -   同步对象的选择粒度为库，暂不支持Key粒度的选择。
 |

9.  上述配置完成后单击页面右下角的**下一步**。
10. 配置同步初始化的选项，当前固定为**包含全量数据+增量数据**。

    **说明：** DTS会将源Redis数据库中的存量数据同步至目标Redis数据库中，并同步增量数据。

    ![Redis同步初始化](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156445777050606_zh-CN.png)

11. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156445777047468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
13. 等待该同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在 数据同步页面，查看数据同步作业的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156445777041059_zh-CN.png)



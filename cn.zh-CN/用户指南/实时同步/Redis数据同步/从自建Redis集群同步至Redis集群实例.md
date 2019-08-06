# 从自建Redis集群同步至Redis集群实例 {#concept_228260 .concept}

数据传输服务DTS（Data Transmission Service）支持Redis集群间的单向同步，适用于数据迁移、异地多活、数据异地容灾等多种应用场景。本文以自建Redis集群同步至阿里云Redis集群实例为例，介绍数据同步作业的配置流程。

从阿里云Redis集群实例同步至自建Redis集群的操作步骤与本文类似，您需要根据实际的业务场景配置同步的源和目标实例信息。

**说明：** 完成数据同步作业的配置后，请勿变更源数据库或目标数据库的[架构类型](https://help.aliyun.com/document_detail/86132.html)（例如将集群架构变更为主从架构），否则会导致数据同步失败。

## 前提条件 {#section_cmf_ejn_8wi .section}

-   自建Redis数据库的版本为2.8、3.0、3.2或4.0版本。

    **说明：** 目标阿里云Redis集群实例支持的版本为2.8、4.0或5.0版本，如需跨版本同步请提前确认兼容性。例如，创建按量付费的Redis集群实例来测试，测试完成后可将该实例释放或转为包年包月。

-   目标阿里云Redis集群实例的存储空间需大于源Redis数据库已使用的存储空间。
-   源Redis集群的每个节点必须能够执行`psync`命令，且连接的密码一致。

## 注意事项 {#section_x90_tqd_68v .section}

-   为保障同步链路稳定性，建议将配置文件redis.conf中`repl-backlog-size`参数的值适当调大。
-   为保障同步质量，DTS会在源Redis数据库中插入一个key：**DTS\_REDIS\_TIMESTAMP\_HEARTBEAT**，用于记录更新时间点。

## 支持的同步拓扑 {#section_iym_m6y_g4w .section}

-   一对一单向同步
-   一对多单向同步
-   级联单向同步

关于各类同步拓扑的介绍及注意事项，请参见[数据同步拓扑介绍](cn.zh-CN/用户指南/实时同步/数据同步拓扑介绍.md#)。

## 支持的同步命令 {#section_k3i_40m_941 .section}

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

1.  [购买数据同步作业](../../../../cn.zh-CN/快速入门/购买流程.md#section_39h_fto_gdl)。

    **说明：** 购买时，源实例和目标实例均选择为**Redis**。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据同步**。
4.  在同步作业列表页面顶部，选择数据同步实例所属地域。

    ![选择地域](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156508510450604_zh-CN.png)

5.  定位至已购买的数据同步实例，单击**配置同步链路**。
6.  配置数据同步的源实例及目标实例信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1054245/156508510454591_zh-CN.png)

    |配置|选项|说明|
    |:-|:-|:-|
    |同步作业名称|-|     -   DTS为每个同步作业自动生成一个名称，该名称没有唯一性要求。
    -   建议为同步作业配置具有业务意义的名称，便于后续的识别。
 |
    |源实例信息|实例类型|选择**ECS上的自建数据库**。|
    |实例地区|购买数据同步实例时选择的源实例地域，不可变更。|
    |ECS实例ID|选择自建Redis集群中任一节点的Master所在的ECS实例ID。|
    |数据库类型|固定为**Redis**。|
    |实例模式|选择**集群版**。|
    |端口|填入自建Redis集群中任一节点的Master的服务端口，本案例填入**7000**。|
    |数据库密码|填入连接自建Redis数据库的密码。 **说明：** 非必填项，如果没有设置密码可以不填。

 |
    |目标实例信息|实例类型|选择**Redis实例**。|
    |实例地区|购买数据同步实例时选择的目标实例地域，不可变更。|
    |实例ID|选择目标阿里云Redis集群实例ID。|
    |数据库密码|填入连接目标阿里云Redis集群实例的密码。|

7.  单击页面右下角的**授权白名单并进入下一步**。

    **说明：** 此步骤会将DTS服务器的IP地址，自动添加到源ECS实例的内网入方向规则和目标阿里云Redis集群实例的白名单中，用于保障DTS服务器能够正常连接源ECS实例和目标阿里云Redis集群实例。

8.  配置目标已存在表的处理模式和同步对象。

    ![配置处理模式和同步对象](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156508510550649_zh-CN.png)

    |配置|说明|
    |:-|:-|
    |目标已存在表的处理模式|     -   **预检查并报错拦截**：检查目标库是否为空。如果待同步的目标库为空，则通过该检查项目；如果不为空，则在预检查阶段提示错误，数据同步作业不会被启动。
    -   **无操作**：跳过目标库是否为空的检查项。

**警告：** 选择为**无操作**后，在同步过程中如果遇到目标库中的Key与源库中的Key相同，会将源库的数据覆盖写入目标库中，请谨慎选择。

 |
    |同步对象|     -   在源库对象框中单击待同步的数据库，然后单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156508510540698_zh-CN.png)将其移动到已选择对象框。
    -   同步对象的选择粒度为库，暂不支持Key粒度的选择。
 |

9.  单击页面右下角的**下一步**。
10. 配置同步初始化的选项，当前固定为**包含全量数据+增量数据**。

    **说明：** DTS会将源Redis数据库中的存量数据同步至目标Redis数据库中，并同步增量数据。

    ![Redis同步初始化](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/776198/156508510550606_zh-CN.png)

11. 上述配置完成后，单击页面右下角的**预检查并启动**。

    **说明：** 

    -   在数据同步作业正式启动之前，会先进行预检查。只有预检查通过后，才能成功启动数据同步作业。
    -   如果预检查失败，单击具体检查项后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156508510547468_zh-CN.png)，查看失败详情。根据提示修复后，重新进行预检查。
12. 在预检查对话框中显示**预检查通过**后，关闭预检查对话框，同步作业将正式开始。
13. 等待该同步作业的链路初始化完成，直至处于**同步中**状态。

    您可以在 数据同步页面，查看数据同步作业的状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17125/156508510541059_zh-CN.png)



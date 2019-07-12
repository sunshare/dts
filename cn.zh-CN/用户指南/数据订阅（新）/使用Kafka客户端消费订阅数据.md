# 使用Kafka客户端消费订阅数据 {#concept_508217 .concept}

新版数据订阅支持使用0.10.0.x版本至1.1.x版本的Kafka客户端消费订阅数据，本文将介绍Kafka客户端demo代码的使用说明。

## 前提条件 {#section_p9u_awf_ddf .section}

-   已创建数据订阅通道，详情请参见[创建数据订阅通道](cn.zh-CN/用户指南/数据订阅（新）/创建数据订阅通道.md#)。
-   已创建消费组，详情请参见[新增消费组](cn.zh-CN/用户指南/数据订阅（新）/新增消费组.md#)。

## Kafka客户端Demo代码下载 {#section_7nm_4fm_7s8 .section}

请下载[Kafka客户端Demo代码](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/109395/cn_zh/1560928466965/subscribe_demo.tar)。

## 订阅通道数据格式介绍 {#section_mcu_0ca_nmc .section}

数据以Avro序列化存储，详细格式请参见Kafka客户端Demo代码中的Record.avsc文件，该文件的路径为subscribe\_demo/src/main/resources/。订阅到数据后，您需要根据avro schema定义进行数据解析。

## Kafka客户端Demo代码说明 {#section_t4g_wpn_ta6 .section}

-   代码中参数设置项。

    **说明：** 您可以通过DTS控制台获取以下参数的取值，详情请参见[获取数据订阅所需信息](#section_oud_5wv_dmf)。

    |参数|说明|
    |:-|:-|
    |dtsConnectIp|数据订阅通道的网络地址。|
    |dtsConnectPort|数据订阅通道的端口。|
    |topic|数据订阅通道的订阅Topic。|
    |sid|消费组ID。|
    |username|该消费组的的账号。|
    |password|该消费组账号对应的的密码。 **说明：** 如果忘记消费组密码，您可以[修改消费组密码](cn.zh-CN/用户指南/数据订阅（新）/管理消费组.md#section_isf_puz_17u)。

 |
    |startTimeStamp|您需要保存已消费的数据时间点。当业务程序中断后，您可以通过订阅客户端传入已消费的数据时间点来继续消费数据，防止数据丢失。同时您还可以在订阅客户端启动时，传入所需的消费位点，对订阅位点进行调整，实现按需消费数据。|

-   关键代码。
    -   makeProps

        功能：配置访问订阅通道的相关参数。

    -   assignOffsetToConsumer

        功能：指定期望时间点开始消费数据。

        **说明：** 您需要保存已消费的数据时间点，便于业务程序中断后，仍可按需设置时间点消费，保证数据不丢失。

    -   consume

        功能：具体消息处理函数，主要作用是对数据类型进行转换。

    -   avro数据格式与MySQL类型的对应关系

        功能：按类型对数据解析并使用。avro数据格式的解析数据中包含dataTypeNumber字段，该字段的不同取值对应不同的MySQL数据类型，详情请参见[MySQL字段类型与dataTypeNumber数值的对应关系](#section_woc_4pq_mes)。


## 获取数据订阅所需信息 {#section_oud_5wv_dmf .section}

本案例以RDS for MySQL数据订阅通道为例，介绍如何获取数据订阅所需信息。

1.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
2.  在左侧导航栏，单击**数据订阅**。
3.  在数据订阅列表页面上方，选择订阅通道所属地域。
4.  定位目标数据订阅通道，单击该订阅ID。
5.  在订阅配置页面，您将获取到**订阅Topic**和**网络**信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/408280/156289717948804_zh-CN.png)

    **说明：** 

    -   网络区域框中展示的地址信息包含域名和端口号。
    -   如果您部署Kafka Client的ECS实例与数据订阅通道属于同一经典网络或同一专有网络，建议通过内网地址进行数据订阅，网络延迟最小。
6.  在左侧导航栏，单击**数据消费**，您将获取到**消费组ID**和对应的**账号**信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/408280/156289717948805_zh-CN.png)

    **说明：** 如果忘记消费组密码，您可以[修改消费组密码](cn.zh-CN/用户指南/数据订阅（新）/管理消费组.md#section_isf_puz_17u)。


## MySQL字段类型与dataTypeNumber数值的对应关系 {#section_woc_4pq_mes .section}

|MySQL字段类型|对应dataTypeNumber数值|
|:--------|:-----------------|
|MYSQL\_TYPE\_DECIMAL|0|
|MYSQL\_TYPE\_INT8|1|
|MYSQL\_TYPE\_INT16|2|
|MYSQL\_TYPE\_INT32|3|
|MYSQL\_TYPE\_FLOAT|4|
|MYSQL\_TYPE\_DOUBLE|5|
|MYSQL\_TYPE\_NULL|6|
|MYSQL\_TYPE\_TIMESTAMP|7|
|MYSQL\_TYPE\_INT64|8|
|MYSQL\_TYPE\_INT24|9|
|MYSQL\_TYPE\_DATE|10|
|MYSQL\_TYPE\_TIME|11|
|MYSQL\_TYPE\_DATETIME|12|
|MYSQL\_TYPE\_YEAR|13|
|MYSQL\_TYPE\_DATE\_NEW|14|
|MYSQL\_TYPE\_VARCHAR|15|
|MYSQL\_TYPE\_BIT|16|
|MYSQL\_TYPE\_TIMESTAMP\_NEW|17|
|MYSQL\_TYPE\_DATETIME\_NEW|18|
|MYSQL\_TYPE\_TIME\_NEW|19|
|MYSQL\_TYPE\_JSON|245|
|MYSQL\_TYPE\_DECIMAL\_NEW|246|
|MYSQL\_TYPE\_ENUM|247|
|MYSQL\_TYPE\_SET|248|
|MYSQL\_TYPE\_TINY\_BLOB|249|
|MYSQL\_TYPE\_MEDIUM\_BLOB|250|
|MYSQL\_TYPE\_LONG\_BLOB|251|
|MYSQL\_TYPE\_BLOB|252|
|MYSQL\_TYPE\_VAR\_STRING|253|
|MYSQL\_TYPE\_STRING|254|
|MYSQL\_TYPE\_GEOMETRY|255|


# 跨阿里云账号数据迁移/同步时如何配置RAM授权 {#Untitled .concept}

数据传输服务DTS支持将另一个阿里云账号下的RDS实例数据迁移或同步至当前阿里云账号，本文将介绍在跨阿里云账号使用DTS进行数据迁移/同步时，源实例所属的阿里云账号如何配置RAM授权。

## DTS跨账号数据迁移/同步支持的数据类型 {#section_s31_rys_lhb .section}

|支持的功能|源数据类型|目标数据类型|
|:----|:----|:-----|
|数据迁移|RDS实例|RDS实例|
|DRDS实例|
|HybridDB for MySQL实例|
|OceanBase实例|
|ECS自建数据库|
|有公网IP的自建数据库|
|数据同步|RDS实例|RDS实例|
|MaxCompute\(原ODPS\)实例|
|Datahub\(流计算使用\)实例|
|分析型数据库AnalyticDB|

## 背景介绍 {#section_z83_v4l_75r .section}

在使用DTS进行数据迁移或者数据同步时，需要在源实例所属云账号中配置RAM授权，将目标实例所属云账号作为授信云账号，允许通过数据传输服务访问源实例所属云账号的相关云资源。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/156462633244890_zh-CN.png)

## 准备工作 {#section_4cd_6j9_mg7 .section}

使用目标实例所属的云账号登录[账号管理](https://account.console.aliyun.com/#/secure)页面，获取云账号ID。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/156462633244838_zh-CN.png)

## 源实例所属云账号授权DTS访问 {#section_mfw_bgt_lhb .section}

在您第一次使用DTS时，需要您将名称为**AliyunDTSDefaultRole** 的权限策略授权给DTS的RAM角色。经过授权后，DTS可访问当前云账号下的RDS、ECS等云资源以进行数据迁移或数据同步。

**说明：** 如果您的阿里云账号登陆[数据传输服务DTS控制台](https://dts.console.aliyun.com/)时，没有提示您授权，说明当前云账号已经授权过，您可跳过本步骤。

**操作步骤**

1.  使用源实例所属云账号登陆[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在弹出的提示对话框中，单击**前往RAM角色授权**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/156462633244835_zh-CN.png)

3.  在弹出的云资源访问授权对话框中，单击**同意授权**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/156462633244836_zh-CN.png)


## 将目标实例所属的云账号设置为授信云账号 {#section_rfj_z3t_lhb .section}

**操作步骤**

1.  使用源实例所属云账号登陆[RAM控制台](https://ram.console.aliyun.com/)。
2.  在左侧导航栏，单击**RAM角色管理**。
3.  单击**新建RAM角色**，选择可信实体类型为**阿里云账号**，单击**下一步**。
4.  在新建RAM角色对话框，配置RAM角色信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/156462633344837_zh-CN.png)

    |配置选项|配置说明|
    |:---|:---|
    |角色名称|填写RAM角色名称，本案例填写**ram-for-dts**。 **说明：** 可以填写大写英文、小写英文、数字或短横线（-），长度不超过64个字符。

 |
    |备注（可选）|填写RAM角色备注名称。|
    |选择云账号|选择为**其他云账号**，填写目标实例所属的云账号ID作为授信云账号。 **说明：** 云账号ID获取方法请参见[获取目标实例所属的账号ID](#section_4cd_6j9_mg7)。

 |
    |备注（可选）|填写RAM角色备注名称。|

5.  单击**完成**。
6.  单击**精确授权**。
7.  在添加权限对话框中选择权限类型为**系统策略**，并输入策略名称：**AliyunDTSRolePolicy**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/156462633344840_zh-CN.png)

8.  单击**确定**。
9.  单击**完成**。
10. 在信任策略管理页签，单击**修改信任策略**，将下述代码复制至策略框中。

    **说明：** 您需要将下述代码中的<云账号ID\>更换为您的目标实例所属的云账号ID，云账号ID获取方法请参见[获取目标实例所属的账号ID](#section_4cd_6j9_mg7)。

    ``` {#codeblock_uw8_uv2_qk5}
    {
        "Statement": [
            {
                "Action": "sts:AssumeRole",
                "Effect": "Allow",
                "Principal": {
                    "RAM": [
                        "acs:ram::<云账号ID>:root"
                    ],
                    "Service": [
                        "<云账号ID>@dts.aliyuncs.com"
                    ]
                }
            }
        ],
        "Version": "1"
    }
    ```


完成权限授权后，即可配置跨云账号数据迁移或同步任务。


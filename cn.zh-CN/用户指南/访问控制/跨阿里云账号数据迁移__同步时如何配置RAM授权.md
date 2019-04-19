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

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/155564226844890_zh-CN.png)

## 准备工作 {#section_4cd_6j9_mg7 .section}

使用目标实例所属的云账号登录[账号管理](https://account.console.aliyun.com/#/secure)页面，获取云账号ID。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/155564226844838_zh-CN.png)

## 源实例所属云账号授权DTS访问 {#section_mfw_bgt_lhb .section}

在您第一次使用DTS时，需要您将名称为**AliyunDTSDefaultRole** 的权限策略授权给DTS的RAM角色。经过授权后，DTS可访问当前云账号下的RDS、ECS等云资源以进行数据迁移或数据同步。

**说明：** 如果您的阿里云账号登陆[数据传输服务DTS控制台](https://dts.console.aliyun.com/)时，没有提示您授权，说明当前云账号已经授权过，您可跳过本步骤。

**操作步骤**

1.  使用源实例所属云账号登陆[数据传输服务DTS控制台](https://dts.console.aliyun.com/)。
2.  在弹出的提示对话框中，单击**前往RAM角色授权**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/155564226844835_zh-CN.png)

3.  在弹出的云资源访问授权对话框中，单击**同意授权**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/155564226844836_zh-CN.png)


## 将目标实例所属的云账号设置为授信云账号 {#section_rfj_z3t_lhb .section}

**操作步骤**

1.  使用源实例所属云账号登陆[RAM 控制台](https://ram.console.aliyun.com/)。
2.  在左侧导航栏，单击**RAM角色管理**。
3.  单击**新建角色**。
4.  在新建RAM角色对话框，配置RAM角色信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/155564226844837_zh-CN.png)

    |配置选项|配置说明|
    |:---|:---|
    |选择可信实体类型|选择为**阿里云账号**。|
    |选择云账号|选择为**其他云账号**，填写目标实例所属的云账号ID作为授信云账号。 **说明：** 云账号ID获取方法请参见[获取目标实例所属的账号ID](#section_4cd_6j9_mg7)。

 |
    |RAM角色名称|填写RAM角色名称，本案例填写ram-for-dts。 **说明：** 可以填写大写英文、小写英文、数字或短横线（-），长度不超过64个字符。

 |
    |备注（可选）|填写RAM角色备注名称。|

5.  上述配置完成后，单击**确定**。
6.  在左侧导航栏，单击**授权管理** \> **权限策略管理**。
7.  单击**新建授权策略**，配置授权策略信息。
    1.  填写**策略名称**，本案例填写策略名称为DTS迁移使用。
    2.  **配置模式**选择为**脚本配置**。
    3.  填写授权策略的脚本信息，详情请参见[DTS授权脚本](#section_yvz_2d5_lhb)。
8.  返回RAM角色管理页面，找到创建的RAM角色，单击角色名称。
9.  在权限管理页签，单击添加权限。
10. 在添加授权对话框中选择权限为**自定义权限策略**，选择自定义的策略**DTS迁移使用**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17089/155564226844840_zh-CN.png)

11. 单击**确定**并在授权结果页面中单击**完成**。
12. 在信任策略管理页签，单击**修改信任策略**，将下述代码复制至策略框中。

    **说明：** 您需要将下述代码中的<云账号ID\>更换为您的目标实例所属的云账号ID，云账号ID获取方法请参见[获取目标实例所属的账号ID](#section_4cd_6j9_mg7)。

    ```
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

## DTS授权脚本 {#section_yvz_2d5_lhb .section}

```
{
    "Version": "1",
    "Statement": [
        {
            "Action": [
                "rds:Describe*",
                "rds:CreateDBInstance",
                "rds:CreateAccount*",
                "rds:CreateDataBase*",
                "rds:ModifySecurityIps",
                "rds:GrantAccountPrivilege"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "ecs:DescribeSecurityGroupAttribute",
                "ecs:DescribeInstances",
                "ecs:DescribeRegions",
                "ecs:AuthorizeSecurityGroup"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "dhs:ListProject",
                "dhs:GetProject",
                "dhs:CreateTopic",
                "dhs:ListTopic",
                "dhs:GetTopic",
                "dhs:UpdateTopic",
                "dhs:ListShard",
                "dhs:MergeShard",
                "dhs:SplitShard",
                "dhs:PutRecords",
                "dhs:GetRecords",
                "dhs:GetCursors"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "elasticsearch:DescribeInstance",
                "elasticsearch:ListInstance",
                "elasticsearch:UpdateAdminPwd",
                "elasticsearch:UpdatePublicNetwork",
                "elasticsearch:UpdateBlackIps",
                "elasticsearch:UpdateKibanaIps",
                "elasticsearch:UpdatePublicIps",
                "elasticsearch:UpdateWhiteIps"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "drds:DescribeDrds*",
                "drds:ModifyDrdsIpWhiteList",
                "drds:DescribeRegions",
                "drds:DescribeRdsList",
                "drds:CeateDrdsDB",
                "drds:DescribeShardDBs"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "polardb:DescribeDBClusterIPArrayList",
                "polardb:DescribeDBClusterNetInfo",
                "polardb:DescribeDBClusters",
                "polardb:DescribeRegions",
                "polardb:ModifySecurityIps"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "dds:DescribeDBInstanceAttribute",
                "dds:DescribeReplicaSetRole",
                "dds:DescribeSecurityIps",
                "dds:DescribeDBInstances",
                "dds:ModifySecurityIps",
                "dds:DescribeRegions"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "kvstore:DescribeSecurityIps",
                "kvstore:DescribeInstances",
                "kvstore:DescribeRegions",
                "kvstore:ModifySecurityIps"
            ],
            "Resource": "*",
            "Effect": "Allow"
        },
        {
            "Action": [
                "petadata:DescribeInstanceInfo",
                "petadata:DescribeSecurityIPs",
                "petadata:DescribeInstances",
                "petadata:ModifySecurityIPs"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```


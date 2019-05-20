# 授权子账号使用DTS {#concept_47568_zh .concept}

为细分账号权限，提升账号安全性，您可以通过[访问控制RAM](https://help.aliyun.com/document_detail/28627.html)（Resource Access Management）将DTS的管理权限授权给子账号，然后使用子账号访问DTS。

## 策略说明 {#section_xp2_lz5_mhb .section}

DTS支持的授权策略为读写策略和只读策略。

**说明：** 授权策略暂不支持API粒度的访问授权。

-   读写策略，策略名称为：**AliyunDTSFullAccess**。

    该策略拥有DTS所有读写权限，授权了该策略的子账号可以进行DTS实例的购买、配置、管理等操作。

-   只读策略，策略名称为：**AliyunDTSReadOnlyAccess**。

    该策略拥有DTS所有读权限，授权了该权限的子账号可以查看主账号下所有DTS任务的任务详情、任务配置等信息，不能进行变更操作。

    **说明：** 变更操作主要包括：DTS实例的购买、配置、管理等操作。


## 子账号授权操作步骤 {#section_c5g_21v_mhb .section}

1.  登录[RAM 控制台](https://ram.console.aliyun.com/)创建子账号，详情请参考[创建RAM用户](https://help.aliyun.com/document_detail/28637.html)。
2.  在RAM控制台的左侧导航栏，单击**人员管理** \> **用户**。
3.  定位至创建的RAM用户，在**操作**列单击**添加权限**。

    ![添加授权](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17087/155831818645025_zh-CN.png)

4.  在添加权限对话框中，配置授权信息。

    ![配置授权信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17087/155831818645026_zh-CN.png)

    1.  选择权限为**系统权限策略**。
    2.  在搜索框中输入dts，展现DTS相关的系统权限策略。
    3.  根据业务需求，单击要授权的权限策略添加到已选择区域框中。

        **说明：** 策略说明请参见[策略说明](#section_xp2_lz5_mhb)。

5.  单击**确定**并在授权结果对话框中单击**完成**。

## 后续操作 {#section_vdp_tcv_mhb .section}

使用RAM用户登录控制台，详情请参考[RAM用户登录控制台](https://help.aliyun.com/document_detail/43640.html)。


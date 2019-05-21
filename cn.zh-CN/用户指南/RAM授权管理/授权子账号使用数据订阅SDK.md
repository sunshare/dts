# 授权子账号使用数据订阅SDK {#concept_265351 .concept}

数据传输服务（Data Transmission Service，简称DTS）支持阿里云RAM主子账号体系。您可以使用子账号进行任务的创建和管理，也可以使用子账号的AccessKey和AccessKeySecret进行数据的实时订阅。

## 策略说明 {#section_c4r_m3g_thb .section}

DTS支持的授权策略为读写策略和只读策略。

-   读写策略，策略名称为：**AliyunDTSFullAccess**。

    该策略拥有DTS所有读写权限，授权了该策略的子账号可以进行DTS实例的购买、配置、管理等操作。

-   只读策略，策略名称为：**AliyunDTSReadOnlyAccess**。该策略拥有DTS所有读权限，授权了该权限的子账号可以查看主账号下所有DTS任务的任务详情、任务配置等信息，不能进行变更操作。

    **说明：** 变更操作主要包括：DTS实例的购买、配置、管理等操作。


## 子账号授权操作步骤 {#section_c5g_21v_mhb .section}

1.  登录[RAM 控制台](https://ram.console.aliyun.com/)创建子账号，详情请参考[创建RAM用户](https://help.aliyun.com/document_detail/28637.html)。

    **说明：** 创建RAM用户时，将访问方式选择**编程访问**并下载保存AK信息。

2.  在RAM控制台的左侧导航栏，单击**人员管理** \> **用户**。
3.  定位至创建的RAM用户，在**操作**列单击**添加权限**。

    ![添加授权](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17087/155840947145025_zh-CN.png)

4.  在添加权限对话框中，配置授权信息。

    ![配置授权信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17087/155840947145026_zh-CN.png)

    1.  选择权限为**系统权限策略**。
    2.  在搜索框中输入dts，展现DTS相关的系统权限策略。
    3.  单击**AliyunDTSFullAccess**策略添加到已选择区域框中。
5.  单击**确定**并在授权结果对话框中单击**完成**。

## 通过子账号订阅数据 {#section_hpm_ljg_thb .section}

当子账号创建且授权完成后，您可以使用DTS提供的SDK来订阅数据。关于SDK使用请参考[数据订阅SDK示例代码运行简介](https://help.aliyun.com/document_detail/26647.html)。

**说明：** 需要将SDK Demo中的用户AccessKey和AccessKeySecret修改成子账号的AccessKey和AccessKeySecret信息。


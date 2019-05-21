# schema存在性检查 {#concept_266933 .concept}

为保障数据迁移任务的顺利执行，DTS在预检查阶段检查待迁移的数据库在目标实例中是否存在。如果不存在，DTS会自动创建，如果创建失败则提示预检查失败。

DTS在以下情况中自动创建数据库的操作将会失败并提示预检查失败。

## 源库中待迁移数据库库名或字符集不符合RDS规范 {#section_ffy_kfc_5hb .section}

-   源库中待迁移数据库库名含有小写字母、数字、下划线、中划线以外的特殊字符。
-   源库中待迁移数据库的字符集不是utf8、gbk、latin1或utf8mb4。

**修复方法：**

1.  创建符合RDS库名和字符集规范的数据库，详情请参考[创建数据库](https://help.aliyun.com/document_detail/96105.html)。

    **说明：** 在此步骤中需要将该数据库授权给数据迁移任务中填写的目标数据库账号。

2.  登录[数据传输控制台](https://dts.console.aliyun.com/)。
3.  在左侧导航栏，单击**数据迁移**。
4.  定位至目标迁移任务，单击**修改任务配置**。

    ![修改任务配置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17096/155840952347488_zh-CN.png)

5.  单击页面右下角的**授权白名单并进入下一步**。
6.  将待迁移的数据库映射为目标数据库中新创建的数据库，详情请参考[数据库名映射](https://help.aliyun.com/document_detail/26628.html#h2-u6570u636Eu5E93u540Du6620u5C04)。
7.  单击**预检查并启动**。

## 提供的目标数据库账号权限不足 {#section_v3m_qfc_5hb .section}

**修复方法：**

1.  将迁移的目标库读写权限授予给目标数据库账号，详情请参考[修改账号权限](https://help.aliyun.com/document_detail/96101.html)。
2.  重新执行预检查。


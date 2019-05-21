# 源库server-id检查 {#concept_267769 .concept}

在启动MySQL到MySQL的[增量数据迁移](https://help.aliyun.com/document_detail/43782.html)任务时，DTS将在预检查阶段对源数据库进行server-id检查。本文将介绍源库server-id检查失败对应的修复方法。

## 解决方法 {#section_urs_4mq_7bc .section}

1.  登录自建MySQL数据库服务器。
2.  执行下述命令重新设置`server-id`。

    ``` {#codeblock_wam_qdd_qgw}
    set global server_id=<id>;
    ```

    **说明：** <id\>为大于1的整数。

    示例：

    ``` {#codeblock_lx3_pe0_33l}
    set global server_id=2;
    ```

3.  登录[数据传输控制台](https://dts.console.aliyun.com/)，重新执行预检查。


# 源库binlog检查 {#concept_266433 .concept}

在启动MySQL到MySQL的[增量数据迁移](https://help.aliyun.com/document_detail/43782.html)任务时，DTS将在预检查阶段对源数据库进行binlog检查。本文将介绍源库binlog检查涉及的检查项及修复方法。

## 源库binlog是否开启检查 {#section_bls_hx5_thb .section}

该检查项主要检查源数据库是否开启binlog功能。如果检查失败，请参考下述方法修复。

**修复方法：**

1.  登录自建MySQL数据库服务器。
2.  使用`vim`命令修改配置文件`my.cnf`中的如下参数。

    ```
    log_bin=mysql_bin
    binlog_format=row
    server_id=大于 1 的整数
    binlog_row_image=full //当本地 MySQL 版本大于 5.6 时，则需设置该项。
    ```

3.  重启MySQL进程。

    ```
    $mysql_dir/bin/mysqladmin -u root -p shutdown
    $mysql_dir/bin/safe_mysqld &
    ```

    **说明：** 将`mysql_dir`替换为您MySQL实际的安装目录。

4.  登录[数据传输控制台](https://dts.console.aliyun.com/)，重新执行预检查。

## 源库binlog模式检查 {#section_cxp_2y5_thb .section}

该检查项主要检查源数据库的binlog模式是否为`ROW`。如果检查失败，请参考下述方法修复。

**修复方法：**

1.  连接自建MySQL数据库。
2.  在数据库命令窗口执行下述语句将binlog模式设置为`ROW`。

    ```
    set global binlog_format='ROW';
    ```

3.  重启MySQL进程。

    ```
    $mysql_dir/bin/mysqladmin -u root -p shutdown
    $mysql_dir/bin/safe_mysqld &
    ```

    **说明：** 将`mysql_dir`替换为您MySQL实际的安装目录。

4.  登录[数据传输控制台](https://dts.console.aliyun.com/)，重新执行预检查。

## 源库binlog存在性检查 {#section_tkt_fy5_thb .section}

该检查项主要检查源数据库的binlog文件是否被删除。如果检查失败，说明源数据库的binlog文件不完整，请参考下述方法修复。

**修复方法：**

1.  在预检查对话框中，单击源库binlog存在性检查栏目后的![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/155840806447468_zh-CN.png)。

    ![binlog存在性检查错误](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/155840806447469_zh-CN.png)

2.  在弹出的查看详情对话框中，查看失败原因中提示缺少的binlog文件，本案例缺少的binlog文件为mysql\_bin.000003。

    ![binlog存在性检查修复方法](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/155840806447470_zh-CN.png)

3.  连接自建MySQL数据库。
4.  在数据库命令窗口执行下述语句，清除指定的binlog文件之前的所有binlog文件。

    **说明：** 本案例中缺失的binlog文件为mysql\_bin.000003，指定的日志文件为该文件之后的第一个文件，即填入的binlog\_filename为`mysql_bin.000004`。

    ```
    PURGE BINARY LOGS TO '<binlog_filename>';
    ```

    示例：

    ``` {#codeblock_wod_ahi_wtz}
    PURGE BINARY LOGS TO 'mysql_bin.000004';
    ```

5.  登录[数据传输控制台](https://dts.console.aliyun.com/)，重新执行预检查。

## Mysql源库binlog\_row\_image是否为FULL {#section_etw_gy5_thb .section}

该检查项主要检查源数据库的`binlog_row_image`是否为`full`。如果检查失败，说明源数据库的binlog未记录全镜像，请参考下述方法修复。

**修复方法：**

1.  连接自建MySQL数据库。
2.  在数据库命令窗口执行下述语句，将`binlog_row_image`设置为`full`。

    ```
    set global binlog_row_image=FULL;
    ```

3.  登录[数据传输控制台](https://dts.console.aliyun.com/)，重新执行预检查。


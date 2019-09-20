# 为自建MySQL创建账号并设置binlog {#concept_1198525 .concept}

当数据迁移/同步/订阅的源库为自建MySQL时，为满足预检查阶段对源库的要求，保障任务的顺利执行，在正式配置之前，您需要在自建MySQL数据库上创建账号并设置binlog。

## 影响 {#section_ygd_2pc_ckg .section}

执行该操作需要重启MySQL服务，为避免影响您的业务使用，请在业务低峰期操作。

## 操作步骤 {#section_3lk_l61_hgj .section}

1.  登录自建MySQL数据库。
2.  在自建MySQL数据库中创建用于数据迁移/同步的账号。

    ``` {#codeblock_urb_pvd_ces}
    CREATE USER 'username'@'host' IDENTIFIED BY 'password';
    ```

    **说明：** 

    -   username：待创建的账号。
    -   host：允许该账号登录的主机，如果允许该账号从任意主机登录数据库，可以使用百分号（%）。
    -   password：账号的密码。
    例如，创建一个账号，账号名为dtsmigration，密码为Dts123456，并允许从任意主机登录数据库，命令如下。

    ``` {#codeblock_ypv_fpj_98m}
    CREATE USER 'dtsmigration'@'%' IDENTIFIED BY 'Dts123456';
    ```

3.  对账号进行授权操作。

    ``` {#codeblock_se3_4kr_aat}
    GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
    ```

    **说明：** 

    -   privileges：授予该账号的操作权限，如SELECT、INSERT、UPDATE等，如果要授予该账号所有权限，则使用ALL。
    -   databasename：数据库名。如果要授予该账号具备所有数据库的操作权限，则使用星号（\*）。
    -   tablename：表名。如果要授予该账号具备所有表的操作权限，则使用星号（\*）。
    -   username：待授权的账号。
    -   host：允许该账号登录的主机，如果允许该账号从任意主机登录，则使用百分号（%）。
    -   WITH GRANT OPTION：授予该账号使用GRANT命令的权限，该参数为可选。
    例如，授予dtsmigration账号具备所有数据库和表的所有权限，并允许从任意主机登录数据库，命令如下。

    ``` {#codeblock_2ph_zzw_69g}
    GRANT ALL ON *.* TO 'dtsmigration'@'%';
    ```

4.  开启并设置自建MySQL数据库的binlog。
    -   Linux操作系统操作步骤如下：
        1.  使用`vim`命令，修改配置文件my.cnf中的如下参数。

            ``` {#codeblock_2zh_waq_ofn}
            log_bin=mysql_bin
            binlog_format=row
            server_id=2 //设置大于1的整数
            binlog_row_image=full //当自建MySQL的版本大于5.6时，则必须设置该项。
            ```

        2.  修改完成后，重启MySQL进程。

            ``` {#codeblock_2f1_a7o_rwd}
            mysql_dir/bin/mysqladmin -u root -p shutdown
            mysql_dir/bin/safe_mysqld &
            ```

            **说明：** 您需要将mysql\_dir替换为MySQL实际的安装目录。

    -   Windows操作系统操作步骤如下：
        1.  修改配置文件my.ini中的如下参数。

            ``` {#codeblock_5ts_dnv_sm0}
            log_bin=mysql_bin
            binlog_format=row
            server_id=2 //设置大于1的整数
            binlog_row_image=full //当自建MySQL的版本大于5.6时，则必须设置该项。
            ```

        2.  修改完成后，重启MySQL服务。

            **说明：** 您可以通过Windwos中的服务管理器重启服务，或使用如下命令重启服务：

            ``` {#codeblock_8u3_j21_yue}
            net stop mysql
            net start mysql
            ```



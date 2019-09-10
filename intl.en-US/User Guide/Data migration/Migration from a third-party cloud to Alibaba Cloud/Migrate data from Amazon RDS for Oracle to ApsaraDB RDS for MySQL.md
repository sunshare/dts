# Migrate data from Amazon RDS for Oracle to ApsaraDB RDS for MySQL {#concept_354108 .concept}

This topic describes how to migrate data from Amazon RDS for Oracle to ApsaraDB RDS for MySQL by using Data Transmission Service \(DTS\). DTS supports schema migration, full data migration, and incremental data migration. You can combine these methods to migrate databases without any service interruptions.

## Prerequisites {#section_8vl_bru_fm7 .section}

-   To ensure that DTS can access Amazon RDS for Oracle through the public network, you need to set **Public Availability** to **Yes** in Amazon RDS for Oracle.
-   The version of Amazon RDS for Oracle must be V10g, V11g, or V12c.
-   The version of ApsaraDB RDS for MySQL must be V5.6 or V5.7.
-   The storage space of your ApsaraDB RDS for MySQL instance is at least twice the size of the data to be migrated from Amazon RDS for Oracle.

    **Note:** The binlog files generated during the migration occupy some space. The files are automatically cleared after the migration is completed.


## Precautions {#section_3e9_huw_b09 .section}

-   If the source instance does not have a primary key or uniqueness constraint, and the fields in the table are not unique, there may be duplicate data in the destination database.
-   Table names under the ApsaraDB RDS for MySQL instance are case-insensitive. If you create a table whose name contains uppercase letters, ApsaraDB RDS for MySQL converts the uppercase letters to lowercase letters before the table is created.

    If the source database has tables that have the same name in different cases, the objects to be migrated may have the same name. This causes the "The object already exists" error to be displayed during schema migration. In this case, use the object name mapping feature provided by DTS to rename the objects with the same name when configuring the objects to be migrated. For more information, see [Object name mapping](https://www.alibabacloud.com/help/doc-detail/26628.htm).

-   If the database to be migrated does not exist in the ApsaraDB RDS for MySQL instance, DTS will automatically create a new database. However, in the following two cases, you need to [Create a database](https://www.alibabacloud.com/help/doc-detail/87038.htm#h2-url-6) in the destination ApsaraDB RDS for MySQL instance.
    -   The database name does not comply with the naming conventions of databases under ApsaraDB RDS for MySQL instances. For more information, see [Create a database](https://www.alibabacloud.com/help/doc-detail/87038.htm#h2-url-6).
    -   The name of the database to be migrated in the source Amazon RDS for Oracle instance is different from that in the destination ApsaraDB RDS for MySQL instance.

## Billing information {#section_md8_hm9_8ox .section}

|Migration type|Configuration fee|Public network traffic fee|
|--------------|-----------------|--------------------------|
|Full data migration|Not billed|Not billed|
|Incremental data migration|Billed. For more information, see [Data Transmission Service Pricing](https://www.alibabacloud.com/product/data-transmission-service/pricing).|Not billed|

## Migration type description {#section_w8i_cpf_jb6 .section}

-   Schema migration

    DTS supports schema migration of tables, indexes, constraints, and sequences. Other objects such as views, synonyms, triggers, stored procedures, stored functions, packages, and custom object types are not supported.

-   Full data migration

    DTS migrates all the existing data of objects from the Amazon RDS for Oracle database to the database in the ApsaraDB RDS for MySQL instance.

-   Incremental data migration

    In addition to migrating the existing data, DTS also captures the redo logs generated by the Amazon RDS for Oracle database. The incremental data of the Amazon RDS for Oracle database is synchronized to the ApsaraDB RDS for MySQL instance. Incremental data migration allows you to migrate Amazon RDS for Oracle databases without any service interruptions.


## SQL operations that can be synchronized during incremental data migration {#section_s2x_f4m_1yb .section}

-   INSERT, DELETE, and UPDATE
-   CREATE TABLE

    **Note:** The CREATE TABLE operations for creating partition tables or tables that contain functions cannot be synchronized.

-   ALTER TABLE operations, only including ADD COLUMN, DROP COLUMN, RENAME COLUMN, and ADD INDEX
-   DROP TABLE
-   RENAME TABLE, TRUNCATE TABLE, and CREATE INDEX

## Migration permission requirements {#section_14w_e9e_1h5 .section}

|Source database|Schema migration|Full data migration|Incremental data migration|
|:--------------|:---------------|:------------------|:-------------------------|
|Amazon RDS for Oracle database|Owner permissions on the schema to be migrated|Owner permissions on the schema to be migrated|Master user permissions|
|ApsaraDB RDS for MySQL instance|Read and write permissions on the destination database|Read and write permissions on the destination database|Read and write permissions on the destination database|

**How to create a database account and grant permissions to the account:**

-   For operations in an Amazon RDS for Oracle database, see [CREATE USER](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm) and [GRANT](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_9013.htm).
-   For operations in an ApsaraDB RDS for MySQL instance, see [Create an account](https://www.alibabacloud.com/help/doc-detail/87038.htm) and [Modify account permissions](https://www.alibabacloud.com/help/doc-detail/26188.htm).

## Data types and mappings {#section_8mi_nzd_9af .section}

There are some differences between MySQL and Oracle data types. Therefore, DTS maps the data types of Oracle and MySQL according to their definitions during schema migration. The following table describes the mappings of the data types.

|Oracle data type|MySQL data type|Supported by DTS|
|:---------------|:--------------|:---------------|
|varchar2\(n \[char/byte\]\)|varchar\(n\)|Yes|
|nvarchar2\[\(n\)\]|national varchar\[\(n\)\]|Yes|
|char\[\(n \[byte/char\]\)\]|char\[\(n\)\]|Yes|
|nchar\[\(n\)\]|national char\[\(n\)\]|Yes|
|number\[\(p\[,s\]\)\]|decimal\[\(p\[,s\]\)\]|Yes|
|float\(p\)\]|double|Yes|
|long|longtext|Yes|
|date|datetime|Yes|
|binary\_float|decimal\(65,8\)|Yes|
|binary\_double|double|Yes|
|timestamp\[\(fractional\_seconds\_precision\)\]|datetime\[\(fractional\_seconds\_precision\)\]|Yes|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|Yes|
|timestamp\[\(fractional\_seconds\_precision\)\]with localtimezone|datetime\[\(fractional\_seconds\_precision\)\]|Yes|
|clob|longtext|Yes|
|nclob|longtext|Yes|
|blob|longblob|Yes|
|raw|varbinary\(2000\)|Yes|
|long raw|longblob|Yes|
|bfile|-|**No**|
|interval year\(year\_precision\) to month|-|**No**|
|interval day\(day\_precision\)tosecond\[\(fractional\_seconds\_precision\)\]|-|**No**|

**Note:** 

-   A char column with a length greater than 255 Bytes is converted to the varchar\(n\) type.
-   MySQL does not support Oracle data types such as bfile, interval year to month, and interval day to second. Therefore, these data types are not converted during schema migration.

    The schema migration fails if the table to be migrated contains these three data types. When you select a migration object, you must exclude columns with these three data types from the object to be migrated.

-   The timestamp data type of MySQL does not contain the time zone information. However, Oracle provides timestamp with time zone and timestamp with local time zone data types that allow you to store datetime with the time zone information. Therefore, DTS converts the values of these data types from the current time zone to UTC for storage in the ApsaraDB RDS for MySQL instance.
-   Some tables may fail to be migrated because MySQL limits the row size of tables. In this case, you must exclude these tables from the object to be migrated, or adjust the fields in these tables to meet the requirements of MySQL.

## Premigration preparation {#section_rpp_s1n_ni7 .section}

1.  Log on to the Amazon RDS Management Console.
2.  Go to the Basic Information page of the source Amazon RDS for Oracle instance.
3.  In the Security group rules section, click the name of the security group corresponding to the existing inbound rule.

    ![Security group rules](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/150145/156808369741899_en-US.png)

4.  On the Security Groups page, click the Inbound tab in the Security Group section. On the Inbound tab, click Edit to add IP address ranges of the DTS server in the corresponding region to the inbound rule. For more information about the IP address ranges, see [DTS IP address ranges](https://www.alibabacloud.com/help/doc-detail/84900.htm).

    **Note:** 

    -   You only need to add the DTS IP address ranges corresponding to the region where the destination database is located. In this case, the source database is located in Singapore and the destination database is located in Hangzhou. You only need to add the DTS IP address ranges corresponding to China \(Hangzhou\).
    -   You can add the required IP address ranges to the inbound rule at one time.
    ![Edit AWS inbound rules](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/287980/156808369747942_en-US.png)

5.  Adjust log configuration of Amazon RDS for Oracle. Skip this step if you do not need to perform **incremental data migration**.
    1.  Use the Master User account and the SQL\*Plus tool provided by Oracle to connect to the Amazon RDS for Oracle database.
    2.  Run the `archive log list;` command to confirm that the Amazon RDS for Oracle instance is in archiving mode.

        **Note:** If the instance is not archived, enable the archiving mode. For more information, see [Managing Archived Redo Logs](https://docs.oracle.com/cd/E11882_01/server.112/e25494/archredo.htm#ADMIN008).

    3.  Enable the force logging mode.

        ``` {#codeblock_j30_tln_my5}
        exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);
        ```

    4.  Enable primary key supplemental logging.

        ``` {#codeblock_00y_w7j_bnk}
        begin rdsadmin.rdsadmin_util.alter_supplemental_logging(p_action => 'ADD',p_type => 'PRIMARY KEY');end;/
        ```

    5.  Enable unique key supplemental logging.

        ``` {#codeblock_m1e_ckd_4s1}
        begin rdsadmin.rdsadmin_util.alter_supplemental_logging(p_action => 'ADD',p_type => 'UNIQUE');end;/
        ```

    6.  Set the retention period of archived logs.

        **Note:** We recommend that you set the retention period of archived logs to at least 24 hours.

        ``` {#codeblock_0dj_app_pzd}
        begin rdsadmin.rdsadmin_util.set_configuration(name => 'archivelog retention hours', value => '24');end;/
        ```

    7.  Submit changes.

        ``` {#codeblock_br1_yzc_oa2}
        commit;
        ```


## Procedure {#section_tm5_nqk_12x .section}

1.  Log on to the [Data Transmission Service console](https://dts-intl.console.aliyun.com/).
2.  In the left-side navigation pane, click **Data Migration**.
3.  In the upper-right corner, click **Create Migration Task**.
4.  Configure the parameters of **Source and Destination Databases**.

    ![Configure Source and Destination Databases](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156808369747598_en-US.png)

    |Category|Parameter|Description|
    |:-------|:--------|:----------|
    |Task Name|-|     -   DTS automatically generates a name for each task. Task names are not required to be unique.
    -   You can modify task names as needed. We recommend that you specify meaningful names to help identify the tasks.
 |
    |Source Database|Instance Type|Select **User-Created Database with Public IP Address**.|
    |Instance Region|When the instance type is set to **User-Created Database with Public IP Address**, you do not need to set **Instance Region**. **Note:** Click **Get IP Address ranges of DTS** corresponding to **Instance Region** to obtain IP address ranges of the DTS server and add the obtained IP address ranges to the inbound rule of Amazon RDS for Oracle. For more information, see [Premigration preparation](#section_rpp_s1n_ni7).

 |
    |Database Type|Select **Oracle**.|
    |Hostname or IP Address|Enter the URL of the Amazon RDS for Oracle database. **Note:** You can query the database connection information on the Basic Information page of Amazon RDS for Oracle.

 ![Connection Address](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/287980/156808369747946_en-US.png)

|
    |Port Number|Enter the port of the Amazon RDS for Oracle database. The default value is **1521**.|
    |Instance Type|     -   Non-RAC Instance: After this option is selected, you also need to enter **SID**.
    -   RAC Instance: After this option is selected, you also need to enter **Service Name**.
 In this case, select Non-RAC Instance and enter SID.

 |
    |Database Account|Enter the account used to connect to the Amazon RDS for Oracle database. For permission requirements, see [Migration permission requirements](#section_14w_e9e_1h5).|
    |Database Password|Enter the password for the Amazon RDS for Oracle database account. **Note:** After the source database information is specified, click **Test Connectivity** corresponding to the **Database Password** to verify whether the specified information is correct. If the source database information is correct, the **Test Passed** message is displayed. If the **Test Failed** message is displayed, click **Diagnose** in the **Test Failed** message. Adjust the entered source database information as prompted.

 |
    |Destination Database|Instance Type|Select **RDS Instance**.|
    |Instance Region|Select the region of the destination instance.|
    |RDS Instance ID|Select the ID of the destination instance.|
    |Database Account|Enter the account used to connect to the database under the destination instance. For permission requirements, see [Migration permission requirements](#section_14w_e9e_1h5).|
    |Database Password|Enter the password for the account of the database under the destination instance. **Note:** After the destination database information is specified, click **Test Connectivity** corresponding to **Database Password** to verify whether the specified information is correct. If the destination database information is correct, the **Test Passed** message is displayed. If the **Test Failed** message is displayed, click **Diagnose** in the **Test Failed** message. Adjust the specified destination database information as prompted.

 |

5.  After the configuration is complete, click **Set Whitelist and Next** in the lower-right corner.

    **Note:** The IP address of the DTS server is automatically added to the whitelist of the destination RDS instance. This ensures that the DTS server can connect to the destination instance. After the migration is complete, you can delete the IP address of the DTS server from the whitelist if you no longer need it. For more information, see [Configure a whitelist](https://www.alibabacloud.com/help/doc-detail/43185.htm).

6.  Configure migration objects and types.

    ![Configure Migration Types and Objects](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156808369747602_en-US.png)

    |Parameter|Description|
    |:--------|:----------|
    |Migration Type|     -   If you only need to migrate the existing data, select **Schema Migration** and **Full Data Migration**.

**Note:** To ensure data consistency, do not write new data to the Amazon RDS for Oracle database during full data migration.

    -   If you need to migrate the data without stopping your business, select **Schema Migration**, **Full Data Migration**, and **Incremental Data Migration**.
 |
    |Available| Select the database to be migrated from the Available section, and click the ![>](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/156808369740698_en-US.png) icon to move the database to the Selected section.

 **Note:** 

    -   The object to be migrated can be a database, a table, or a column.
    -   After an object is migrated to the destination RDS instance, the name of the object remains the same as that in the Amazon RDS for Oracle database. If the object to be migrated has a different name in the destination instance, you can use the object name mapping feature provided by DTS. For more information, see [Object name mapping](https://help.aliyun.com/document_detail/26628.html).
 |

7.  In the lower-right corner, click **Precheck**.

    **Note:** 

    -   A precheck is performed before a migration task starts. The migration task starts only after the precheck succeeds.
    -   If the precheck fails, click the ![Note](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17095/156808369847468_en-US.png) icon corresponding to the failed items to view their details. Perform the precheck again after you have rectified the failed items.
8.  After the precheck succeeds, click **Next**.
9.  On the Confirm Settings page, set **Channel Specification** and select **Data Transmission Service \(Pay-As-You-Go\) Service Terms**.
10. Click **Buy and Start** to start the migration.
    -   Full data migration

        Do not manually stop a migration task, because the system may fail to migrate the full data of the database. Wait until the migration task stops automatically.

    -   An incremental data migration task does not automatically end. You need to manually end the migration task.

        **Note:** Select an appropriate time point to manually end the migration task. For example, you can end the migration task during off-peak hours for business or before you migrate your business to the RDS instance.

        1.  When the status of the migration task is **The migration task is not delayed**, stop writing data to the source database for several minutes. The latency may be displayed.
        2.  When the status of the migration task becomes **The migration task is not delayed** again, stop the migration task manually.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17104/156808369847604_en-US.png)

        3.  Migrate business to the RDS instance.

## Subsequent operations {#section_g9e_88u_81v .section}

The database account used for data migration has read and write permissions. To ensure database security, delete the accounts of both source and destination databases after the migration is complete.

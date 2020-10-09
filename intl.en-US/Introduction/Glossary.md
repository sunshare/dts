# Glossary

The following table describes the terms that are frequently mentioned in the Data Transmission Service \(DTS\) documentation.

|Term|Description|
|:---|:----------|
|precheck|The system performs a precheck before starting a data migration task, data synchronization task, or change tracking task. In this process, the following items are checked:-   Connectivity between the DTS server and the source and target databases
-   Database account permissions
-   Whether binary logging is enabled
-   Database version numbers

**Note:** If the precheck fails, click the information icon ![Information icon](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/3457359951/p47468.png) next to each failed item to view the details. Follow the instructions to fix the issues and run the precheck again |
|schema migration|In the schema migration phase, DTS migrates schema objects from the source database to the target database, including tables, views, triggers, and stored procedures.

To migrate schema objects across heterogeneous databases, DTS converts schema objects into data types that work with the target database. For example, it converts the NUMBER data type in Oracle databases to the DECIMAL data type in MySQL databases. |
|full data migration|In the full data migration phase, DTS replicates the existing data from the source database to the target database. If you select only schema migration and full data migration for the task, updates that occur during the full data migration phase will not be captured and replicated to the target database.

**Note:** To ensure data consistency, we recommend that you suspend all updates to the source database during the full data migration phase. To migrate data with minimized downtimes, you must select schema migration, full data migration, and incremental data migration when creating the data migration task. |
|incremental data migration|In the incremental data migration phase, DTS continuously replicates ongoing changes between the source and target data stores. This function is typically used in minimized-downtime migrations to replicate changes that occur during the full data migration phase.

**Note:** Data synchronization is a continuous process that does not automatically end. To stop the process, you need to manually stop the data migration task. |
|initial synchronization|DTS replicates the existing schema objects and data to the target database before replicating ongoing changes. Initial synchronization includes initial schema synchronization and initial full data synchronization.

-   Initial schema synchronization: replicates the schema objects corresponding to your selected objects from the source database to the target database.
-   Initial full data synchronization: replicates the existing data of your selected objects from the source database to the target database. |
|replication performance|The replication performance is a measurement based on the number of records that are replicated to the target database per second. The unit is records per second \(RPS\). For more information, see [Specifications of data synchronization channels]().|
|synchronization latency|The synchronization latency is a measurement based on the difference between the timestamp of the latest data record synchronized to the target database and the current timestamp of the source database. If the synchronization latency is zero, the source database is in sync with the target database. |
|data update|Data updates are data manipulation language \(DML\) operations that modify data, without modifying the schema, such as INSERT, DELETE, and UPDATE operations.|
|schema update|Schema updates are data definition language \(DDL\) operations that modify schema objects, such as CREATE TABLE, ALTER TABLE, and DROP VIEW operations.|
|timestamp range|The timestamp range is the range of timestamps for the incremental data that is stored in a change tracking task. By default, the change tracking task keeps updates for 24 hours. DTS regularly cleans expired incremental data and updates the timestamp range of the change tracking channel.

**Note:** The timestamp of the incremental data is the timestamp when the incremental data is updated in the source database and written into the transaction log. |
|consumption checkpoint|The consumption checkpoint is the timestamp of the latest data update that is consumed by a consumer application.

Every time that a consumer client consumes and commits a data update, DTS marks the latest commit time as the consumption checkpoint. If the consumer client stops responding, DTS automatically resumes from the consumption checkpoint when sending updates to the next healthy consumer. |


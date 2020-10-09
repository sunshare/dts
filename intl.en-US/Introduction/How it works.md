# How it works

This topic describes the architecture of Data Transmission Service \(DTS\) and how it works in each replication mode.

## DTS architecture

The architecture is illustrated in the following figure:

![DTS architecture diagram](../images/p147359.png "DTS architecture")

The architecture of Data Transmission Service provides the following features:

-   Primary/secondary redundancy:

    Each function of DTS is deployed on multiple servers with primary/secondary redundancy. The HA manager continuously performs health checks on each server. If one server functions abnormally, the workloads on that server are switched to a healthy server with minimal delay.

-   Endpoint change detection:

    For continuous data replications, such as data synchronization and change tracking, the HA manager detects the changes made to the endpoints of source ApsaraDB instances. If an instance endpoint has changed, the HA manager reconfigures the data source to keep the data connection functioning.


## How DTS works in data migration mode

A data migration task consists of several phases, namely schema migration, full data migration, and incremental data migration. To keep the source data operational during migration, you must select all of these phases in the migration task configuration wizard.

Before migrating data, DTS needs to re-create the schema in the target database. For heterogeneous migrations, DTS parses the data definition language \(DDL\) code of the source database, translates the code into the syntax of the target database, and then re-creates the schema objects in the target database.

In the full data migration phase, DTS replicates the existing data from the source database to the target database. The source database remains operational and updates are continuously made during the migration process. DTS uses an incremental data reader to capture the ongoing changes that occur during the full data migration process. The incremental data reading is activated when the full data migration starts. During the full data migration phase, incremental data is parsed, reformatted, and stored locally on the DTS server.

When the full data migration process is complete, DTS retrieves the incremental data stored locally, reformats it again, and applies the incremental changes in the target database. This process continues until all ongoing updates are replicated to the target database and the source and target databases are in sync.

The data migration mode is illustrated in the following figure:

![Data migration process](../images/p132019.png "Data migration process")

## How DTS works in data synchronization mode

The data synchronization mode of DTS replicates ongoing changes between two data stores. This mode is typically used for OLTP-to-OLAP replications. In this mode, a migration task consists of the following two phases:

-   Initial data load: DTS loads the existing data from the source database to the target database.
-   Ongoing replication: DTS replicates ongoing changes and keeps the source and target databases in sync.

To replicate ongoing changes, DTS uses two components that work with the transaction log:

-   Transaction log reader: The transaction log reader communicates with the source database using the corresponding protocol to read the transaction log. For example, it uses Binglog Dump to read transaction log data from ApsaraDB for MySQL databases.
-   Transaction log applier: The transaction log applier retrieves data updates from the transaction log reader, filters the updates to keep only ones related to the objects being replicated, and applies the updates to the target database. When doing this, the transaction log applier maintains the ACID properties of transactions. Both the transaction log reader and the transaction log applier are based on redundant deployments. The HA manager checks the health condition of each server. If an anomaly occurs, the execution of the transaction log is resumed on a healthy server.

The data synchronization mode is illustrated in the following figure:

![Data synchronization process](../images/p132020.png "Data synchronization process")

## How DTS works in change tracking mode

The change tracking replication mode of DTS captures data updates and exposes them as a publisher/subscriber stream. You can customize the consumption mechanism for your different applications.

The log processor communicates with the source database using the corresponding protocol to read the transaction log. For example, it uses Binglog Dump to read transaction log data from ApsaraDB for MySQL databases. Then, the log processor parses the transaction log data, filters the data, normalizes the update records, and keeps the processed data in persistence.

The log processor is based on redundant deployments. The HA manager checks the health condition of each server. If an anomaly occurs, the workloads of transaction log reading are resumed on a healthy server.

The change tracking mode is illustrated in the following figure:

![Change tracking process](../images/p139300.png "Change tracking process")


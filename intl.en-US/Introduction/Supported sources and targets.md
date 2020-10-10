# Supported sources and targets

This topic describes the supported data migration paths \(source-target pairs\) and replication features of all replication modes. For each migration path, the supported cloud-based and user-created databases and their corresponding replication features are listed.

## Data migration

The data migration mode migrates your data between data stores. This mode is typically used for one-time migrations.

A user-created source or target database, such as a MySQL, SQL Server, or Oracle database, can be based on one of the following hosting and network deployments:

-   User-created database with public IP address
-   Database without public IP:Port \(Accessed through database gateway\)
-   User-created database accessed through Cloud Enterprise Network\(CEN\)
-   User-created database hosted on ECS Instance
-   User-created database connected over Express Connect, VPN Gateway, or Smart Access Gateway

|Source database|Target database|Migration phases|
|:--------------|:--------------|:---------------|
|-   User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

-   ApsaraDB RDS for MySQL

All versions


|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|Apsara PolarDB for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|Distributed Relational Database Service \(DRDS\)

MySQL 5.x

**Note:** Apsara PolarDB for MySQL clusters are not supported.

|-   Full data migration
-   Incremental data migration |
|AnalyticDB for MySQL

2.0 and 3.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created PostgreSQL database

9.4, 9.5, 9.6, 10.x, 11.x, and 12

|-   Full data migration
-   Incremental data migration |
|User-created Oracle database \(RAC and non-RAC deployments\)

9i, 10g, 11g, 12c, 18c, and 19c

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB for MariaDB TX

10.3

|ApsaraDB for MariaDB TX

10.3

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|Apsara PolarDB for MySQL

All versions

|Apsara PolarDB for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|AnalyticDB for MySQL

2.0 and 3.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|Apsara PolarDB-O

All versions

|Apsara PolarDB-O

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created Oracle database \(RAC and non-RAC deployments\)

9i, 10g, 11g, 12c, 18c, and 19c

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|-   User-created SQL Server database

2005, 2008, 2008 R2, 2012, 2014, 2016, and 2017

**Note:**

    -   SQL Server Cluster and SQL Server Always On availability groups are not supported.
    -   If the version of the source database is 2005, incremental data migration is not supported.
-   ApsaraDB RDS for SQL Server

2008, 2008 R2, 2012, 2014, 2016, and 2017

**Note:** If the version of the source database is 2008 or 2008 R2, incremental data migration is not supported.


|User-created SQL Server database

2005, 2008, 2008 R2, 2012, 2014, 2016, and 2017

**Note:** SQL Server Cluster and SQL Server Always On availability groups are not supported.

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for SQL Server

2008, 2008 R2, 2012, 2014, 2016, and 2017

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created Oracle database \(RAC and non-RAC deployments\)

9i, 10g, 11g, 12c, 18c, and 19c

|User-created Oracle database \(RAC and non-RAC deployments\)

9i, 10g, 11g, 12c, 18c, and 19c

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB for PolarDB-O

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for PPAS

9.3 and 10

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|Distributed Relational Database Service \(DRDS\)

MySQL 5.x

**Note:** Apsara PolarDB for MySQL clusters are not supported.

|-   Full data migration
-   Incremental data migration |
|AnalyticDB for MySQL

2.0 and 3.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|-   User-created PostgreSQL database

9.4, 9.5, 9.6, 10.x, 11.x, and 12

-   ApsaraDB RDS for PostgreSQL

9.4, 10, 11, and 12


|-   User-created PostgreSQL database

9.4, 9.5, 9.6, 10.x, 11.x, and 12

-   ApsaraDB RDS for PostgreSQL

9.4, 10, 11, and 12


|-   Schema migration
-   Full data migration
-   Incremental data migration |
|-   User-created MongoDB database \(single-node, replica set, or sharded cluster deployment\)

3.0, 3.2, 3.4, 3.6, 4.0, and 4.2

-   ApsaraDB for MongoDB instance \(single-node or replica set deployment\)

All versions


|-   User-created MongoDB database \(single-node, replica set, or sharded cluster deployment\)

3.0, 3.2, 3.4, 3.6, 4.0, and 4.2

-   ApsaraDB for MongoDB instance \(single-node, replica set, or sharded cluster deployment\)

All versions


|-   Full data migration
-   Incremental data migration

**Note:** As a NoSQL database, MongoDB does not require schema migration. |
|User-created Redis database \(single-node deployment\)

2.8, 3.0, 3.2, 4.0, and 5.0

|User-created Redis database \(single-node and cluster deployment\)

2.8, 3.0, 3.2, 4.0, and 5.0

|-   Full data migration
-   Incremental data migration

**Note:** As a NoSQL database, Redis does not require schema migration. |
|ApsaraDB for Redis instance \(single-node and cluster deployment\)

Community versions 4.0 and 5.0

|-   Full data migration
-   Incremental data migration |
|User-created TiDB database|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|ApsaraDB RDS for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|Apsara PolarDB for MySQL

All versions

|-   Schema migration
-   Full data migration
-   Incremental data migration |
|User-created Db2 database

9.7 and 10.5

|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|-   Schema migration
-   Full data migration
-   Incremental data migration |

## Data synchronization

The data synchronization mode replicates ongoing updates between data stores. This mode is typically used for replication between server nodes in a highly available system based on a redundant and distributed deployment. This mode is also used for OLTP-to-OLAP replications to allow for real-time analytics.

A user-created source or target database, such as a MySQL or Redis database, can be based on one of the following hosting and network deployments:

-   User-created database hosted on ECS Instance
-   User-created database connected over Express Connect, VPN Gateway, or Smart Access Gateway
-   Database without public IP:Port \(Accessed through database gateway\)
-   User-created database accessed through Cloud Enterprise Network \(CEN\)

|Source database|Target database|Initial data load type|Synchronization directionality|
|:--------------|:--------------|:---------------------|------------------------------|
|-   User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

-   ApsaraDB RDS for MySQL

All versions


|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication

Two-way replication |
|ApsaraDB RDS for MySQL

All versions

|Initial schema synchronization

Initial full data synchronization

|One-way replication

Two-way replication |
|Apsara PolarDB for MySQL

All versions

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|AnalyticDB for MySQL

2.0 and 3.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|AnalyticDB for PostgreSQL \(formerly known as HybridDB for PostgreSQL\)

4.3 and 6.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|Elasticsearch

5.5, 6.3, 6.7, and 7.4

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|MaxCompute

All versions

|Initial schema synchronization

Initial full data synchronization

|One-way synchronization|
|User-created Kafka database

Cluster versions 0.10 and 1.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|DRDS MySQL 5.x

**Note:** ApsaraDB RDS for MySQL instances \(MySQL 8.0\) or Apsara PolarDB for MySQL clusters are not supported

|DRDS MySQL 5.x

**Note:** ApsaraDB RDS for MySQL instances \(MySQL 8.0\) or Apsara PolarDB for MySQL clusters are not supported

|Initial full data synchronization|One-way replication |
|AnalyticDB for MySQL

2.0 and 3.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|AnalyticDB for PostgreSQL

\(Previous name: HybridDB for PostgreSQL\)

4.3 and 6.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|Apsara PolarDB for MySQL

All versions

|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|ApsaraDB RDS for MySQL

All versions

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|Apsara PolarDB for MySQL

All versions

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|AnalyticDB for MySQL

2.0 and 3.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|User-created Kafka database

Cluster versions 0.10 and 1.0

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|Elasticsearch

5.5, 6.3, and 6.7

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|MaxCompute

All versions

|Initial schema synchronization

Initial full data synchronization

|One-way replication |
|-   ApsaraDB RDS for PostgreSQL

9.4, 10, 11, and 12

-   User-created PostgreSQL database

9.x to 12


|AnalyticDB for PostgreSQL \(formerly known as HybridDB for PostgreSQL\)

4.3 and 6.0

|Initial full data synchronization

|One-way replication |
|-   User-created Redis database \(single-node and cluster deployment\)

2.8, 3.0, 3.2, 4.0, and 5.0

-   ApsaraDB for Redis instance \(standard, read/write splitting, and cluster instances\)

Community versions 4.0 and 5.0

-   ApsaraDB for Redis Enhanced Edition instance \(standard, read/write splitting, and cluster instances\)

5.0


|-   User-created Redis database \(single-node or cluster deployment\)

2.8, 3.0, 3.2, 4.0, and 5.0

-   ApsaraDB for Redis instance \(standard, read/write splitting, and cluster instances\)

Community versions 4.0 and 5.0

-   ApsaraDB for Redis Enhanced Edition instance \(standard, read/write splitting, and cluster instances\)

5.0


|Initial full data synchronization

**Note:** Redis is a NoSQL database that does not require initial schema synchronization.

|One-way replication

Two-way replication

**Note:** Only ApsaraDB for Redis Enhanced Edition instance \(5.0\) support two-way synchronization |

## Change tracking

The change tracking mode captures data updates and expose them as a publish/subscribe stream of updates. This mode is typically used for systems that require asynchronous replication for better performance over mission-critical workloads.

A user-created source database, such as MySQL, can be based on one of the following hosting and network deployments:

-   User-created database hosted on ECS Instance
-   User-created database connected over Express Connect, VPN Gateway, or Smart Access Gateway
-   Database without public IP:Port \(Accessed through database gateway\)
-   User-created database with public IP address

|Source database|Dat update types|
|---------------|----------------|
|User-created MySQL database

5.1, 5.5, 5.6, 5.7, and 8.0

|-   Data updates
-   Schema updates |
|ApsaraDB RDS for MySQL

All versions |
|User-created Oracle database

9i, 10g, and 11g |
|PolarDB MySQL 5.6, 6.7, 8.0 |


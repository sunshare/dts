# What is Data Transmission Service?

Alibaba Cloud Data Transmission Service \(DTS\) helps you migrate data between data stores, such as relational databases, NoSQL databases, and data warehouses. You can use DTS to migrate your data to Alibaba Cloud or between combinations of cloud and on-premises data systems.

DTS supports several data replication modes, including data migration, data integration, data synchronization, and change tracking. You can choose a combination of data replication modes that best suit your use cases.

## Features

As a managed service, DTS offers the following advantages over traditional data replication tools:

-   Provides highly stable data transmission.
-   Helps you manage data migration between your data stores so that you can focus on developing applications.
-   Provides several data replication modes, including data migration, data integration, data synchronization, and change tracking.
-   Supports data migration between data stores that are based on different engines and architectures.
-   Supports a variety of database engines as sources and targets.
-   Supports resumable transmission that addresses transmission interruptions caused by hardware and network failures.
-   Helps you build a distributed data system that is scalable and highly available.
-   Supports RAM-based authorization that allows you to define fine-grained authorization policies for managing DTS tasks.
-   Supports scheduled migration tasks for handling recurring data migration workloads.

## Data replication modes

DTS supports several data replication modes, each designed for a different scenario.

|Mode|Description|Related topics|
|----|-----------|--------------|
|Data migration|In this mode, DTS migrates your data between data stores. This mode is typically used for one-time migrations that require minimized downtimes. The incremental data migration phase of this mode can replicate ongoing changes in real time.|-   [Data migration mode](/intl.en-US/Replication Modes/Data migration mode.md)
-   [Data migration procedure list](/intl.en-US/Data Migration/Data migration procedure list.md) |
|Data integration|In this mode, you can schedule data migration tasks to migrate data on a regular basis. This mode is typically used for recurring migrations in a large data warehousing system. For example, you can schedule a data migration that recurs every night to transfer the transactional data collected during the day to a data warehouse.|-   [Data integration mode](/intl.en-US/Replication Modes/Data integration mode.md) |
|Data synchronization|In this mode, DTS replicates changes between data stores in real time. This mode is usually used for data replication between databases in a distributed system, for example, for redundancy and high availability. It supports both one-way and two-way replications. Compared with the incremental data migration phase of data migration, the data synchronization mode delivers higher performance and lower lag.|-   [Data synchronization mode](/intl.en-US/Replication Modes/Data synchronization mode.md)
-   [Data synchronization procedure list](/intl.en-US/Data Synchronization/Data synchronization procedure list.md) |
|Change tracking|In this mode, DTS captures your changes and exposes the stream of changes as a publish/subscribe service.|-   [Change tracking mode](/intl.en-US/Replication Modes/Change tracking mode.md) |

## Instances and tasks

DTS provisions required resources for your replication workloads in the form of an instance. Instances are available with different instance sizes and billing methods. Different instance sizes deliver different data transmission capacities for your varied performance requirements. A combination of instance size and billing method determines how much you are charged for the use of the DTS service.

A task defines configurations for a data replication workload, such as the connections to the source and target data stores. You can also view the task status to track the progress of your replication workloads. A task can be created in one of the replication modes. Instances can support two tasks in data synchronization mode, which allows you to perform two-way data synchronization. In all other replication modes, instances can only support one task. After a task is created, its data replication mode cannot be changed. If you want to change the data replication mode that you are using, you must stop the task, then create a new instance and an associated task in the desired replication mode.

For different data replication modes, the process for creating instances and tasks is different. For example, when you work in data migration mode, you create both an instance and task at the same time. In data synchronization or change tracking mode, you create an instance first and then create a task for the instance.


# Use cases

Data Transmission Service \(DTS\) supports data replication in several modes, including data migration, change tracking, and data synchronization. You can use a combination of data replication modes that best suit your use cases.

## Database migration with minimized downtime

**Applicable replication mode**: data migration

To ensure data consistency, traditional migration processes require that you stop writing data to the source database during data migration. Depending on the data volume and network conditions, the migration process may take several hours or even days. This prolonged process may have a great impact on your business.

DTS can migrate your data with minimized downtime. Your applications remain operational during migration. The only downtime is when you switch your application to the new target database. You can usually limit this switchover window to minutes. The following figure shows the process of data migration.

![Architecture of data migration](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4500168951/p46583.png)

The data migration process includes several phases, namely schema migration, full data migration, and incremental data migration. During incremental data migration, ongoing changes that occur in the source are replicated to the target in near real time. When the migration is complete, you can verify that the migrated data schema and data in the target database are fully compatible with your application. Then, you can switch your application to the target database.

## Geo-redundancy

**Applicable replication mode**: data synchronization

If your application is deployed in a single region, service interruption may occur because of failures that are rare but inevitable, such as power failures and network disconnections.

In this case, you can build a secondary deployment in a different region to increase the availability of your application. DTS continuously replicates changes between these two geo-redundant deployments and keep the regional replicas in sync. If a failure occurs in the primary region, you can switch user requests to the secondary region.

![Geo-redundancy diagram](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1246858951/p147420.png)

## Active geo-redundancy

**Applicable replication mode**: data synchronization

As your business grows, you may encounter the following issues if you deploy your application in a single region:

-   Users access your application from a wide range of geographical locations. Distant users may experience high network latency and slow performance.
-   The scalability is limited by the capacity of infrastructure in a single region, such as network bandwidth.

To address these challenges, you can build multiple business units in the same city or across different cities. DTS performs two-way real-time data replication between business units to keep the regional replicas in sync. If a failure occurs in a business unit, you only need to switch the traffic to another business unit. The application can be recovered within seconds. In this way, you achieve higher availability based on redundancy of multiple business units.

You can also distribute traffic across business units. For example, you can redirect the traffic bound for each business unit to the server closest to the user. This reduces network latency and improves user experience.

![Active geo-redundancy diagram ](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1246858951/p147401.png)

## Integration with cloud BI systems

**Applicable replication mode**: data synchronization

Alibaba Cloud provides a complete portfolio of BI offerings. To leverage cloud-based BI capabilities, you need to integrate your applications and the BI data stores of Alibaba Cloud. DTS allows you to replicate data in real time from user-created databases to Alibaba Cloud BI data stores, such as MaxCompute.

![BI systems diagram](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1246858951/p147419.png)

## Real-time analytics

**Applicable replication mode**: change tracking

Data analytics is essential in improving enterprise insights and user experience. With the ability to analyze real-time data, enterprises can adjust marketing strategies and adapt to changing markets and higher demands for better user experience.

With the change tracking mode of DTS, you can subscribe to incremental data without disrupting your online transactions. You can use a standard Kafka client SDK to stream the subscribed incremental data to the analytics system so you can perform analytics tasks on the latest transactional data.

![Real-time analytics diagram](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1246858951/p147416.png)

## Simple cache updating

**Applicable replication mode**: change tracking

To improve the response times of your application, a commonly used strategy is to introduce a memory-cached read handler to increase the performance of concurrent read requests. All read requests are directed to the read handler. The read handler can process read requests with better performance using its in-memory retrieval.

With the change tracking mode of DTS, you can implement a simple cache updating mechanism by subscribing to the changes committed to the primary database and updating the cache in near real time.

![Simple caching diagram](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1246858951/p147417.png)

This architecture provides the following benefits:

-   Low-latency write commits

    The write request is completed immediately after the update is committed to the database. The cache updating operation is handled in a parallel process. The application does not have to wait for the cache to be updated.

-   Simplified application logic

    The application does not have to write the same update into two replicas. Instead, it only needs to subscribe to the stream of updates and update the cache accordingly in a parallel process.

-   No performance overheads on database

    DTS retrieves data updates from the redo log. This process does not interfere with the normal transactions of the read/write database and does not incur performance overheads.


## Decoupled components and asynchronous processing

**Applicable replication mode**: change tracking

A typical online shopping system consists of such components as order management, inventory management, and shipping management. In a synchronous processing model, an order placement action has to integrate all dependent actions that are to be performed in the corresponding components. This means that the user has to wait until all the updates are committed before they have the order confirmed. The synchronous processing model has the following weaknesses:

-   The order placement action takes a long time to complete.
-   With multiple actions integrated into one step, a single fault in any of the components may fail the entire function.

To resolve these issues, you can deploy a system with decoupled components, where each transaction triggers asynchronous updates to the corresponding component databases. Using the change tracking mode of DTS, components can subscribe to data updates and perform follow-up processing accordingly. This architecture decouples core components from other components so that core components can function more stably.

The following figure shows the asynchronous processing across decoupled components.

In this scenario, the order management component returns the result immediately after the user places an order. The change tracking replication mode of DTS captures data updates and exposes them as a publisher/subscriber stream. Other components can use a standard Kafka client to subscribe to the data updates and then perform follow-up processing, such as inventory updates and shipping processing.

This architecture has been adopted in a wide range of businesses of Alibaba Group. Every day, tens of thousands of applications subscribe to the updates made to the Taobao order management system using the change tracking mode of DTS and perform follow-up processing in parallel.

![Decoupled components diagram](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1246858951/p147418.png)

## Scalable read capacity

**Applicable replication mode**: data synchronization

To handle massive amounts of concurrent read requests, you can distribute the workloads among multiple read-only database instances. To achieve this, you can use the data synchronization mode of DTS to replicate data to read-only instances in real time. This scale-out architecture allows you to handle ultra-high concurrent read workloads.

## Scheduled replication for data warehousing

**Applicable replication mode**: data migration

For a large online application that handles massive amounts of transactional data every day, you may need to adopt a next-day warehousing strategy to transfer data into data warehouses on a regular basis. For example, you want to schedule migrations to start during off-peak hours to replicate transactional data of the day to data warehouses. With this deployment, your analytics systems can work on one-day-old data.


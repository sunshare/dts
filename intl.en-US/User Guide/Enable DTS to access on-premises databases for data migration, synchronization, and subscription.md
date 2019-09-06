# Enable DTS to access on-premises databases for data migration, synchronization, and subscription {#concept_1340353 .concept}

You must modify the security settings of an on-premises database when you want to use Data Transmission Service \(DTS\) to migrate, synchronize, or subscribe to the database. You need to add the CIDR blocks of the DTS servers to the security settings of the database to enable access to the database. This topic describes how to modify the security settings for the following on-premises databases: **User-Created Database with Public IP Address** and **User-Created Database Connected Over Express Connect, VPN Gateway, or Smart Access Gateway**.

**Note:** Alibaba Cloud ApsaraDB provides multiple database engines, such as ApsaraDB RDS for MySQL and ApsaraDB for MongoDB. For ApsaraDB databases and user-created databases running on Elastic Compute Service \(ECS\) instances, you do not need to modify their security settings. DTS will automatically add its server IP addresses to the whitelist of the ApsaraDB databases or the security rules of the ECS instances.

## Data migration and subscription {#section_rzg_bqk_s23b .section}

-   **Data migration** 

    When you configure data migration, if a **User-Created Database with Public IP Address** or **User-Created Database Connected Over Express Connect, VPN Gateway, or Smart Access Gateway** is specified as the source or destination database, then you must modify the security settings of both the source and destination databases. Add the CIDR blocks of the DTS servers in the region where the destination database is deployed to the security settings.

    For example, the source database is deployed in the China \(Shenzhen\) region, and the destination database is deployed in the China \(Hangzhou\) region. In this case, you must add the CIDR blocks of the DTS servers in the China \(Hangzhou\) region to the security settings of both the source and destination databases.

-   **Data subscription** 

    If you want to subscribe to a **User-Created Database with Public IP Address** or **User-Created Database Connected Over Express Connect, VPN Gateway, or Smart Access Gateway**, then you must modify the security settings of the database. Add the CIDR blocks of the DTS servers in the region where the database is deployed to the security settings.


|Region|CIDR block|
|:-----|:---------|
|China \(Hangzhou\)|101.37.14.0/24, 114.55.89.0/24, 115.29.198.0/24, 118.178.120.0/24, 118.178.121.0/24, 120.26.106.0/24, 120.26.116.0/24, 120.26.117.0/24, 120.26.118.0/24, 120.55.192.0/24, 120.55.193.0/24, 120.55.194.0/24, 120.55.241.0/24, 121.40.125.0/24, 121.196.246.0/24, 10.25.87.0/24, 10.27.68.0/24, 10.27.69.0/24, 10.28.141.0/24, 10.28.143.0/24, 10.46.74.0/24, 10.46.75.0/24, 10.46.77.0/24, 10.47.49.0/24, 10.47.50.0/24, 10.47.163.0/24, 10.51.4.0/24, 10.51.5.0/24, 10.165.13.0/24, 10.171.253.0/24, 10.252.83.0/24, 100.104.52.0/24, 101.37.12.0/24, 101.37.13.0/24, 101.37.15.0/24, 10.241.104.0/24, 10.28.140.0/24, 101.37.25.0/24, 47.96.39.0/24, 118.31.184.0/24, 118.31.165.0/24, 118.31.246.0/24, 10.80.113.0/24, 10.80.67.0/24, 10.31.121.0/24, 10.80.62.0/24, 10.80.221.0/24, 120.55.12.0/24, 10.135.204.0/24, 10.81.87.0/24, 10.81.88.0/24, 47.97.7.0/24, 10.81.54.104, 10.81.178.51, 47.97.27.142, 47.97.73.210, 10.81.64.74, 10.46.228.166, 10.46.228.110, 10.46.224.18, 10.46.224.71, 10.81.46.23, 121.43.162.118, 121.43.185.141, 121.196.211.16, 114.55.125.94, 121.43.179.168, and 121.43.174.187.|
|China \(Shanghai\)|139.196.17.0/24, 139.196.18.0/24, 139.196.25.0/24, 139.196.27.0/24, 139.196.154.0/24, 139.196.116.0/24, 139.196.254.0/24, 10.24.169.0/24, 10.47.80.0/24, 10.47.10.0/24, 10.174.66.0/24, 10.174.116.0/24, 10.174.120.0/24, 10.174.121.0/24, 10.174.150.0/24, 100.104.205.0/24, 139.196.166.0/24, 10.27.142.0/24, 10.27.146.0/24, 10.27.145.0/24, 106.14.46.0/24, 106.14.37.0/24, 106.14.36.0/24, 106.15.250.0/24, 101.132.248.0/24, 47.100.95.0/24, 10.81.129.0/24, 10.31.207.0/24, 10.81.130.0/24, 106.15.73.0/24, 106.15.75.0/24, 47.100.137.0/24, 10.30.232.0/24, 10.30.233.0/24, 10.30.234.0/24, 10.30.235.0/24, 106.14.177.89, 106.14.178.118, 139.196.138.36, 106.14.4.132, 139.196.92.27, 139.196.143.11, and 10.81.204.0/24.|
|China \(Qingdao\)|115.28.200.0/24, 115.28.216.0/24, 115.28.226.0/24, 115.28.247.0/24, 118.190.133.0/24, 120.27.53.0/24, 10.31.69.0/24, 10.144.88.0/24, 10.144.153.0/24, 10.161.39.0/24, 10.161.59.0/24, 10.252.29.0/24, and 100.104.72.0/24.|
|China \(Beijing\)|112.126.80.0/24, 112.126.87.0/24, 112.126.91.0/24, 112.126.92.0/24, 123.56.108.0/24, 123.56.120.0/24, 123.56.137.0/24, 123.56.148.0/24, 123.56.164.0/24, 123.57.48.0/24, 182.92.153.0/24, 182.92.186.0/24, 10.51.50.0/24, 10.51.119.0/24, 10.51.120.0/24, 10.170.194.0/24, 10.170.196.0/24, 10.170.232.0/24, 10.170.237.0/24, 10.170.247.0/24, 10.171.15.0/24, 10.171.117.0/24, 10.172.244.0/24, 100.104.183.0/24, 101.200.174.0/24, 101.200.160.0/24, 101.200.176.0/24, 10.51.82.0/24, 10.44.137.0/24, 10.44.141.0/24, 47.94.36.0/24, 47.94.47.0/24, 10.31.155.0/24, 10.31.144.0/24, 10.26.26.0/24, 10.26.27.0/24, 101.201.214.0/24, 101.201.82.0/24, 123.56.182.0/24, 10.24.188.0/24, 101.201.105.0/24, 182.92.132.0/24, 60.205.157.0/24, 10.24.191.0/24, 10.24.189.0/24, 10.26.168.0/24, 101.201.107.0/24, 60.205.164.0/24, 60.205.165.0/24, 59.110.4.0/24, 10.24.192.0/24, 10.26.66.0/24, 10.26.101.0/24, 59.110.17.0/24, 123.56.186.0/24, 60.205.146.0/24, 10.27.33.0/24, 10.26.171.0/24, 10.24.193.0/24, 59.110.37.0/24, 59.110.19.0/24, 60.205.112.0/24, 60.205.243.0/24, 10.26.164.0/24, 10.26.166.0/24, 10.26.167.0/24, 101.201.108.0/24, 59.110.38.0/24, 60.205.197.0/24, 60.205.166.0/24, 10.24.195.0/24, 10.26.162.0/24, 10.26.161.0/24, 10.26.68.0/24, and 10.26.100.0/24.|
|China \(Zhangjiakou\)|47.92.22.0/24, 11.192.243.0/24, 100.104.175.0/24, and 11.112.227.0/24.|
|China \(Hohhot\)|100.104.72.0/24, 39.104.29.0/24, and 11.193.183.0/24.|
|China \(Shenzhen\)|112.74.0.0/16, 120.24.0.0/16, 120.25.0.0/16, 10.116.0.0/16, 10.169.0.0/16, 10.170.40.0/24, 10.170.43.0/24, 100.104.205.0/24, 10.44.0.0/16, and 10.24.0.0/16.|
|China \(Hong Kong\)|203.88.163.0/24, 47.90.37.0/24, 47.90.38.0/24, 47.89.39.0/24, 10.26.5.0/24, 10.47.47.0/24, 10.175.251.0/24, 10.175.254.0/24, 10.175.255.0/24, 100.104.233.0/24, 47.52.25.202, 47.91.228.249, 47.52.166.98, 10.28.201.197, 10.28.185.63, and 10.28.201.14.|
|Singapore|47.88.133.0/24, 47.88.139.0/24, 100.107.4.0/24, 10.45.241.0/24, 10.45.244.0/24, 10.45.245.0/24, and 100.104.188.0/24.|
|Australia \(Sydney\)|47.91.49.0/24 and 47.91.50.0/24.|
|Malaysia \(Kuala Lumpur\)|47.254.212.0/24.|
|Indonesia \(Jakarta\)|149.129.228.0/24.|
|India \(Mumbai\)|149.129.164.0/24.|
|Japan \(Tokyo\)|47.91.9.0/24 and 47.91.13.0/24.|
|US \(Silicon Valley\)|98.11.174.0/24, 198.11.175.0/24, 10.172.115.0/24, 10.172.117.0/24, 10.172.119.0/24, 100.104.175.0/24, and 47.89.244.175.|
|US \(Virginia\)|100.104.233.0/24, 10.152.235.0/24, and 47.89.170.0/24.|
|Germany \(Frankfurt\)|47.91.82.0/24, 47.91.83.0/24, 47.91.84.0/24, 11.192.168.0/24, 11.192.169.0/24, 11.192.170.0/24, 100.104.5.0/24.|
|UK \(London\)|8.208.17.0/24.|
|UAE \(Dubai\)|47.91.102.0/24 and 47.91.103.0/24.|

## Data synchronization {#section_ik0_ow8_gev .section}

When you configure data synchronization, if a **User-Created Database with Public IP Address** or **User-Created Database Connected Over Express Connect, VPN Gateway, or Smart Access Gateway** is specified as the source database, modify the database security settings as follows:

-   If you want to allow DTS to access the source database, then you must modify the security settings of the source database. Add the CIDR blocks of the DTS servers in the regions where the source and destination databases are deployed to the security settings of the source database.

    For example, if the source database is deployed in the China \(Shenzhen\) region and the destination database is deployed in the China \(Hangzhou\) region, then you must add the CIDR blocks of the DTS servers in both regions to the security settings of the source database.

-   If you want to allow DTS to access the destination database, then you must modify the security settings of the destination database. Add the CIDR blocks of the DTS servers in the region where the destination database is deployed to the security settings of the destination database.

    For example, if the source database is deployed in the China \(Shenzhen\) region and the destination database is deployed in the China \(Hangzhou\) region, then you must add the CIDR blocks of the DTS servers in the China \(Hangzhou\) region to the security settings of the destination database.


|Region|CIDR block|
|:-----|:---------|
|China \(Hangzhou\)|10.152.194.0/24, 10.153.9.0/24, 10.153.137.0/24, 10.153.138.0/24, 100.104.52.0/24, 10.153.172.0/24, 10.145.97.0/24,and 10.151.12.84.|
|China \(Shanghai\)|10.145.97.0/24, 10.154.57.0/24, 10.154.58.0/24, 10.154.59.0/24, 100.104.205.0/24, and 100.104.175.0/24.|
|China \(Qingdao\)|10.145.96.0/24, 10.145.97.0/24, 100.104.72.0/24, and 11.219.10.0/24.|
|China \(Beijing\)|10.151.132.0/24, 10.151.140.0/24, 10.155.40.0/24, 100.104.183.0/24, 10.151.143.0/24, and 10.151.193.0/24.|
|China \(Zhangjiakou\)|47.92.22.0/24, 11.192.243.0/24, and 100.104.175.0/24.|
|China \(Hohhot\)|100.104.72.0/24, 39.104.29.0/24, and 11.193.183.0/24.|
|China \(Shenzhen\)|10.145.97.0/24, 100.104.18.0/24, 100.104.205.0/24, and 100.104.72.0/24.|
|China \(Hong Kong\)|10.89.28.0/24, 10.155.40.0/24, 10.151.132.0/24, and 100.104.233.0/24.|
|Singapore|10.151.132.0/24, 10.151.235.0/24, 10.155.40.0/24, and 100.104.188.0/24.|
|Australia \(Sydney\)|11.192.184.0/24 and 11.192.99.0/24.|
|Malaysia \(Kuala Lumpur\)|11.193.188.0/24.|
|Indonesia \(Jakarta\)|11.194.50.0/24 and 100.104.175.0/24.|
|India \(Mumbai\)|11.194.10.0/24 and 100.104.8.0/24.|
|Japan \(Tokyo\)|11.192.147.0/24, 11.192.149.0/24, and 100.104.112.0/24.|
|US \(Silicon Valley\)|10.153.9.0/24 and 10.151.187.0/24.|
|US \(Virginia\)|10.152.235.0/24 and 47.89.170.0/24.|
|Germany \(Frankfurt\)|11.192.168.0/24, 11.192.169.0/24, 11.192.170.0/24, and 100.104.5.0/24.|
|UK \(London\)|11.199.93.0/24, 100.104.133.64/26, 10.151.12.0/24, 120.55.129.0/24, 10.143.33.0/24, and 112.124.140.0/24.|
|UAE \(Dubai\)|47.91.102.0/24 and 47.91.103.0/24.|


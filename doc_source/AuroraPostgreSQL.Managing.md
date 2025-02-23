# Managing Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Managing"></a>

The following sections discuss managing performance and scaling for an Amazon Aurora PostgreSQL DB cluster\.

**Topics**
+ [Scaling Aurora PostgreSQL DB instances](#AuroraPostgreSQL.Managing.Performance.InstanceScaling)
+ [Maximum connections to an Aurora PostgreSQL DB instance](#AuroraPostgreSQL.Managing.MaxConnections)
+ [Temporary storage limits for Aurora PostgreSQL](#AuroraPostgreSQL.Managing.TempStorage)
+ [Testing Amazon Aurora PostgreSQL using fault injection queries](AuroraPostgreSQL.Managing.FaultInjectionQueries.md)
+ [Displaying volume status for an Aurora PostgreSQL DB cluster](AuroraPostgreSQL.Managing.VolumeStatus.md)

## Scaling Aurora PostgreSQL DB instances<a name="AuroraPostgreSQL.Managing.Performance.InstanceScaling"></a>

You can scale Aurora PostgreSQL DB instances in two ways, instance scaling and read scaling\. For more information about read scaling, see [Read scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.ReadScaling)\.

You can scale your Aurora PostgreSQL DB cluster by modifying the DB instance class for each DB instance in the DB cluster\. Aurora PostgreSQL supports several DB instance classes optimized for Aurora\. Don't use db\.t2 or db\.t3 instance classes for larger Aurora clusters of size greater than 40 terabytes \(TB\)\. For detailed specifications of the DB instance classes supported by Aurora PostgreSQL, see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\.

## Maximum connections to an Aurora PostgreSQL DB instance<a name="AuroraPostgreSQL.Managing.MaxConnections"></a>

The maximum number of connections allowed to an Aurora PostgreSQL DB instance is determined by the `max_connections` parameter in the instance\-level parameter group for the DB instance\. By default, this value is set to the following equation:

`LEAST({DBInstanceClassMemory/9531392},5000)`\.

Setting the `max_connections` parameter to this equation makes sure that the number of allowed connection scales well with the size of the instance\. For example, suppose your DB instance class is db\.r4\.large, which has 15\.25 gibibytes \(GiB\) of memory\. Then the maximum connections allowed is 1600, as shown in the following equation:

```
LEAST((15.25*1000000000)/9531392,5000) = 1600
```

The following table lists the resulting default value of `max_connections` for each DB instance class available to Aurora PostgreSQL\. You can increase the maximum number of connections to your Aurora PostgreSQL DB instance by scaling the instance up to a DB instance class with more memory, or by setting a larger value for the `max_connections` parameter, up to 262,143\.


| Instance class | max\_connections default value | 
| --- | --- | 
| db\.r4\.large | 1600 | 
| db\.r4\.xlarge | 3200 | 
| db\.r4\.2xlarge | 5000 | 
| db\.r4\.4xlarge | 5000 | 
| db\.r4\.8xlarge | 5000 | 
| db\.r4\.16xlarge | 5000 | 
| db\.r5\.large | 1600 | 
| db\.r5\.xlarge | 3300 | 
| db\.r5\.2xlarge | 5000 | 
| db\.r5\.4xlarge | 5000 | 
| db\.r5\.12xlarge | 5000 | 
| db\.r5\.24xlarge | 5000 | 
| db\.t3\.medium | 420 | 

For the list of DB instance classes supported by Aurora PostgreSQL, see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\. For the amount of memory for each DB instance class, see [Hardware specifications for DB instance classes for Aurora](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\.

## Temporary storage limits for Aurora PostgreSQL<a name="AuroraPostgreSQL.Managing.TempStorage"></a>

Aurora PostgreSQL stores tables and indexes in the Aurora storage subsystem\. Aurora PostgreSQL uses separate temporary storage for non\-persistent temporary files\. This includes files that are used for such purposes as sorting large datasets during query processing or for index build operations\. For more about storage, see [Amazon Aurora storage and reliability](Aurora.Overview.StorageReliability.md)\. 

The following table shows the maximum amount of temporary storage available for each Aurora PostgreSQL DB instance class\.


| DB instance class | Maximum temporary storage available \(GiB\) | 
| --- | --- | 
| db\.r5\.24xlarge | 1500  | 
| db\.r5\.12xlarge | 748 | 
| db\.r5\.4xlarge | 249 | 
| db\.r5\.2xlarge | 124 | 
| db\.r5\.xlarge | 62 | 
| db\.r5\.large | 31 | 
| db\.r4\.16xlarge | 960 | 
| db\.r4\.8xlarge | 480 | 
| db\.r4\.4xlarge | 240 | 
| db\.r4\.2xlarge | 120 | 
| db\.r4\.xlarge | 60 | 
| db\.r4\.large | 30 | 
| db\.t3\.medium | 7\.5 | 

You can monitor the temporary storage available for a DB instance with the `FreeLocalStorage` CloudWatch metric, described in [Amazon Aurora metrics](Aurora.Monitoring.md#Aurora.AuroraMySQL.Monitoring.Metrics)\. 

For some workloads, you can reduce the amount of temporary storage by allocating more memory to the processes that are performing the operation\. To increase the memory available to an operation, increasing the values of the [work\_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) or [maintenance\_work\_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) PostgreSQL parameters\.
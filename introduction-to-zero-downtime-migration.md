## 1Introduction to Zero Downtime Migration {#GUID-A4EC1775-307C-47A6-89FB-E4C3F1FBC4F5}

Learn about how Zero Downtime Migration works, and its requirements and supported configurations.

### About Zero Downtime Migration {#GUID-FF4CA22F-CC83-4118-AF26-6E7BE224717F}

Zero Downtime Migration provides a robust, flexible, and resumable migration process. Zero Downtime Migration integrates Oracle Maximum Availability Architecture (MAA) and supports Oracle Database 11g Release 2 (11.2.0.4) and later database releases.

You can perform and manage a database migration of an individual database or perform database migrations at a fleet level. Leveraging technologies such as Oracle Data Guard, Oracle Recovery Manager (RMAN), Oracle GoldenGate, and Oracle Data Pump, you can migrate databases online or offline. 

Using Zero Downtime Migration, you can migrate Oracle databases from a variety of on premises and cloud sources to Oracle Database Cloud managed, co-managed and user-managed databases, including Autonomous Database, or any Exadata Database Machine in the cloud or on premises.

The Zero Downtime Migration software is a service with a command line interface that you install and run on a host that you provision. The server where the Zero Downtime Migration software is installed is called the Zero Downtime Migration service host. You can run one or more database migration jobs from the Zero Downtime Migration service host simultaneously.

Zero Downtime Migration Capabilities

* **Audit capability** \- All custom user actions are audited including actions performed by the migration job. 
* **Work flow customization** \- You can customize the migration work flow (marked by operational phases) with your own scripts, which can be run before or after any phase in the work flow. 
* **Job subsystem** \- You can perform and manage database migrations at a fleet scale. 
* **Job scheduler** \- You can schedule your migration job to run at a future point in time. 
* **Pause and resume functionality** \- You can schedule a job pause, and then resume your migration job if needed, which is useful to conform to a maintenance window, for example. 
* **Suspend and resume functionality** \- You can suspend a running job and resume it later, which is useful to circumvent any high workload in the database caused by migration activity, for example. 
* **Job termination** \- You can terminate a running migration job, rather than waiting for it to complete. 
* **Job rerun ability** \- Your migration job can be re-run (resumed) from a point of failure. 
* **Job pre-check** \- You can run pre-checks for migration tasks to prevent errors during database migration. 
* **Compliance** \- Zero Downtime Migration is compliant with Oracle Maximum Availability Architecture best practices and supports Oracle Database 11g Release 2 (11.2.0.4.0) and later. 



Zero Downtime Migration Concepts

* **Online** migration methods incur zero or minimal downtime (typically less than 15 minutes) and can leverage either physical or logical migration methods. 

* **Offline** migration methods will incur downtime on the source database as part of the migration process. It can leverage either physical or logical migration methods. 

* **Physical** migration methods: 

* Use Oracle Data Guard and RMAN to perform migrations

* Allow you to convert a non-multitenant (non-CDB) source database to a multitenant (CDB) target database

* **Logical** migration methods: 

* Use Oracle Data Pump and, for online migrations, Oracle GoldenGate Microservices

* Allow you to perform cross-platform migration where the source Oracle Database is running on Oracle Solaris or IBM AIX operating system, and the target is an Oracle Autonomous Database or Co-managed Oracle Database on Oracle Linux.

* Include integration with the Cloud Premigration Advisor Tool (CPAT), which a) warns you about any features used by your database that aren't supported in the target cloud environment, and b) makes suggestions for remedial changes and/or parameters to use for the Data Pump export and import operations




**Table: ZDM migration methods and their migration technologies and data transfer mechanisms**

Migration Method | High Availability | Migration Technology | Data Transfer Mechanisms Supported  
---|---|---|---  
**Physical**<br>Block copy |  **Offline**<br>Backup-Restore1 |  Oracle RMAN | * Oracle Cloud Object Store<br>Oracle ZDLRA<br>Shared Storage - NFS<br>Direct Restore from service
``|**Online**<br>Backup-Restore1<br>Physical Standby-Switchover |  Oracle RMAN<br>Oracle Data Guard  
**Logical**<br>SQL Unload-Reload |  **Offline**<br>Export-Import |  Oracle Data Pump | * Oracle Cloud Object Store * oci-cli<br>Shared Storage - NFS<br>Secure Copy<br>Database Link<br>Amazon S32
``|**Online**<br>Export-Import<br>Extract-Replicat |  Oracle Data Pump<br>Oracle GoldenGate  1Backup is optional; pre-existing backup can be used 

2Migration from AWS only 

The migration methods are described in the following topics.

* [Physical Migrations with Zero Downtime Migration](introduction-to-zero-downtime-migration.md#GUID-9959A6A5-0A1F-4011-8403-387C32F1736C)
* [Logical Migrations with Zero Downtime Migration](introduction-to-zero-downtime-migration.md#GUID-1F0FB4F4-2F2B-4362-8C96-8C37C884CE76)



### Physical Migrations with Zero Downtime Migration {#GUID-9959A6A5-0A1F-4011-8403-387C32F1736C}

Physical migrations with Zero Downtime Migration use the Recovery Manager (RMAN) and Oracle Data Guard to perform the data transfer from the source to the target database, and can handle the role switch of the target database to primary database for application connections.

#### Physical Online Migration {#GUID-7148B1FC-3E50-42ED-8CC4-1795B42C940B}

Zero Downtime Migration harnesses Oracle Data Guard to perform an online physical migration.

A Zero Downtime Migration online physical migration does the following:

* Backs up the source database to the specified data transfer medium
* Instantiates a standby database from this backup to the target environment
* Configures Data Guard with Maximum Performance protection mode and asynchronous (ASYNC) redo transport mode
* Synchronizes the source and target databases
* Switches over to the target database as the new primary database with minimum downtime



Upon switchover, the target database becomes the primary database, and the source database becomes the standby.

If there is SQL*Net connectivity from the new primary and the new standby after the switchover, the configuration continues to synchronize data (ship redo) from the new primary to the new standby source database. This configuration makes it possible to perform a rollback with minimal downtime, if you need to switch the primary back to the original source database.

However, if there is no SQL*Net connectivity from the new primary and the new standby after the switchover, there is no data synchronization (ship redo) from the new primary to the new standby on the source database. With this configuration you cannot fall back to the original source database.

Note that any fallback operation must be done manually; Zero Downtime Migration does not handle reverse role switches to fall back to the original source database as the primary database.

> **note:** 

* When migrating to the cloud, TDE is mandatory for Oracle Database 12.2 and later releases. 
* The source must have TDE configured. The tablespaces on the source do not need to be encrypted. The tablespaces on the target will be encrypted when using backup/restore or restore from service. 
* When the target is on-premises Oracle Exadata Database Machine (`NON_CLOUD`), you can choose to not use TDE and to skip TDE at the source by specifying `ZDM_TDE_MANDATORY=FALSE`. In this case, the target will not use TDE. 
* If the source has TDE enabled, then the target will also use TDE. 



#### Physical Offline Migration {#GUID-1B4A63F4-C4A9-47FC-B508-470662620731}

Zero Downtime Migration can perform a backup and restore operation to achieve an offline physical migration.

A Zero Downtime Migration offline physical migration does the following:

* Backs up the source database to the specified data transfer medium
* Instantiates a new database from this backup to the target environment



The offline migration method is similar to cloning a database. The target database has no relationship to the source, so there is no data synchronization or fallback capability. No SQL*Net connectivity is needed between the source and target database servers.

Note that for physical migrations, the offline methodology is the only one that supports Oracle Database Standard Edition 2.

#### Supported Physical Migration Paths {#GUID-DBEDBEE1-6951-42E8-96A4-AAE6AE930F48}

Zero Downtime Migration supports the following physical migration paths.

* On-premises Oracle Database to Oracle Cloud Infrastructure (either virtual machine or bare metal)
* On-premises Oracle Database to Oracle Exadata Database Service on Dedicated Infrastructure
* On-premises Oracle Database to Oracle Exadata Database Service on Cloud@Customer
* On-premises Oracle Database to On-Premises Exadata Database Machine
* On-premises Oracle Database to Oracle Exadata Database Service on Dedicated Infrastructure (ExaDB-D) on Oracle Database@Azure 

> **note:** Currently, physical online migration with direct data transfer is the supported workflow. 

For more information, see [Migrate Oracle Databases to ExaDB-D on Oracle Database@Azure with Zero Downtime Migration (ZDM)](https://www.youtube.com/watch?v=1fDDGlb7y7Y) and [Oracle Zero Downtime Migration – Physical Online Migration to ExaDB-D on Oracle Database@Azure](https://www.oracle.com/a/otn/docs/database/zdm-physical-migration-to-oracle-at-azure.pdf). 

* Oracle Cloud Infrastructure Classic Database to Oracle Cloud Infrastructure (either virtual machine or bare metal)
* “Oracle Cloud Infrastructure Database to a database in the same or another Oracle Cloud Infrastructure region. 

For example, you can move a database from the `phoenix` commercial OCI region to the `frankfurt` or `ashburn` region. 




#### Data Transfer Media Supported for Physical Migrations {#GUID-18947500-FBE5-48D9-97B0-F29357C99365}

The Zero Downtime Migration physical migration process involves creating a backup of the source database and restoring it to the target database. Zero Downtime Migration supports the following backup media, depending on your target environment.

* Oracle Cloud Infrastructure Object Storage
* Zero Data Loss Recovery Appliance
* Network File System (NFS) 

> **note:** Oracle ACFS NAS - MAX is fully supported for both physical and logical migration scenarios where NFS is selected as the transfer medium of choice. 




#### Direct Data Transfer Support {#GUID-00701E56-0B91-42F3-AEA6-84A386B53B05}

Zero Downtime Migration supports direct data transfer during a physical migration to avoid backing up the source database to an intermediate store such as Object Storage or NFS.

Backing up a large database to intermediate storage adds additional overhead because of the additional hop that the data has to be moved to. This intermediate step can be costly in migrations involving very large databases. Recovery Manager (RMAN) allows Zero Downtime Migration to support active database duplication and restore from service.

Active database duplication does not require backups of the source database. It duplicates the live source database to the destination host by copying the database files over the network to the auxiliary instance. RMAN can copy the required files as image copies or backup sets.

With restore from service, RMAN restores database files, over the network, by using the `FROM SERVICE` clause of the `RESTORE` command. During the restore operation RMAN creates backup sets of the files that need to be restored and then transfers these backup sets to the target database over the network. 

Restore from service also allows you to transfer data from a Oracle Data Guard standby database, reducing the load on your primary database system.

#### Supported Database Architectures for Physical Migration {#GUID-FE1EE025-623F-421B-A22E-E6AFB8103A50}

Zero Downtime Migration supports the following database architecture implementations.

* Oracle Database Single-Instance, which can be migrated to a single-instance or Oracle RAC database target
* Oracle RAC One Node, which can be migrated to an Oracle RAC database target
* Oracle RAC, which can be migrated to an Oracle RAC database target



#### Oracle Database Edition Support {#GUID-15F17C80-515F-4EF6-A684-D38845155CAB}

Zero Downtime Migration supports migrations of Standard and Enterprise Edition Oracle databases.

While Standard Edition databases can use Zero Downtime Migration, they must use the offline migration method which is based on a backup and restore methodology that does not use Data Guard.

Enterprise Edition can be migrated using the offline or online method.

#### Target Placeholder Database Environment {#GUID-42B4216B-0F4B-4CC3-AB30-10C43346DACC}

Zero Downtime Migration requires that you configure a placeholder database target environment before beginning the migration process. Zero Downtime Migration uses the provisioned target as a template and recreates the target during the course of migration.

You should configure the target database with the required and desired options of your native environment, because Zero Downtime Migration does not preserve this automatically during the migration.

During the migration process, the Zero Downtime Migration service host restores the source database to this placeholder database target environment by dropping the placeholder database and recreating a database in the target environment with the same `db_name` as that of source database. 

Any database parameters for the target database, including SGA parameters, are maintained during the migration, and the migrated database runs with this same configuration.

Once the migration is complete, the target database is accessible using Oracle Database Cloud Service console, and you can manage the database with SRVCTL commands. You can make any modifications to database parameters after the migration.

### Logical Migrations with Zero Downtime Migration {#GUID-1F0FB4F4-2F2B-4362-8C96-8C37C884CE76}

Online and offline logical migrations using Zero Downtime Migration are described in the following topics:

#### Logical Online Migration {#GUID-2C3B478F-F3CD-49AE-AD0E-9DE3324D47F0}

Zero Downtime Migration harnesses Oracle GoldenGate and Oracle Data Pump to perform an online logical migration.

During a logical online migration, the source database remains online for client connections while data is moved to the target database, using a combination of Oracle Data Pump and Oracle GoldenGate replication.

Note that for migrations to OCI targets you can use the [Oracle GoldenGate license](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/96175416) from Oracle Cloud Marketplace, which is the Oracle-certified way of deploying the GoldenGate hub in Oracle Cloud Infrastructure. 

The “Oracle GoldenGate for Oracle – Database Migrations” Oracle Cloud Marketplace offering now contains a downloadable docker image for Zero Downtime Migration to use with on-premises Exadata Cloud@Customer systems. You can review the latest marketplace VM at <https://cloudmarketplace.oracle.com/marketplace/en_US/listing/96175416>, and the documentation for this functionality can be found at [Migrating to Exadata Cloud@Customer Using Oracle Zero Downtime Migration](https://docs.oracle.com/en/middleware/goldengate/core/21.1/oggmp/provisioning-oracle-goldengate-microservices-oci-marketplace.md#GUID-A52F5E0D-1AB8-4E89-8CE3-BBE9132C005C). 

The source database can be either a pluggable database (PDB) or a non-multitenant database.

Logical online migration involves two steps:

* Instantiation of target database. 

Oracle Data Pump extracts data from the source database and loads it into the target database. 

* Real-time data replication between source and target databases. 

Zero Downtime Migration completes the migration once both databases are synchronized.

Optionally, you can configure the migration job to pause after replication is set up, and Oracle GoldenGate can continue to replicate data between the source and target databases in real-time until you choose to switch applications over to the target database.




#### Logical Offline Migration {#GUID-3B2D26B0-FC99-4430-A048-B03512696F8A}

Zero Downtime Migration can perform an offline logical migration using Oracle Data Pump to extract the data from the source database and load it into a target database.

Offline logical migration means that the source database is not available for clients while data is moved to the target database. When using the offline migration method, you must stop updates to the source database before you start a migration. When the migration is complete, the target database and source database do not require any direct SQL*Net connectivity between them.

#### Supported Logical Migration Targets {#GUID-431D24EC-E1C3-43A8-9C9D-C0137A2D3A87}

Zero Downtime Migration supports logical database migration to the following target databases.

* Oracle Autonomous Database on Shared Exadata Infrastructure (Data Warehouse or Transaction Processing)
* Oracle Autonomous Database on Dedicated Exadata Infrastructure (Data Warehouse or Transaction Processing)
* Oracle Autonomous Database on Exadata Cloud@Customer
* Oracle Co-managed Database Systems: 
* Oracle Base Database Service (Virtual Machine, Bare Metal)
* Oracle Exadata Database Service on Dedicated Infrastructure
* Oracle Exadata Database Service on Cloud@Customer
* On premises Exadata Database Machine
* Autonomous Database on Dedicated Infrastructure and Autonomous Database on Cloud@Customer with fractional OCPU allocation, where a fraction of OCPU is allocated per database service, instead of integer OCPU.

You can specify any predefined fractional service alias available; however, for Autonomous Transaction Processing workloads TP* services are preferred over LOW* services because LOW* is meant for low priority batch jobs.

* `TP_TLS`, `TP`, `LOW_TLS` or `LOW`(for Autonomous Transaction Processing workloads) 
* `LOW_TLS` or `LOW` (for Autonomous Data Warehouse workloads) 



#### Initial Load Methods Supported for Logical Migrations {#GUID-0F8B8312-1F57-46A6-8F15-6144286C8326}

Some Zero Downtime Migration logical migration work flows involve placing Oracle Data Pump dump files on storage media for transfer to the target database. Some migration work flows transfer the initial load without using intermediate storage.

**Oracle Cloud Object Store**

Object Storage is supported as a Data Pump dump file storage medium for logical migrations to all target Autonomous Database or co-managed databases.

To use Object Storage with OCI CLI on co-managed targets, see [Command Line Interface (CLI)](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/cliconcepts.htm). 

**Database Link**

Autonomous Database Shared Infrastructure and Dedicated Infrastructure (Data Warehouse or Transaction Processing) and co-managed database targets support the use of a database link for data transport. 

> **note:** Migrating your database using database link for database sized more than few gigabytes (GB) is not recommended. 

**Network File System (NFS)**

For migrations to co-managed target databases, NFS is supported as a data transfer medium. Oracle ACFS 21c NAS - MAX is fully supported for both physical migration scenarios where NFS is selected as the transfer medium of choice. See [Migrating to Co-Managed Database Server with NFS Data Transfer Medium](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C62F892E-A45E-4CE4-90C7-3955BBD355F5) and [DUMPTRANSFERDETAILS_PUBLICREAD](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-53C3274C-163F-4208-B862-C8D80281D2C5) for more information. 

**Direct Copy**

For migrations to co-managed target databases, Data Pump dumps can be securely transferred directly from the source database to the target using secure copy (SCP) or RSYNC.

**Amazon Simple Storage Service (Amazon S3) Bucket**

An Amazon S3 bucket is supported as a Data Pump dump file storage medium for migrations of Oracle Database instances from Amazon Web Services RDS Oracle Database.

#### What Is Migrated During Initial Load {#GUID-A62B0689-9902-4027-8CEF-BC6410286225}

The initial load phases of a migration job moves the contents of all schemas from the source database to schemas of the same name in the target database. You can elect to exclude specific objects and rename objects when you create a migration.

There are some limitations on objects, schemas, roles, and users that can be migrated, because they cannot be exported by Data Pump, such as system-generated schemas that contain Oracle-managed data and metadata.

**Objects Excluded by Default in Oracle 12c and Later Releases**

By default, objects owned by Oracle-maintained users (`ORACLE_MAINTAINED = Y`), and the `ggadmin` and `c##ggadmin` users, are excluded from migration. In addition, Data Pump ignores schemas listed in the `KU_NOEXP_VIEW` in addition to Oracle-maintained users. 

Oracle Database includes the column `ORACLE_MAINTAINED` in several dictionary views to indicate which objects, schemas, roles, and users are maintained by Oracle. You can query these views to discover object types which are Oracle-maintained, and therefore excluded from migration. See *Oracle Database Reference* at <https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/database-reference.pdf> to find views that contain `ORACLE_MAINTAINED`. 

**Objects Excluded by Default in Oracle 11gR2**

Zero Downtime Migration excludes schemas listed in the `KU_NOEXP_VIEW` view, and the following Oracle-maintained schemas from Oracle 11gR2 migrations. 

"ORACLE_OCM","OJVMSYS","XS$NULL","GSMCATUSER","MDDATA","DIP", "APEX_PUBLIC_USER","SPATIAL_CSW_ADMIN_USR", "OWBSYS", "SPATIAL_WFS_ADMIN_USR","GSMUSER","AUDSYS","FLOWS_FILES", "MDSYS","ORDSYS","WMSYS","EXFSYS","APEX_040200","APPQOSSYS", "GSMADMIN_INTERNAL","ORDDATA","CTXSYS","ANONYMOUS","XDB", "ORDPLUGINS","SI_INFORMTN_SCHEMA","OLAPSYS","DBSNMP","SYSKM", "SYSBACKUP","OUTLN","SYSDG","SYS","SYSTEM","APEX_030200", "GGADMIN", "LBACSYS", "MGMT_VIEW", "OWBSYS_AUDIT", "DVSYS", "TSMSYS", "MGDSYS"

#### Data Replication {#GUID-BF631FCE-B190-4F90-B2CB-819B4DE0B786}

Replication migrates all data and metadata operations in transactions committed after the initial load until you resume the migration job after the Monitor Replication Lag phase.

During the migration job it is recommended that your database avoid Data Definition Language (DDL) operations to provide the most optimal environment for fast database replication. When DDL is replicated, Oracle GoldenGate Replicat serializes data to ensure that there are no locking issues between DML and DDL on the same objects.

The `DBOPTIONS DEFERREFCONST` option is set by default for Oracle GoldenGate Replicat Non-integrated Parallel Replicat mode of replication. `DEFERREFCONST` allows to migrate constraints and not disable the constraints on the target tables or setting them to DEFERRED. When used, `DEFERREFCONST` defers both `DEFERABLE` and `NOT DEFERABLE` constraints. `DEFERREFCONST` applies to every transaction that is processed by Replicat. 

By default, Zero Downtime Migration excludes all DDL from GoldenGate replication. However, Zero Downtime Migration lets you override this behavior with a configuration parameter.

The following objects are not supported:

* Changes to external tables
* Oracle GoldenGate Unsupported Types (see [Understanding What’s Supported](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oracle-db/1-understanding-whats-supported.md#GUID-110CD372-2F7E-4262-B8D2-DC0A80422806)) 



### Hybrid Migrations with Zero Downtime Migration {#GUID-DC764CAC-CDA1-49A0-9074-676A81FE119C}

Hybrid migrations is a new migration method alongside physical and logical migration. Currently, `XTTS_OFFLINE` with data transfer medium as NFS is supported. This hybrid implementation combines RMAN for incremental backup and restore of tablespaces and Datapump for migration of metadata. Therefore, this migration combines elements of both physical and logical migration. 

### Zero Downtime Migration Requirements and Considerations {#GUID-74C94FF2-AD24-4DF7-8557-A3169196C793}

#### Supported Platforms {#GUID-B9A171CF-1F0D-4810-87DD-5AA555574743}

Zero Downtime Migration supports the following platforms for the service host and the migration source and target database servers.

**Zero Downtime Migration Service Host - Supported Platforms**

The Zero Downtime Migration service host can be configured on Oracle Linux 7 (Linux-x86-64), Oracle Linux 8 (64-bit) on x86-64, Red Hat Enterprise Linux 8 (64-bit) on x86-64, and Red Hat Enterprise Linux 9 (64-bit) on x86-64.

You can deploy the Zero Downtime Migration service on a standalone server on-premises or on a standalone Linux server (compute instance) in the Oracle Cloud. Oracle Linux is the supported platform for the Zero Downtime Migration service host.

Note that the Zero Downtime Migration service host can be shared with other applications for other purposes.

**Supported Source Environments**

* Oracle Cloud Infrastructure co-managed databases or on-premises environments
* Amazon Web Services RDS Oracle Database

* Linux-x86-64 (all migration modes)

* IBM AIX (using logical migration)

* Oracle Solaris on SPARC (64-Bit) (using logical migration)




**Supported Target Environments**

* Oracle Cloud Infrastructure co-managed databases: Oracle Exadata Database Service on Dedicated Infrastructure, Oracle Exadata Database Service on Cloud@Customer, Oracle Base Database Service (Virtual Machine and Bare Metal).

The target co-managed database can be either a pluggable database or a non-multitenant database.

* Linux-x86-64 is the supported operating system for target database servers.

* As a target environment only, Autonomous Database is supported platform for logical migrations. You can choose a Dedicated Infrastructure (Data Warehouse or Transaction Processing) or Shared (Data Warehouse or Transaction Processing).

An Autonomous Database is a pluggable database in an Autonomous Container Database (ACD) deployed on an Autonomous Exadata Infrastructure (AEI) rack.

You can also migrate to Oracle Autonomous Database on Dedicated Exadata Infrastructure and Oracle Autonomous Database on Exadata Cloud@Customer with **fractional OCPU allocation** , where a fraction of OCPU is allocated per database service, instead of integer OCPU. 

You can specify any predefined fractional service alias available; however, for Autonomous Transaction Processing workloads TP* services are preferred over LOW* services because LOW* is meant for low priority batch jobs.

* `TP_TLS`, `TP`, `LOW_TLS`, or `LOW` (for Autonomous Transaction Processing workloads) 
* `LOW_TLS` or `LOW` (for Autonomous Data Warehouse workloads) 
* On-premises Oracle Exadata Database Machine

* Migrating using logical migration methods to Oracle Exadata Database Machine or Oracle Database Appliance is supported.



#### Supported Database Versions for Migration {#GUID-13AC85E5-53FF-4D2A-A075-DDB975090CE3}

Zero Downtime Migration supports most Oracle Database versions available on Oracle Cloud Infrastructure, Oracle Exadata Database Service on Cloud@Customer, and Oracle Exadata Database Service on Dedicated Infrastructure.

The following Oracle Database versions can be migrated using Zero Downtime Migration.

* Oracle Database 11g Release 2 (11.2.0.4)
* Oracle Database 12c Release 1 (12.1.0.2)
* Oracle Database 12c Release 2 (12.2.0.1)
* Oracle Database 18c
* Oracle Database 19c
* Oracle Database 21c
* Oracle Database 23ai (Only supported for target databases)
* All subsequent Oracle Database releases



Some restrictions apply to physical migration work flows. See [Preparing the Source and Target Databases](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-E8A2DD5F-B447-465F-BE2E-55E79FFD092D). 

#### Zero Downtime Migration Database Server Access {#GUID-0F0C042A-3561-4BAF-8331-0BB5B194DA9E}

The Zero Downtime Migration service host needs to access the source and target database servers during a database migration.

To perform the migration, the Zero Downtime Migration service host requires either root user or SSH key-based access to one of the source database servers, and the Zero Downtime Migration service host requires SSH key-based access to one of the target database servers. If you are migrating an Oracle RAC database, providing access to one of the Oracle RAC nodes is adequate. The Zero Downtime Migration service host copies the software needed for migration to the source and target servers and cleans it up at the end of the operation.

An SSH private key is required to establish SSH connections. This generated key must not use a passphrase. You can create and add a new SSH key to your existing deployment using the Oracle Cloud Service Console.

#### Zero Downtime Migration Operational Phases {#GUID-6CC55C41-F387-46F7-93B4-03DD2D64AD80}

The Zero Downtime Migration service defines the migration process in units of operational phases.

Zero Downtime Migration auto computes the migration work flow using defined operational phases based on configured input parameters, such as the target platform, backup medium, and so on. You can customize the work flow by inserting custom plug-ins on each of the operational phases. The Zero Downtime Migration service lets you pause and resume the migration work flow at any chosen operational phase.

Migration work flow-associated phases for a given operation can be listed. Phases that are performed on the source database server are listed with a _SRC suffix, and the phases associated with the target database server are listed with a _TGT suffix.

> **note:** See Also:[Zero Downtime Migration Process Phases](zero-downtime-migration-process-phases.md#GUID-DA18E66B-2943-4C67-91B7-0CF3E0B0DC6B)

#### Zero Downtime Migration Security Provisions {#GUID-64274CA5-839C-40AD-BE3F-D56B41AB2C96}

Zero Downtime Migration permissions and ownership of files and directories, and handling of configurations for security features, are equivalent to those of Oracle Database.

Zero Downtime Migration installs in a location, named `ZDM_HOME`, that is structured similarly to the Oracle home directory, `ORACLE_HOME`, for Oracle Database. The permissions and ownership of files and directories in the `ZDM_HOME` follow the same conventions as that of a database `ORACLE_HOME`. 

Zero Downtime Migration also creates a base directory structure for storing Zero Downtime Migration configuration files, logs, and other artifacts, named `ZDM_BASE`, that is similar to an Oracle base directory, `ORACLE_BASE`, that is associated with an Oracle home. The structure, owners, and permissions of directories and files in `ZDM_BASE` are similar to that of an `ORACLE_BASE`. 

`ZDM_BASE` and `ORACLE_BASE` do not allow access by group or others. 

You do not need to do any additional steps to ensure security the of the Zero Downtime Migration configuration because the Zero Downtime Migration configuration is designed to be secure out of the box.

Zero Downtime Migration is configured to accept JMX connections only from the local host, and to listen on the loopback address for HTTP connections. Zero Downtime Migration operations can only be performed by the operating system user that installed the product.

For physical migrations, SSH connectivity from the Zero Downtime Migration service host to the source database server and the target database server is required. You must provide the SSH key file location as an input for a migration job, and the existence of this file is expected for the duration of the migration job. You must manage the security of the directories and files where these key files are located.

You can modify the communication ports when there is a port conflict with another application. Note that access to these ports are configured only from within the Zero Downtime Migration host. You can change the RMI and HTTP port properties in the file `$ZDM_BASE/crsdata/*`zdm_service_host`*/rhp/conf/standalone_config.properties`. 

The properties are:

* RMI port - `oracle.jwc.rmi.port=8895`
* HTTP port - `oracle.jwc.http.port=8896`



Restart the Zero Downtime Migration service after changing the properties.

When Zero Downtime Migration operations require passwords, prompts are given for password entry by default. Zero Downtime Migration can also operate non-interactively by entering wallet information in the migration response file settings.

Passwords are encrypted and stored in the Zero Downtime Migration database. Provided passwords are not expected to change for the duration of a migration job.

From an operation perspective, Zero Downtime Migration follows the guidelines in *Oracle Database Security Guide* for handling source and target database configurations for migration, such as Oracle Wallets, Transparent Data Encryption, and so on. 
## 5Preparing for a Logical Database Migration {#GUID-53169B81-1574-4A69-B5B7-3AC1F4F48E8A}

The following topics describe how to complete the Zero Downtime Migration prerequisites before running a logical database migration job.

### Best Practices for a Logical Database Migration {#GUID-57A06FF2-52C8-4F8D-BD2E-4D27D897D24D}

The following best practices are recommended for a logical database migration.

Source database workload and activity

* During the migration job it is recommended that your database avoid Data Definition Language (DDL) operations to provide the most optimal environment for fast database replication. When DDL is replicated, Oracle GoldenGate Replicat serializes data to ensure that there are no locking issues between DML and DDL on the same objects. See [Data Replication](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-BF631FCE-B190-4F90-B2CB-819B4DE0B786) for details. 
* Patching and Upgrade operations are not recommended during migration as they can result in lot of DDL operations 
* During the migration period, to provide the most optimal environment for fast database replication, avoid large batch DML operations. Running large batch operations, like a single transaction that affects multimillion of rows, can slow down replication rates. 
* You can specify ZDM parameters `[GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_DURATION](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-0393A20A-C0DE-41E1-9B97-2751B9BC9981)` and `[GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_CHECKINTERVAL](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-F5ABF18A-B5E9-44A9-A831-AB643357268E)` to generate GoldenGate Extract warnings for long running transactions. 
* ZDM sets parameter `SPLIT_TRANS_RECS` for GoldenGate Replicat. 



Source database configuration

* Apply recommended/required Oracle GoldenGate and RDBMS patches. See [Oracle GoldenGate -- Oracle RDBMS Server Recommended Patches (Doc ID 1557031.1)](https://support.oracle.com/rs?type=doc&id=1557031.1) and [Latest GoldenGate/Database (OGG/RDBMS) Patch recommendations (Doc ID 2193391.1)](https://support.oracle.com/rs?type=doc&id=2193391.1) for details. 
* Enable `ARCHIVELOG` mode 

* Enable `FORCE LOGGING`

* Enable database minimal supplemental logging

* Set `STREAMS_POOL_SIZE` to at least 2GB 

See [Source Database Prerequisites for Logical Migration](https://docs.oracle.com/en/database/oracle/zero-downtime-migration/21.4/zdmug/preparing-logical-database-migration1.md#GUID-004B4498-49B9-45A8-BD1B-5449857139A0) for details. 




Source tables uniqueness

* Oracle GoldenGate requires a unique row identifier on the source and target tables to locate the correct target rows for replicated updates and deletes.

This is usually considered with primary key or unique key indexes. If there are tables identified that do not have any such keys, GoldenGate must construct a pseudo key that contains all of the allowable columns in the table, excluding virtual columns, UDTs, function-based columns, extended (32K) VARCHAR2/NVARCHAR2 columns, and any columns that are explicitly excluded from the Oracle GoldenGate configuration by an Oracle GoldenGate user.

However, constructing a key from all of the columns will significantly impede the performance of Oracle GoldenGate.

If the source database is version Oracle Database 12g (12.2) or later, use the data dictionary view DBA_GOLDENGATE_NOT_UNIQUE to identify all of the tables that do not have a primary key or non-null unique columns. 

* See [Ensuring Row Uniqueness in Source and Target Tables](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oracle-db/preparing-database-oracle-goldengate.md) for details. 



MAX_STRING_SIZE

* If source database is configured with MAX_STRING_SIZE=STANDARD, then set MAX_STRING_SIZE=STANDARD for the target database.
* See [MAX_STRING_SIZE](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/MAX_STRING_SIZE.md) for details. 



Source database server CPU and memory requirements

If there are enough FREE CPU resources on the source system, set ZDM configuration parameter

GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE = HIGH. Else, set ZDM configuration parameter GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE = LOW-RES

GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE = HIGH

Resource usage on source system: 

* OCPUs: up to 3 (6 vCPUs)
* RAM: 3 GB (SGA steam pool)



GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE = LOW

Resource usage on source system:

* OCPUs: up to 2 (4 vCPUs)
* RAM: 16.8MB (SGA stream pool)



Target database server CPU and memory requirements

Use ZDM parameter GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE to tune Oracle GoldenGate Replicat. Setting performance profile automatically configures relevant parameters to achieve the desired throughput and latency. ZDM defaults to HIGH.

GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE = HIGH

Replicat Appliers = 2 * CPU_COUNT of target database

GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE = LOW

Replicat Appliers = CPU_COUNT of target database / 2

Oracle GoldenGate server CPU requirements

OCPUs for GoldenGate server = `ceil(((#replicat_appliers1 / 2) + #vCPU_extract2 + #vCPU_OS3) / 2)`

1`#replicat_appliers`: number of GoldenGate Replicat Appliers configured using ZDM parameter GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE 

2`#vCPU_extract`: based on ZDM parameter 

GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE

3`#vCPU_OS`: used for Operating System resources management (set to 2 vCPUs) 

Example:

When:

* GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE= HIGH 
* GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE = HIGH
* Target DB CPU_COUNT = 5



`#vCPU_extract = 6`

`#replicat_appliers = 10`

`#vCPU_OS = 2`

then,

OCPUs for GG server = `ceil(6.5) = 7`

See [Oracle Zero Downtime Migration – Logical Migration Performance Guidelines](https://www.oracle.com/a/tech/docs/zdm-gg-performance.pdf) for details. 

Oracle GoldenGate server trail file storage space requirement

The space required depends upon the selected size of the trails, the amount of data being captured for replication, and how long the consumed trails are kept on the disk. The recommended minimum disk allocated for trails is computed as:

**((transaction log size * 0.33) * number of log switches per day) * number of days to retain trails**

For example, if the transaction logs are 1GB in size and there is an average of 10 log switches per day, it means that Oracle GoldenGate will capture 3.3GB data per day. To be able to retain trails for 7 days, the minimum amount of disk space needed to hold the trails is 23GB. See [Other Disk Space Considerations](https://docs.oracle.com/en/middleware/goldengate/core/21.3/installing/overview.md) for details. 

GGADMIN users in non-ADB source and target databases

Create a GoldenGate administration user, `ggadmin`, in source database granting all of the permissions listed in the example. If the source database is multitenant (CDB), create the user in the source PDB, as shown in this example. 
```
SQL> create user ggadmin identified by password
defaulttablespace users temporary tablespace temp;
SQL> grant connect, resource to ggadmin;
SQL> alter user ggadmin quota 100M ON USERS;
SQL> grant unlimited tablespace to ggadmin;
SQL> grant select any dictionary to ggadmin;
SQL> grant select any table to ggadmin;
SQL> grant create view to ggadmin;
SQL> grant execute on dbms_lock to ggadmin;
SQL> exec dbms_goldengate_auth.GRANT_ADMIN_PRIVILEGE('ggadmin');
```


If the source database is multitenant (CDB), also create user `c##ggadmin` in `CDB$ROOT` as shown in this example: 
```
SQL> create user c##ggadmin identified by password defaulttablespace userstemporary tablespace temp;
SQL> alter user c##ggadmin quota 100M ON USERS;
SQL> grant unlimited tablespace to c##ggadmin;
SQL> grant connect, resource to c##ggadmin container=all;
SQL> grant select any dictionary to c##ggadmin container=all;
SQL> grant select any table to ggadmin;
SQL> grant create view to c##ggadmin container=all;SQL> grant set container to c##ggadmin container=all;
SQL> grant execute on dbms_lock to c##ggadmin container=all;
SQL> execdbms_goldengate_auth.GRANT_ADMIN_PRIVILEGE('c##ggadmin',container=>'all');
SQL> create user c##ggadmin identified by password
```


If the target database is not an Autonomous Database, create a GoldenGate administration user, `ggadmin`, in the target database granting all of the permissions listed in the example. If the target database is multitenant (CDB), create the user in the target PDB, as shown in this example. 
```
SQL> create user ggadmin identified by password default tablespace users
temporary tablespace temp;
SQL> grant connect, resource to ggadmin;
SQL> alter user ggadmin quota 100M ON USERS;
SQL> grant unlimited tablespace to ggadmin;
SQL> grant select any dictionary to ggadmin;
SQL> grant create view to ggadmin;
SQL> grant select any table to ggadmin;
SQL> grant insert any table to ggadmin;
SQL> grant update any table to ggadmin;
SQL> grant delete any table to ggadmin;
SQL> grant comment any table to ggadmin;
SQL> grant execute on dbms_lock to ggadmin;
SQL> exec dbms_goldengate_auth.GRANT_ADMIN_PRIVILEGE('ggadmin');
```


Network connectivity requirements

* [Additional Connectivity Prerequisites for Oracle GoldenGate Hub](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-3BD5C670-E0E2-4278-B9C1-B62F0F6E2210)
* [Zero Downtime Migration Port Requirements](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-E6F8EF13-03A5-43DD-8F98-182632C83CB3)



Application switchover

[Handling Application Switchover in a Logical Migration](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-1455CCE0-A583-452B-8FA0-5B5F343EFE17)

### Source Database Prerequisites for Logical Migration {#GUID-004B4498-49B9-45A8-BD1B-5449857139A0}

During the migration job it is recommended that your database avoid Data Definition Language (DDL) operations to provide the most optimal environment for fast database replication. When DDL is replicated, Oracle GoldenGate Replicat serializes data to ensure that there are no locking issues between DML and DDL on the same objects.

Complete the following prerequisites on the source database to prepare for a logical migration.

**Offline and Online Migrations Require:**

* The character set on the source database must be the same as the target database.

* Configure the streams pool with the initialization parameter `STREAMS_POOL_SIZE`. 

    For offline logical migrations, for optimal Data Pump performance, it is recommended that you set `STREAMS_POOL_SIZE` to a minimum of 256MB-350MB, to have an initial pool allocated, otherwise you might see a significant delay during start up. 

    For online logical migrations, set `STREAMS_POOL_SIZE` to at least 2GB. See <https://support.oracle.com/epmos/faces/DocumentDisplay?id=2078459.1> for the recommendation 1GB `STREAMS_POOL_SIZE` per integrated extract + additional 25 percent. 

* System time of the Zero Downtime Migration service host and source database server should be in sync with your Oracle Cloud Infrastructure target.

    If the time on any of these systems varies beyond 6 minutes from the time on OCI, it should be adjusted. You can use `ntp time check` to synchronize the time if NTP is configured. If NTP is not configured, then it is recommended that you configure it. If configuring NTP is not an option, then you need to correct the time manually to ensure it is in sync with OCI time. 

* If you are using a database link, and your target database is on Autonomous Database Shared Infrastructure, you must configure TCPS on the source. Autonomous Database Shared Infrastructure doesn't allow a database link to a source that is not configured with TCPS.

* If you are migrating from an Amazon Web Services RDS environment, see [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA) for information about source environment preparations. 

* In the PDB being exported, if you have created local objects in the C## user's schema and you want to import them, then either make sure a common user of the same name already exists in the target CDB instance (for non-Autonomous Database targets) or use the following Zero Downtime Migration parameter to rename the schema on import.
```
DATAPUMPSETTINGS_METADATAREMAPS-1=type:REMAP_SCHEMA,oldValue:c##common_user,newValue:new_ name
```


* If you are migrating to Oracle Autonomous Database on Exadata Cloud@Customer from any on-premises Oracle Database, including existing Exadata Cloud@Customer systems, see [Migrating to Oracle Autonomous Database on Exadata Cloud@Customer](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-24C15009-A984-494F-9239-583D14248079) for additional prerequisite setup tasks. 




**Online Migrations Require:**

* If the source is Oracle Database 11.2, apply mandatory 11.2.0.4 RDBMS patches on the source database.

    See My Oracle Support note [ Oracle GoldenGate -- Oracle RDBMS Server Recommended Patches (Doc ID 1557031.1)](https://support.oracle.com/rs?type=doc&id=1557031.1)

    * Database PSU 11.2.0.4.210720 includes a fix for Oracle GoldenGate performance bug 28849751 - IE PERFORMANCE DEGRADES WHEN NETWORK LATENCY BETWEEN EXTRACT AND CAPTURE IS MORE THAN 8MS
    
    * OGG RDBMS patch 32248879 MERGE REQUEST ON TOP OF DATABASE PSU 11.2.0.4.201020 FOR BUGS 32048478 20448066 - This patch contains mandatory fix for Oracle GoldenGate Microservices bug 20448066 DBMS_XSTREAM_GG APIS SHOULD BE ALLOWED FOR SCA PROCESSES

* If the source is Oracle Database 12.1.0.2 or a later release, apply mandatory RDBMS patches on the source database.
* If the source is Oracle Database Standard Edition 2 available with Oracle Database 18c or Oracle Database 19c and is lower than DBRU 19.11, apply RDBMS patch for bug 29374604 - Integrated Extract not starting against Oracle RDBMS Standard Edition. 

    See My Oracle Support note [Latest GoldenGate/Database (OGG/RDBMS) Patch recommendations (Doc ID 2193391.1)](https://support.oracle.com/rs?type=doc&id=2193391.1), which lists the additional RDBMS patches needed on top of the latest DBBP/RU for Oracle Database 12c and later. 

    For source Oracle Database 12.1.0.2, along with above mandatory patches, apply patch 20448066. 

* Enable `ARCHIVELOG` mode for the database. See [Changing the Database Archiving Mode](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ADMIN-GUID-C12EA833-4717-430A-8919-5AEA747087B9). 

* Enable `FORCE LOGGING` to ensure that all changes are found in the redo by the Oracle GoldenGate Extract process. See [Specifying FORCE LOGGING Mode](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ADMIN-GUID-ACEA5B83-D70F-4F30-A85B-3030248F597B)

* Enable database minimal supplemental logging. See [Minimal Supplemental Logging](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=SUTIL-GUID-82A335B2-9F8F-4A95-A314-2EECDB0B3D2D). 
```
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
```


* Enable initialization parameter `ENABLE_GOLDENGATE_REPLICATION`. 

* Install the `UTL_SPADV` or `UTL_RPADV` package for Integrated Extract performance analysis. 

Source database version 19c and later, see [UTL_SPADV](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-8FF3932C-6B75-4AEF-8738-AE04658223EB)

Operational notes: To use this package, you must connect to an Oracle database as an Oracle Replication administrator (for example, `ggadmin`) and run the `utlrpadv.sql` script in the rdbms/admin directory in ORACLE_HOME. 

Earlier database versions, see [UTL_SPADV Operational Notes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-9F407868-DFE3-44A1-9FC7-AD77EB19CF88)

Operational notes: To use this package, you must connect to an Oracle database as an Oracle Replication administrator (for example, `ggadmin`) and run the `utlspadv.sql` script in the rdbms/admin directory in ORACLE_HOME. 

* During the migration period, to provide the most optimal environment for fast database replication, avoid large batch DML operations. Running large batch operations, like a single transaction that affects multi-millions of rows, can slow down replication rates. Create, alter, and drop DDL operations are not replicated.

* Set `WALLET_SOURCEADMIN` to specify the path to the directory that contains the auto login wallet file `cwallet.sso`, which provides the source database administrator password. See [WALLET_SOURCEADMIN](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-63A82F48-E61E-4A49-B3B2-204B83BC56E7). 
* For adding large number of `INCLUDEOBJECTS` in Datapump component, create a table in the Source database in the export user schema to list all the `TABLE` objects to be filtered and specify the following parameters. For example, create a table `SYSTEM.INCLUDE_TEMP_LIST` and list all objects specified in `INCLUDEOBJECTS` to be filtered for `SCHEMA SCOTT`: 

` INCLUDEOBJECTS-1=owner:SCOTT DATAPUMPSETTINGS_METADATAFILTERS-1=name:NAME_EXPR,value:’IN(select OBJECT_NAME from SYSTEM.INCLUDE_TEMP_LIST’)’, objectType:TABLE `



**Offline Migrations Require:**

* The `DATAPUMP_EXP_FULL_DATABASE` and `DATAPUMP_IMP_FULL_DATABASE` roles are required. These roles are required for Data Pump to determine whether privileged application roles should be assigned to the processes comprising the migration job. 

`DATAPUMP_EXP_FULL_DATABASE` is required for the export operation at the source database for the specified database user. The `DATAPUMP_IMP_FULL_DATABASE` role is required for the import operation at the specified target database for specified target database user. 

See the Oracle Data Pump documentation for more information.

* Oracle Database Standard Edition 2 does not allow Data Pump dump encryption support so you must encrypt the target database post-migration. To proceed with the migration, set DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ENCRYPTION =`NONE`. See, [DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ENCRYPTION](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-4D96D5ED-24F5-46EE-8F41-10FE36427347) for more information. So, specify `NONE` if database is Oracle Database Standard Edition 2 and Oracle Advanced Security is not enabled. 



**Prerequisites when the source database is IBM AIX**: 

1. Ensure that CURL is available if OSS is used as the data transfer medium.
2. Ensure that the `id` command is available. See [id Command](https://www.ibm.com/docs/en/aix/7.1?topic=i-id-command) for more information. 



**Configuring the PROFILE parameter**: 

Specify the full path to the source database profile file. ZDM maintains a key-value template for each of the supported database PROFILE. See [PROFILE](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-D5717422-94E6-48DB-849B-81499D2C91A8)

> **note:** 

The Zero Downtime Migration service host requires Perl to run the install script. 

If you plan to migrate from Oracle Database 11.2.0.4 sources, you also need the latest Perl patch 5.28.2 or later. Alternatively, for Zero Downtime Migration to invoke CPAT remotely, set the [RUNCPATREMOTELY](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-85FB677B-4BD0-4EBE-BE53-10DD48742D57) parameter to TRUE to avoid patching the Perl in the source database host. 

### Target Database Prerequisites for Logical Migration {#GUID-34138211-17BD-4CA6-8B7C-C211F64250F6}

Complete the following prerequisites on the target database to prepare for a logical migration.

**Data Pump-only logical migrations require:**

* The `DATAPUMP_IMP_FULL_DATABASE` role is required for the import operation at the specified target database for specified target database user. 




**All logical migrations require:**

* The character set on the source database must be the same as the target database.

* System time of the Zero Downtime Migration service host and source database server should be in sync with your Oracle Cloud Infrastructure target.

* All source database requirements be met. Some tasks are performed on both the source and target. See [Source Database Prerequisites for Logical Migration](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-004B4498-49B9-45A8-BD1B-5449857139A0)

* Ensure to set the [ENCRYPT_NEW_TABLESPACES](https://docs.oracle.com/en/database/oracle/oracle-database/23/refrn/ENCRYPT_NEW_TABLESPACES.md) parameter which specifies whether to encrypt newly created user tablespaces. Set the parameter to `ALWAYS` to encrypt the tablespace. 




> **note:** For logical migration, if the target is Oracle Database Appliance and the data transfer medium is Object Storage Service (OSS), ensure that OSS connectivity works. Fetch the CA certificates for OCI and update certificate store of Oracle Database Appliance. See [Obtain the Root CA Certificate from Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/uaids/obtain-root-ca-certificate-oracle-identity-cloud-service.md) for more information. 

**Online logical migrations require:**

* Disable auto-purge jobs on the target database immediately after instantiation (Data Pump Import). Otherwise, the purge jobs will cause data inconsistency condition on the target database causing GoldenGate Replicat to fail. Some of these jobs will purge data. 
    * During the ZDM_POST_DATAPUMP_TGT phase, ZDM will disable purge jobs in the target database.
    * ZDM enables the purge jobs in the target database during the ZDM_POST_SWITCHOVER_TGT phase. 



### Additional Logical Migration Prerequisites {#GUID-1D392BDA-4D82-49FC-A5E4-C23398A81A19}

Complete the following additional prerequisites to prepare for a logical migration.

**Create an OCI API key pair**

See [Required Keys and OCIDs](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#RequiredKeysandOCIDs) for details. 

**Configure AWS S3 security credentials**

If you are migrating from an Amazon Web Services RDS environment, see [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA) for information about source environment preparations. 

**Set Up Data Transfer Media**

* To use Object Storage data transfer medium:

    Create an Object Store bucket on Oracle Cloud Infrastructure if you are using Object Storage as a data transfer medium. This is not required for Oracle Exadata Database Service on Cloud@Customer or on-premises Exadata Database Machine targets.

* To use NFS shared storage:

    Ensure that the NFS is shared between the source and target database server and mapped to the Database Directory object.
    
    For information about how to mount NFS for dump storage, see [Mount Options for Oracle files for RAC databases and Clusterware when used with NFS on NAS devices (Doc ID 359515.1)](https://support.oracle.com/rs?type=doc&id=359515.1). 

* To use a database link (DBLINK):

    If you are using an **existing** database link between the target database to an on-premises source database by `global_name` of the source database, ensure that the database link is not broken. Zero Downtime Migration can reuse the pre-existing database link for migration if that data transfer medium is configured. 
    
    Zero Downtime Migration supports the use of a database link for all Autonomous Database targets; however, when you set up the database link for Autonomous Database Dedicated Infrastructure, you must use the Easy Connect syntax or provide a complete descriptor in the `USING 'connect string'` clause. You cannot use a network service name because the tnsnames.ora file is not available for lookup. Database links can only be used for TCP connections because TCPS connections require a wallet. 
    
    See [SQL Commands with Restrictions in Autonomous Database](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbdl/index.md#ADBDL-GUID-A701F5DB-1D7F-497C-9C0C-5FDBD653D817) and [Create Database Links from Autonomous Database to Oracle Databases](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/database-links-oracledb.md#GUID-84FB6B85-D60D-4EDC-BB3C-6485B2E5DF4D)

* If you are **not** using a database link for data transfer, ensure that the file system used for the Data Pump export directory has sufficient space to store Data Pump dump files. 

* To use Amazon S3 bucket:

See [Setting Up S3 Bucket Data Transfer Medium](preparing-logical-database-migration1.md#GUID-2514A61A-031F-40D0-B229-14699A1A782F)




**If the source uses self-signed database server certificates:**

If the source database listener is configured with TLS (TCPS) using self-signed database server certificates, then ensure that the self-signed certificate is added to the Zero Downtime Migration home cert store as follows.
```
keytool -import -keystore ZDM_HOME/jdk/jre/lib/security/cacerts -trustcacerts
-alias "src ca cert" -file source_db_server-certificate
```


**Online Migration Additional Prerequisites**

For online migration, do the following additional prerequisite tasks:

* **Set up an Oracle GoldenGate Microservices hub:**

    For Oracle Database Cloud Services targets, deploy the "Oracle GoldenGate for Oracle - Database Migrations" image from Oracle Cloud Marketplace.:
    
    The "Database Migrations" version of the Oracle GoldenGate Marketplace image provides limited free licensing for use with OCI Database Migration Service. See the license agreement for details.
    
    Any other use of GoldenGate requires purchasing a license for the Oracle GoldenGate product. See the Oracle GoldenGate documentation for more information.

    1. Log in to Oracle Cloud Marketplace.
    2. Search for the "Oracle GoldenGate for Oracle - Database Migrations" Marketplace listing.
    3. From the Marketplace search results, select the "Oracle GoldenGate for Oracle - Database Migrations" listing.
    4. For instructions to deploy the Marketplace listing, see [Deploying Oracle GoldenGate Microservices on Oracle Cloud Marketplace](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oggmp/provisioning-oracle-goldengate-microservices-oci-marketplace.md#GUID-EF6680D7-7571-41E7-BF2D-831BD79BCC15). 

For guidance in configuring Zero Downtime Migration GoldenGate settings and choosing the correct GoldenGate Hub shape, see Oracle MAA technical brief, [Oracle Zero Downtime Migration – Logical Migration Performance Guidelines](https://www.oracle.com/a/tech/docs/zdm-gg-performance.pdf). 

If you are migrating to Exadata Cloud@Customer, or any on-premises Oracle Exadata Database Machine, you must use an on-premises Oracle GoldenGate Microservices instance to create a deployment for the source and target. The “Oracle GoldenGate for Oracle – Database Migrations” marketplace offering now contains a downloadable docker image for migrations to ExaCC. You can review the latest marketplace VM at <https://cloudmarketplace.oracle.com/marketplace/en_US/listing/96175416>, and the documentation for this functionality can be found at [Migrating to Exadata Cloud@Customer Using Oracle Zero Downtime Migration](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oggmp/provisioning-oracle-goldengate-microservices-oci-marketplace.md#GUID-A52F5E0D-1AB8-4E89-8CE3-BBE9132C005C) . 

By default, Oracle GoldenGate deployment endpoints are configured using self-signed HTTPS certificate. Either of the following options are available: 
* Specify the [GOLDENGATEHUB_ALLOWSELFSIGNEDCERTIFICATE ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-68968802-F79A-4315-BBED-66195E2D96EA) parameter to `TRUE` in response file to indicate that ZDM can trust the self-signed HTTPS certificate. 
* Update the configuration for Oracle GoldenGate deployment to use a certificate issued by a trusted Certificate Authority.

* **Create a GoldenGate administration user:**

    Note that the examples shown below are for basic migrations. For comprehensive Oracle GoldenGate permissions information, see [Granting the Appropriate User Privileges](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oracle-db/preparing-database-oracle-goldengate.md#GUID-F9EBB989-E22F-4355-BE60-40F957B8515E) in *Using Oracle GoldenGate with Oracle Database*. 

    * On the Source Database:

        * Create a GoldenGate administration user, `ggadmin`, granting all of the permissions listed in the example. If the source database is multitenant (CDB), create the user in the source PDB, as shown in this example. 
        ```
        SQL> create user ggadmin identified by password
        default tablespace users temporary tablespace temp;
        SQL> grant connect, resource to ggadmin;
        SQL> alter user ggadmin quota 100M ON USERS;
        SQL> grant unlimited tablespace to ggadmin;
        SQL> grant select any dictionary to ggadmin;
        SQL> grant create view to ggadmin;
        SQL> grant execute on dbms_lock to ggadmin;
        SQL> exec dbms_goldengate_auth.GRANT_ADMIN_PRIVILEGE('ggadmin');
        ```


        * If the source database is multitenant (CDB), also create user `c##ggadmin` in `CDB$ROOT` as shown in this example. 
        ```
        SQL> create user c##ggadmin identified by password default tablespace users
        temporary tablespace temp;
        SQL> alter user c##ggadmin quota 100M ON USERS;
        SQL> grant unlimited tablespace to c##ggadmin;
        SQL> grant connect, resource to c##ggadmin container=all;
        SQL> grant select any dictionary to c##ggadmin container=all;
        SQL> grant create view to c##ggadmin container=all;
        SQL> grant set container to c##ggadmin container=all;
        SQL> grant execute on dbms_lock to c##ggadmin container=all;
        SQL> exec
        dbms_goldengate_auth.GRANT_ADMIN_PRIVILEGE('c##ggadmin',container=>'all');
        ```


    * On the Target Database:

        * If the target is Autonomous Database, unlock the pre-created `ggadmin` user. 
        
        * If the target is not Autonomous Database, create a `ggadmin` user in the target PDB as shown in the above examples. This user is similar to the `ggadmin` user on the source database. Grant the following additional privileges: 
        ```
        SQL> grant select any table to ggadmin;
        SQL> grant insert any table to ggadmin;
        SQL> grant update any table to ggadmin;
        SQL> grant delete any table to ggadmin;
        ```


        See [Establishing Oracle GoldenGate Credentials ](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oracle-db/establishing-oracle-goldengate-credentials.md) for information about privileges required for a "Replicat all modes" user. 

    * Regardless of migration mode (FULL or SCHEMA or TABLE), ZDM enforces the following privileges for `ggadmin` user in the target database for DML replication:
    ```
    grant select any table to ggadmin;
    grant insert any table to ggadmin;
    grant update any table to ggadmin;
    grant delete any table to ggadmin;
    ```
    
    
    * Similarly, in case of DDL replication, ZDM enforces the following privileges for `ggadmin` user in the target database:
    ```
    GRANT CREATE ANY TABLE to GGADMIN;
    
    GRANT ALTER ANY TABLE to GGADMIN;
    
    GRANT DROP ANY TABLE to GGADMIN;
    
    GRANT CREATE ANY INDEX to GGADMIN;
    
    GRANT ALTER ANY INDEX to GGADMIN;
    
    GRANT DROP ANY INDEX to GGADMIN;
    
    GRANT CREATE ANY VIEW to GGADMIN;
    
    GRANT ALTER ANY VIEW to GGADMIN;
    
    GRANT DROP ANY VIEW to GGADMIN;
    
    GRANT CREATE ANY PROCEDURE to GGADMIN;
    
    GRANT ALTER ANY PROCEDURE to GGADMIN;
    
    GRANT DROP ANY PROCEDURE to GGADMIN;
    ```


    * To replicate tables with Oracle GoldenGate `support_mode = ID KEY`, the following privileges are required for `ggadmin` user in the source database. See [Setting Flashback Query](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oracle-db/preparing-database-oracle-goldengate.md#GUID-AE2D0BE3-F7C3-4A8C-B96C-0498AE71D244) for more information.
        ```
        GRANT FLASHBACK ANY TABLE TO ggadmin;
        ```


* **If the source database is configured to use SSL/TLS:**

If the source database is configured to use SSL/TLS, then ensure that the wallet containing certificates for TLS authentication is located in directory `/u02/deployments/deployment_name/etc` on the GoldenGate instance. 

* **If Oracle Database Vault is enabled in the source or target database:**

If you want to use Oracle GoldenGate in an Oracle Database Vault environment in the source or target database, then you must have the appropriate privileges granted for an Oracle GoldenGate account. 

See [Privileges for Using Oracle GoldenGate with Oracle Database Vault](https://docs.oracle.com/en/database/oracle/oracle-database/19/dvadm/dba-operations-in-an-oracle-database-vault-environment.md#GUID-8BC01B07-01AE-4973-9E72-BD637D773D01) for more information. 

* **If the target database is configured to use SSL/TLS:**

Ensure that the wallet containing certificates for TLS authentication is located in the correct location on the GoldenGate instance, as follows:

* For an Autonomous Database, the wallet file should be located in directory `/u02/deployments/deployment_name/etc/Wallet_ path` or ` /u02/deployments/deployment_name/etc/adb`. The new path is an alternate to use the Oracle GoldenGate for concurrent migration to two different targets. 

* For a co-managed database, the wallet file should be located in directory ` /u02/deployments/deployment_name/etc/*`Wallet_`*`

Autonomous databases are always configured to use TLS.

* **If GoldenGate schema name is different from the default value ggadmin:**

ZDM supports GoldenGate schema name different from default value other than `ggadmin`. By default, GoldenGate deployment is configured with `GGSCHEMA= ggadmin` in the `GLOBALS` file. You can specify a different GoldenGate schema name for source (PDB, CDB) and target. If you specify a different GoldenGate schema name, then you will need to update the `GLOBALS` file. 

* **Dynamic Export Parallelism for online logical migration**

In some cases, GoldenGate replication from source to target is unsuccessful due to `INSTANTIATION_SCN MISMATCH` between `DBA_APPLY_INSTANTIATED_SCHEMAS` and `DBA_APPLY_INSTANTIATED_OBJECTS`. As a solution, ZDM limits initial parallelism for Data Pump jobs to 1, until the `SCHEMA/PROCACT_SCHEMA` path is processed. Subsequently, ZDM adjusts the parallelism to the expected level for the remaining data export. 

* **Connecting to OCI through proxy server**

When ZDM server requires to access OCI REST endpoints through HTTP_PROXY, import the HTTP PROXY server certificate to the ZDM server `$ZDM_HOME/jre/lib/security/cacerts` using the `ZDM_HOME/jdk/bin/keytool` binary. 

* **Source and target database requirements for Oracle Database 23ai**

For database versions earlier than Oracle Database 23ai, continue granting privileges as follows: 

`SQL> exec dbms_goldengate_auth.GRANT_ADMIN_PRIVILEGE(‘ggadmin’);`

However, for Oracle Database 23ai, grant the following roles for source and target:
```
Source ggadmin: grant OGG_CAPTURE to ggadmin;
Target ggadmin: grant OGG_APPLY to ggadmin;
```


* **Source and target database requirements for non Autonomous databases**

Ensure to have the supported default shell as `bash`. If this is not the case, then you might get the following error: `ZDM_VALIDATE_SRC fails with PRCZ-2103 : Failed to execute command"/usr/bin/id" LANG=en_US.UTF-8`. 

This is the issue with default shell being `cshell` and not bash. On `cshell`, the environment parameters are not recognized so all the commands with environments will fail. 

* **If the version of the target database is earlier than Oracle Database 19c (19.18)**

You require Bug 34677088 if the version of the non-ADB target database is earlier than Oracle Database 19c (19.18) to avoid the following error:

`ORA-43853: SECUREFILE LOBs (Large Objects) cannot be used in non-automatic segment space management tablespace`

During import, ZDM applies the transform parameter `LOB_STORAGE` to `SECUREFILE` and if the target tablespace is not `ASSM`, then import can fail with `ORA-43853: SECUREFILE LOBs (Large Objects) cannot be used in non-automatic segment space management tablespace`. 

If you cannot apply BLR for Bug 34677088 in the target database, then set `DATAPUMPSETTINGS_SECUREFILELOB` to `FALSE`. However, the action makes all other objects (present in `ASSM` tablespaces) to not get transformed to secure LOB. 

* Ensure that `/tmp` is mounted with `execute` permission as a prerequisite for the source and target databases if you want to perform a logical migration with host SSH access. 



#### Additional Connectivity Prerequisites for Oracle GoldenGate Hub {#GUID-3BD5C670-E0E2-4278-B9C1-B62F0F6E2210}

To perform online logical migrations with Oracle GoldenGate, in addition to the connectivity between the Zero Downtime Migration service host and the source and target database servers, you must also ensure connectivity between the Oracle GoldenGate hub and the source and target database servers.

Ensure that the OCI network security rules allow the following connections.

**Table: Prerequisite Connections for Online Logical Migration**

Initiator | Target | Protocol | Port | Purpose  
---|---|---|---|---  
GoldenGate hub | Source database | TCP TCPS | 1521 User-defined | SQL*Net  
GoldenGate hub | Target database | TCP TCPS | 1521 1522 for ADB-Serverless, port 2484 for ADB-Dedicated, or user-defined for non-ADB | SQL*Net  
ZDM server | GoldenGate hub | HTTPS | 443 | Oracle GoldenGate Microservice REST API calls  

The Zero Downtime Migration server should be allowed to make HTTPS over port 443 calls to an OCI REST endpoint.

#### Validating Connections to and from the Oracle GoldenGate Marketplace Instance {#GUID-0F2D9B23-C1DE-4DB9-BB0D-5011192190C0}

1. From Zero Downtime Migration server to Oracle GoldenGate server.

On Zero Downtime Migration server, run
```
curl -v --insecure -u oggadmin_username https://ogg_instance_fqdn_or_ip/services/v2/deployments
```


Oracle GoldenGate server credentials can be found in /home/opc/ogg-credentials.json on the GoldenGate server.

2. From Oracle GoldenGate server to source database server.

Assuming that the source database listener is not TLS/SSL enabled, on the Oracle GoldenGate server, run
```
export LD_LIBRARY_PATH=/u01/app/ogg/lib:/u01/app/ogg/lib/instantclient
/u01/app/ogg/lib/instantclient/sqlplus username@'source_listener_hostname_or_ip:source_listener_port/source_db_service_name'
```


> **note:** This is applicable only for Oracle GoldenGate 21c and higher versions. 

3. From Oracle GoldenGate server to target database server

If the target database is an Autonomous Database, refer to "Online Migration Additional Prerequisites" at [Additional Logical Migration Prerequisites](preparing-logical-database-migration1.md#GUID-1D392BDA-4D82-49FC-A5E4-C23398A81A19), and ensure that the Autonomous Database wallet containing certificates for TLS authentication exists in the correct location on the Oracle GoldenGate instance. 

On Oracle GoldenGate server, run
```
export LD_LIBRARY_PATH=/u01/app/ogg/lib:/u01/app/ogg/lib/instantclient
/u01/app/ogg/lib/instantclient/sqlplus username@'tcps://abd_listener_hostname_or_ip:adb_listener_port/adb_service_name?wallet_location=dir_path&ssl_server_cert_dn=cert_dn'
```


Refer to [Connect SQL*Plus with TLS Authentication](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/connect-sqlplus-tls.md) for more details about connecting to an Autonomous Database using SQL*Plus. 

If the target database is not an Autonomous Database and is not TLS/SSL enabled, on the Oracle GoldenGate server, run
```
export LD_LIBRARY_PATH=/u01/app/ogg/lib:/u01/app/ogg/lib/instantclient
/u01/app/ogg/lib/instantclient/sqlplus username@'target_listener_hostname_or_ip:target_listener_port/target_db_service_name'
```


> **note:** This is applicable only for Oracle GoldenGate 21c and higher versions. 




#### Validating Connections to and from an Oracle GoldenGate Docker Deployment {#GUID-B7B066CD-7CE7-4748-BF33-42F86DFCEFFF}

1. From Zero Downtime Migration server to Oracle GoldenGate deployment.

On Zero Downtime Migration server, run
```
curl -v --insecure -u oggadmin_username https://ogg_deployment_fqdn_or_ip/services/v2/deployments
```


2. From Oracle GoldenGate deployment to source database server.

As an `ogg` user inside the docker container, run 
```
export ORACLE_HOME=/u01/ogg/lib/instantclient
$ORACLE_HOME/bin/sqlplus username@'source_listener_hostname_or_ip:source_listener_port/source_db_service_name'
```


3. From Oracle GoldenGate server to target database server

As an `ogg` user inside the docker container, run 
```
export ORACLE_HOME=/u01/ogg/lib/instantclient
export LD_LIBRARY_PATH=/u01/ogg/lib/instantclient
$ORACLE_HOME/sqlplususername@'target_listener_hostname_or_ip:target_listener_port/target_db_service_name'
```





### Setting Logical Migration Parameters {#GUID-FCA7FEC2-D064-432F-A793-EF63419A924C}

Set the required logical migration response file parameters. Get the response file template, `$ZDM_HOME/rhp/zdm/template/zdm_logical_template.rsp`, which is used to create your Zero Downtime Migration response file for the database migration procedure, and edit the file as described here. 
    
   The logical migration response file settings are described in detail in [Zero Downtime Migration Logical Migration Response File Parameters Reference](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-D580AD1C-C209-4F0F-A630-863D206FF0E5). 
    
The following parameters are required for an offline or online logical migration:

* `MIGRATION_METHOD`: Set to `ONLINE_LOGICAL` for online migration with GoldenGate or `OFFLINE_LOGICAL` for an offline Data Pump transfer. 

* `DATA_TRANSFER_MEDIUM`: Set to 

`OSS` for Object Storage bucket 

`NFS` for a shared Network File System 

`DBLINK` for a direct transfer using a database link 

`COPY` to use secure copy 

`AMAZONS3` to use an Amazon S3 bucket (only applies to migrations from an AWS RDS Oracle source), see [Migrating from Amazon Web Services RDS Oracle to Oracle Cloud](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA)) 

Unless you are using the default data transfer servers for handling the Data Pump dumps, you may also need to configure the data transfer node settings for the source and target database environments. 

See [Configuring the Transfer Medium and Specifying Transfer Nodes](preparing-logical-database-migration1.md#GUID-3580F7B3-03A5-4ADA-9C2F-FEADF9E125A7) for details. 

* For a logical migration of an Oracle Database 11g source to an 11g target, set `DATAPUMPSETTINGS_SECUREFILELOB=FALSE` or you may get errors. 

* Set the following target database parameters. 
    * `TARGETDATABASE_OCID` specifies the Oracle Cloud resource identifier. 
    
    For example: ocid1.instance.oc1.phx.abuw4ljrlsfiqw6vzzxb43vyypt4pkodawglp3wqxjqofakrwvou52gb6s5a 
    
    See also <https://docs.cloud.oracle.com/en-us/iaas/Content/General/Concepts/identifiers.htm>
    
    * `TARGETDATABASE_ADMINUSERNAME` specifies the database administrator user name. For example, for a co-managed database migration user name as `system` and for an Autonomous Database migration user name as `admin`. 
    
    * `TARGETDATABASE_CONNECTIONDETAILS_SERVICENAME` specifies the fully qualified service name. 
    
    This parameter is optional for Autonomous Database targets; however if an HTTP proxy is required to connect, specify it.

    In addition, for Oracle Autonomous Database on Dedicated Exadata Infrastructure and Oracle Autonomous Database on Exadata Cloud@Customer with **fractional OCPU** service you must specify the appropriate alias with TLS service in the parameter. 
    
    You can specify any predefined fractional service alias available; however, for Autonomous Transaction Processing workloads TP* services are preferred over LOW* services because LOW* is meant for low priority batch jobs.
            
    * `TP_TLS`, `LOW_TLS` (for Autonomous Transaction Processing workloads) 
        * `LOW_TLS` (for Autonomous Data Warehouse workloads) 
* Set the following source database parameters. 
    * `SOURCEDATABASE_ADMINUSERNAME` specifies the database administrator user name. For example, user name as `system`. 
    
    * `SOURCEDATABASE_CONNECTIONDETAILS_HOST` specifies the listener host name or IP address. In case of Oracle RAC, the SCAN name can be specified. (not required for Autonomous Database) 
    
    * `SOURCEDATABASE_CONNECTIONDETAILS_PORT` specifies the listener port number. (not required for Autonomous Database) 
    
    * `SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME` specifies the fully qualified service name. (not required for Autonomous Database) 
    
    For example: *`service_name`*.*`DB_domain`* 
    
    See also <https://docs.cloud.oracle.com/en-us/iaas/Content/Database/Tasks/connectingDB.htm>

    * For migrations from an AWS RDS source, see [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA) for additional parameter settings. 

* Set the following `OCIAUTHENTICATIONDETAILS` parameters. 

    For more information about the required settings, see <https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#RequiredKeysandOCIDs>
    
    * `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_TENANTID` specifies the OCID of the OCI tenancy. You can find this value in the Console under Governance and Administration, Administration, Tenancy Details. The tenancy OCID is shown under Tenancy Information. 
    
    For example: ocid1.tenancy.oc1..aaaaaaaaba3pv6wkcr4jqae5f44n2b2m2yt2j6rx32uzr4h25vqstifsfdsq 
    
    See also <https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingtenancy.htm>
    
    * `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_USERID` specifies the OCID of the IAM user. You can find this value in the Console under Profile, User Settings. 
    
    See also <https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm>
    
    * `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_FINGERPRINT` specifies the fingerprint of the public API key. 
    
    * `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_PRIVATEKEYFILE` specifies the absolute path of API private key file. 
    
    * `OCIAUTHENTICATIONDETAILS_REGIONID` specifies the OCI region identifier. 
    
    See the Region Identifier column in the table at <https://docs.cloud.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm>




**Oracle GoldenGate Settings**

During the migration job it is recommended that your database avoid Data Definition Language (DDL) operations to provide the most optimal environment for fast database replication. When DDL is replicated, Oracle GoldenGate Replicat serializes data to ensure that there are no locking issues between DML and DDL on the same objects.

The `DBOPTIONS DEFERREFCONST` option is set by default for Oracle GoldenGate Replicat non-integrated Parallel Replicat. `DEFERREFCONST` allows to migrate constraints and not disable the constraints on the target tables or setting them to DEFERRED. When used, `DEFERREFCONST` defers both `DEFERABLE` and `NOT DEFERABLE` constraints. `DEFERREFCONST` applies to every transaction that is processed by Replicat. 

For online logical migrations, in addition to the above, you must also set the GoldenGate parameters, `TARGETDATABASE_GGADMINUSERNAME`, `SOURCEDATABASE_GGADMINUSERNAME`, `SOURCECONTAINERDATABASE_GGADMINUSERNAME`, and the parameters prefixed with `GOLDENGATEHUB` and `GOLDENGATESETTINGS`. 

By default, Zero Downtime Migration excludes all DDL from GoldenGate replication. However, you can override this behavior by setting the parameter `GOLDENGATESETTINGS_REPLICATEDDL=true`. 

By default, ZDM ensures that Oracle GoldenGate Extract and Replicat are auto start enabled for resiliency to any unexpected failures.

See [Zero Downtime Migration Logical Migration Response File Parameters Reference](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-D580AD1C-C209-4F0F-A630-863D206FF0E5) for details about these parameters. 

Set `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE` to simplify Oracle GoldenGate Replicat configuration. This parameter replaces the need to configure four separate ` GOLDENGATESETTINGS_REPLICAT_*` parameters listed below. 

Starting with Oracle Zero Downtime Migration 21c (21.4) release, the following parameters are deprecated and will be desupported in a future release: 

* `GOLDENGATESETTINGS_REPLICAT_MAPPARALLELISM`
* `GOLDENGATESETTINGS_REPLICAT_APPLYPARALLELISM`
* `GOLDENGATESETTINGS_REPLICAT_MAXAPPLYPARALLELISM`
* `GOLDENGATESETTINGS_REPLICAT_MINAPPLYPARALLELISM`

If you configure the [GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-7D0DAA07-ABB6-4AFF-B736-70F241A1F602) parameter, then you do not have to configure the deprecated parameters. However, if you do configure these deprecated parameters, then the GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE parameter will not be valid. 

**Oracle Data Pump Settings**

Zero Downtime Migration automatically sets optimal defaults for Data Pump parameters to achieve better performance and ensure data security. If you need to further tune performance, there are several Data Pump settings that you can configure in the response file. 

You can set [DATAPUMPSETTINGS_EXPORTVERSION](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-FF32FE8D-99A1-4535-ABD2-FDFBDB0934A4) parameter to specify the Data Pump job export version. ZDM already sets the version parameter based on source and target version. This version parameter setting to 12 is required only for legacy Database upgraded to 11.2.0.4 and having Work Space Manager enabled. You can select the following values: 

* COMPATIBLE
* 12
* LATEST



The default `DATAPUMPSETTINGS_JOBMODE=SCHEMA` is recommended for migrations to Autonomous Database. 

See [Default Data Pump Parameter Settings for Zero Downtime Migration](preparing-logical-database-migration1.md#GUID-AE6D7CFD-47AE-4AAD-80AC-4850E60F2ED7) for information about the default Data Pump property settings, how to select schemas or objects for inclusion or exclusion, and Data Pump error handling. 

See [Zero Downtime Migration Logical Migration Response File Parameters Reference](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-D580AD1C-C209-4F0F-A630-863D206FF0E5) for all of the Data Pump parameters you can set through Zero Downtime Migration. 

See [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA) for information about setting Data Pump parameters for migration from AWS RDS. 

**Creating PAR URL once per job**: 

For logical migration, ZDM creates a PAR URL in the start/resume of every job and deletes the same PAR URL on completion/failure of job. ZDM uses this PAR URL for upload/download of all the dump and log files. You can customize the validity of PAR URL using the `oracle.zdm.datapump.par_url_validity_in_days` property. 

**Settings for performing logical migration from ADB (ADBS/ADBD) as source**

ZDM supports logical migration from ADB (ADB-S/ADB-D) source. You can either provide ADB connection details with the existing `SOURCEDATABASE_CONNECTIONDETAILS` properties or use a new parameter `SOURCEDATABASE_OCID`. 

> **note:** The value of `SOURCEDATABASE_ENVIRONMENT_DBTYPE` must be `ADBD/ADBS`. 

### Registering a New Region {#GUID-48C5E3B4-508F-46F4-A4C1-104AABDABB31}

Use the following response parameters to register a new realm:

* `OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_KEY=OC4`
* `OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_DOMAINCOMPONENT=realm.example`



Use the following response parameters to register a new region:

* `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_REALMKEY=OC4`
* `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_KEY=REGID`
* `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_ID=region-id`



For example, to register `eu-dcc-milan-1` Region and OC14 Realm, use the next configuration: 

* `OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_KEY=OC14`
* `OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_DOMAINCOMPONENT=oraclecloud14.com`
* `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_REALMKEY=OC14`
* `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_KEY=BGY`
* `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_ID=eu-dcc-milan-1`



For information related to the available regions with the following parameter values: 

* Region Identifier
* Region Key
* Realm Key

See <https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm>. 

For information about OCI SDK configuration values for the most recent Regions and Realms, see: 

* [Region](https://github.com/oracle/oci-java-sdk/blob/master/bmc-common/src/main/java/com/oracle/bmc/Region.java)* [Realm](https://github.com/oracle/oci-java-sdk/blob/master/bmc-common/src/main/java/com/oracle/bmc/Realm.java



### Configuring the Transfer Medium and Specifying Transfer Nodes {#GUID-3580F7B3-03A5-4ADA-9C2F-FEADF9E125A7}

Zero Downtime Migration offers various transfer options to make Oracle Data Pump dumps available to the target database server.

Using the `DATA_TRANSFER_MEDIUM` response file parameter you can configure the following data transfer methods: 

* `OSS`: Oracle Cloud Object Storage. 

Supported for all migration types and targets.

* `NFS`: Network File System 

Supported for co-managed and user managed targets.

* `DBLINK`: Direct data transfer from the source to the target over a database link. 

Supported for all migration types and targets.

* `COPY`: Transfer dumps to the target transfer node using secure copy. 

Supported for co-managed and user managed targets.

* `AMAZON3`: Amazon S3 bucket 
* Only applies to migrations from an AWS RDS Oracle source. See [Migrating from Amazon Web Services RDS Oracle to Oracle Cloud](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA) for more information. 



> **note:** To take advantage of parallelism and achieve the best data transfer performance, Oracle recommends that you transfer data using `OSS` or `NFS` for databases over 50GB in size. The `DBLINK` transfer medium can be convenient for smaller databases, but this choice may involve uncertainty in performance because of its dependence on network bandwidth for the duration of the transfer. 

Once the export of dumps on the source is completed, the dumps are uploaded or transferred in parallel as defined by parameter `DUMPTRANSFERDETAILS_PARALLELCOUNT` (defaults to 3), and any transfer failures are retried by default as specified in the parameter `DUMPTRANSFERDETAILS_RETRYCOUNT` (defaults to 3). 

The transfer of dumps can be done from any node at the source data center, provided that the dumps are accessible from the given node. It is crucial to ascertain the network connectivity and transfer workload impact on the source database server in order to decide which data transfer approach to take. 

Direct Transfer from Source to Target

This option applies only to co-managed cloud target databases.

Zero Downtime Migration enables logical migration using direct transfer of the Data Pump dump from the source to the target securely. The data is copied over from the source database directory object path to the target database server directory object path, or to a target transfer node, using either secure copy or RSYNC. This avoids the data being transferred over a WAN or needing additional shared storage between the source and target environments. This capability greatly simplifies the logical migration within the data center. 

About Transfer Nodes

You will configure a node, referred as a **transfer node** , for both the source data center and the target tenancy. 

The response file parameters that are prefixed with `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE` designate the node that handles the export dumps at the source data center. This **source transfer node** defaults to the source database. 

Similarly, the response file parameters that are prefixed with `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE` designate the node that handles the import of dumps at the target. This **target transfer node** defaults to the target database, for co-managed targets. 

Transfer Node Requirements

The source transfer node can be any of the following:

* Source database server (default)
* NAS mounted server 
* Zero Downtime Migration service node



The target transfer node can be any of the following:

* Target Database server (default)
* NAS mounted server 
* Zero Downtime Migration service node



For a server to be designated as transfer node, the following critical considerations are necessary. 

* Availability of CPU and memory to process the upload or transfer workload

* Connectivity to the specified upload or transfer target

    * Port 443 connectivity to Object Storage Service if the chosen data transfer medium is `OSS`
    
    * Port 22 connectivity to target storage server if the chosen transfer medium is `COPY`

* Availability of Oracle Cloud Infrastructure CLI. For speedier and resilient upload of dumps this is the recommended transfer utility for the `OSS` transfer medium. 

* OCI CLI must be installed and configured as detailed in <https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm>. 

Installing and configuring OCI CLI on each source database server may not be feasible. In such cases, one of the nodes in the data center can be designated as a transfer node with OCI CLI configured, and this node can share a network storage path with the database servers for Data Pump dumps to be created. This also avoids the upload workload consuming additional CPU and memory on production database servers. 




The designated transfer node can act as the gateway server at the data center for the external data transfer allowing transfer data traffic, thus avoiding the need to allow data transfer from the source database server or to the target database server.

Optionally, the additional transfer node requirement can be avoided by leveraging the Zero Downtime Migration server as the transfer node, provided that the Zero Downtime Migration service is placed in an on-premises data center and can meet the transfer node requirements listed above.

For transfer node and database server sharing storage to stage the dumps, ensure that the user on transfer node and database user share a common primary group for the dump files to be read on both the nodes. 

> **note:** The `dbuser` plug-in shares a common group. 

Using the Oracle Cloud Object Storage Transfer Medium

Object Storage data transfer medium is supported for all migration types and targets.

When using Object Storage as the data transfer medium, by setting `DATA_TRANSFER_MEDIUM=OSS`, it is recommended that dumps be uploaded using OCI CLI for faster and more secure and resilient uploads. You must configure OCI CLI in the upload node, and set parameter `DUMPTRANSFERDETAILS_SOURCE_USEOCICLI` to `TRUE`, the parameters for OCI CLI are 

`DUMPTRANSFERDETAILS_SOURCE_USEOCICLI`

`DUMPTRANSFERDETAILS_SOURCE_OCIHOME`

For Oracle Cloud Object Storage based migration, ensure that the cloud access user is privileged to create a pre- authenticated URL for object storage bucket, specified for Data Pump Dump staging. See [Using Pre-Authenticated Requests](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm) for details. 

Using the Database Link Transfer Medium

Supported for online and offline migrations to all migration targets

When you set `DATA_TRANSFER_MEDIUM=DBLINK`, a database link is created from the OCI co-managed database or Autonomous Database target to the source database using the `global_name` of the specified source database. 

Zero Downtime Migration creates the database link if it does not already exist, and the link is cleaned once the Data Pump import phase is complete.

Using the NFS Transfer Medium

The NFS mode of transfer is available, by setting `DATA_TRANSFER_MEDIUM=NFS`, for co-managed target databases that avoid the transfer of dumps. You should ensure that the specified path is accessible between the source and target database server path. 

Zero Downtime Migration ensures the security of dumps in the shared storage by preserving the restricted permission on the dumps such that only the source and target database users are allowed to access the dump.

See [Mount Options for Oracle files for RAC databases and Clusterware when used with NFS on NAS devices (Doc ID 359515.1) ](https://support.oracle.com/rs?type=doc&id=359515.1)

Using the Copy Transfer Medium

Supported for co-managed and user managed target databases only.

1. To use COPY use the `oracle` user and set it in `DUMPTRANSFERDETAILS_TRANSFERTARGET_USER`. 
2. Configure the `ssh` authentication ready for database user `oracle` in the target database. 

> **note:** The `DUMPTRANSFERDETAILS_TRANSFERTARGET_USERKEY` RSA public key must be in the `authorized_keys` file. 

3. The `zdmuser` connects from the ZDM node to the source database as an `auth` user using `zdmauth` or `dbuser` authentication plugins. 
4. ZDM copies over the RSA key file from ZDM node, specified in ` DUMPTRANSFERDETAILS_TRANSFERTARGET_USERKEY`. 
5. The `zdmuser` uses the SSH connection as `sudo` user (such as opc) or `dbuser` (Oracle) to the source database and performs copy of dumps connecting to the target database as` DUMPTRANSFERDETAILS_TRANSFERTARGET_USER`. 
6. ZDM deletes the copied RSA key from the source node. 

You can transfer the dumps from the source to the target securely, by setting `DATA_TRANSFER_MEDIUM=COPY`. 

The relevant parameters are as follows:




`DUMPTRANSFERDETAILS_TRANSFERTARGET_USER`

`DUMPTRANSFERDETAILS_TRANSFERTARGET_USERKEY`

`DUMPTRANSFERDETAILS_TRANSFERTARGET_HOST`

`DUMPTRANSFERDETAILS_TRANSFERTARGET_SUDOPATH`

`DUMPTRANSFERDETAILS_TRANSFERTARGET_DUMPDIRPATH`

You can leverage the RSYNC utility instead of SCP. Set `DUMPTRANSFERDETAILS_RSYNCAVAILABLE` to `TRUE`, and verify that RSYNC is available both at the source and target transfer nodes. 

### Selecting Objects for Migration {#GUID-AFAC1F36-BB52-4EFC-9586-18970BC65B43}

In Zero Downtime Migration, you can specify objects to include or exclude from a logical migration job using parameters in the response file.

Specify rules for objects to include, exclude, or reload in a migration job using the `EXLCUDEOBJECTS-n`, `RELOADOBJECTS-n`and `INCLUDEOBJECTS-n` response file parameters. You can either include or exclude objects in a migration, but you cannot do both. 

If no rule is defined, all schemas and objects of the source database will be migrated, with exceptions listed in [What Is Migrated During Initial Load](introduction-to-zero-downtime-migration.md#GUID-A62B0689-9902-4027-8CEF-BC6410286225). 

If you specify **Include** rules, the migration will only move the specified objects and their dependent objects; all other objects are automatically excluded. USER and TABLESPACE_QUOTA objectType associated with included schema are included by default. 

When specifying **Exclude** rules, the specified objects and their dependent objects will not be migrated using Oracle Data Pump and will not be replicated using Oracle GoldenGate. 

If you specify **Reload** rules, the specified objects will not be replicated using Oracle GoldenGate. Instead, the specified objects will be migrated to target database using Oracle Data Pump during phase `ZDM_RELOAD_PARALLEL_EXPORT_IMPORT` when there is no active workload on source database. 

You can define multiple include, exclude, or reload rules by incrementing the integer appended to the parameter name, as shown in these examples.

Example of a single include rule:

`INCLUDEOBJECTS-1=owner:*`ownerValue1`*, objectName:*`objectNameValue1`*, objectType:*`objectTypeValue1`*`

Example of two exclude rules:

`EXCLUDEOBJECTS-1=owner:*`ownerValue1`*, objectName:*`objectNameValue1`*, objectType:*`objectTypeValue1`*`

`EXCLUDEOBJECTS-2=owner:*`ownerValue2`*, objectName:*`objectNameValue2`*, objectType:*`objectTypeValue2`*`

Example of two reload rules:

`RELOADOBJECTS-1=owner: *`ownerValue1`*, `objectName:`* objectNameValue1`*, `objectType: *`objectTypeValue1`* RELOADOBJECTS-2=owner:*`ownerValue2`*, objectName:*`objectNameValue2`*, objectType:*`objectTypeValue2`*`

Use of wildcard expressions are allowed for RELOADOBJECTS parameter:

`RELOADOBJECTS-1=*`ownerValue1`*,objectName:.*,objectType:TABLE `

`RELOADOBJECTS-1=owner:*`ownerValue1`*,objectName:EMPL.*,objectType:TABLE`

Required Parameter Fields

To specify `INCLUDEOBJECTS-n`, `EXLCUDEOBJECTS-n`, and `RELOADOBJECTS-n` response file parameters, enter values for each of the following fields: 

* **owner** specifies the owner of the selected database objects. When using Include rules, all rules must be for the same owner, and wild characters are not allowed. 

* **objectName** specifies the name of selected database objects 

* **objectType** specifies the type of selected database objects. You can select ALL to select objects of all types (default). 




You can filter owner and objectName fields using any valid pattern in [Java class Pattern](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.md). For example, you can enter .* in the objectName field to select objects of any name. 

Restrictions

You must note the following restrictions:

* When excluding an object in a specified schema, and an object of the same name exists in a different schema that is also part of the migration, the objects will not be excluded (that is, the rule is ignored). The exclusion can be accomplished by migrating the schemas in separate migrations.

* When creating Include rules in FULL job mode, only schema-level rules (objectName is .* and objectType is ALL) are allowed.

* If an Include rule has .* in objectName, no other rule for the same objectType is allowed. If the rule has ALL as objectType, no other rule for any type is allowed.

* The objectType ALL is only allowed for schema-level rules (objectName is .*).

* The objectType TABLE is not allowed for schema-level rules where the owner is specified (objectName is .* and owner is any pattern other than .*).

* Object-level rules (objectName is any pattern other than .*) can only be used for the following object types: DIRECTORY, FUNCTION, JOB, MATERIALIZED_VIEW, PACKAGE, PROCEDURE, TRIGGER, SEQUENCE, TABLE, ROLE, PROFILE (Restricted to job mode FULL), PROCOBJ.

All other objectTypes must be either included or excluded using the .* pattern in objectName, and in addition for exclude, the owner should be .*

* The RELOADOBJECTS parameter is only supported for online logical migration.
* SEQUENCE after migration is not advanced in the following cases: 
    * If the sequence name ends with _SEQ or _SHSEQ
    * If the sequence name starts with AQ$_ or ISEQ$$_
    * If the sequence is marked secondary = 'Y' in dba_objects table.



**Supported objectTypes for object-level filtering**

objectName can be chosen or can be .*

DIRECTORY

FUNCTION

JOB

MATERIALIZED_VIEW

PACKAGE

PROCEDURE

TRIGGER

SEQUENCE

TABLE

ROLE

PROFILE (Restricted to job mode FULL)

PROCOBJ

**Supported objectTypes for generic objectType-level exclude or include**

(objectName must be .*) 

CLUSTER

DB_LINK

GRANT

OBJECT_GRANT

SYSTEM_GRANT

ROLE_GRANT

PROCOBJ_GRANT

PROCDEPOBJ_GRANT

INDEX

INDEXTYPE

MATERIALIZED_VIEW_LOG

MATERIALIZED_ZONEMAP

POST_TABLE_ACTION

PROCOBJ

PROFILE

REF_CONSTRAINT

RLS_POLICY

SYNONYM

TABLESPACE

TABLESPACE_QUOTA

USER

XMLSCHEMA

VIEW

AUDIT_TRAILS

ON_USER_GRANT

**Filtering other objectTypes**

To filter other objectTypes that are NOT listed above like COMMENT, FGA_POLICY, and so on, use the `[DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXCLUDETYPELIST](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-7539C488-5707-433A-B825-EE98CA5E811B)` parameter. 

Examples

Example 5-1 Include all objects of schema MySchema

`INCLUDEOBJECTS-1=owner:MySchema, objectName:.*, objectType:ALL`

owner | objectName | objectType  
---|---|---  
MySchema | .* | ALL  

Example 5-2 Include all tables starting with PROD and procedure MYPROC of schema MySchema, including all dependent objects

`INCLUDEOBJECTS-1=owner:MySchema, objectName:PROD.*, objectType:TABLE`

`INCLUDEOBJECTS-2=owner:MySchema, objectName:MYPROC, objectType:PROCEDURE`

owner | objectName | objectType  
---|---|---  
MySchema | PROD.* | TABLE  
MySchema | MYPROC | PROCEDURE  

Example 5-3 Exclude schemas starting with Experimental, the table MySchema.OldTable (also excluding all dependent objects) and all objects of type DB_LINK

Note that MySchema.OldTable will not be excluded if a table called OldTable is present in a different schema that is also migrated.

`EXCLUDEOBJECTS-1=owner:Experimental.*, objectName:.*, objectType:ALL`

`EXCLUDEOBJECTS-2=owner:MySchema, objectName:OldTable, objectType:TABLE`

`EXCLUDEOBJECTS-3=owner:.*, objectName:.*, objectType:DB_LINK`

owner | objectName | objectType  
---|---|---  
Experimental.* | .* | ALL  
MySchema | OldTable | TABLE  
.* | .* | DB_LINK  

Specify Included and Excluded Objects with Special Characters

The following examples show you how to specify objects names that use special characters in the `EXCLUDEOBJECTS` and `INCLUDEOBJECTS` parameters. 

* To escape a special character, use two slashes (//) before and after all characters in the string before the special character.

For example, to escape dollar sign ($):

`**\\**INLUDEOBJECTS-3= owner:GRAF_MULTI**\\**$_HR`

* To match all characters in between prefix and suffix pattern, add a period and an asterisk (.*) where the matching should occur.

For example, to exclude all schemas starting with GRAF and ending with HR:

`EXCLUDEOBJECTS-3= owner:GRAF**.***HR`




#### Migrating Objects with Different Oracle GoldenGate Support Modes {#GUID-062D4125-7183-4357-98F7-6EE35BF83673}

You can migrate objects with different Oracle GoldenGate support modes.

> **note:** Zero Downtime Migration sets up Oracle GoldenGate replication only for `TABLE` objects. 

> **note:** Oracle GoldenGate cannot replicate materialized views without `UPDATE` clause. Also, materialized view and source table cannot be replicated at the same time. Accordingly, for an online logical migration job, ZDM queries `dba_mviews` in the source database for materialized views that meet the following conditions: 

* `UPDATABLE = 'N' OR MASTER_LINK = NULL`

OR

* `MASTER_LINK = `

ZDM sets a `TABLEEXCLUDE` parameter for GoldenGate Extract to avoid replicating the materialized view, for each materialized view identified using the above query. 

**Migrating Tables with support_mode = INTERNAL**

Zero Downtime Migration detects and notifies you about tables with support_mode = INTERNAL.

> **note:** Zero Downtime Migration 21.3 users must apply Patch 33509650: ZDM PATCH USING MOS to use this feature. 

**DML replication**: Oracle GoldenGate automatically ignores DML for tables with support_mode = INTERNAL. Zero Downtime Migration does not set GoldenGate Extract parameter `TABLEEXCLUDE` for tables with support_mode = INTERNAL. 

Oracle GoldenGate excludes DML replication for Global Temporary Tables.

**DDL replication**: If you set parameter `GOLDENGATESETTINGS_REPLICATEDDL = TRUE`, then Zero Downtime Migration sets Oracle GodenGate Extract parameter `DDLOPTIONS CAPTUREGLOBALTEMPTABLE`. 

**Migrating Tables with support_mode = ID KEY**

Under normal operation, Zero Downtime Migration reports an error listing all user tables with Oracle GoldenGate support_mode = ID KEY.

> **note:** Zero Downtime Migration 21.3 users must apply Patch 33509650: ZDM PATCH USING MOS to use this feature. 

If you set parameter `GOLDENGATESETTINGS_USEFLASHBACKQUERY = TRUE`, Zero Downtime Migration sets the following Oracle GoldenGate Extract parameters that allow ID KEY support mode objects to be replicated. 

* STATOPTIONS REPORTFETCH

* FETCHOPTIONS USESNAPSHOT, NOUSELATESTVERSION, MISSINGROW REPORT




Ensure that the source database has sufficient UNDO size to allow Oracle GoldenGate to use Flashback Query. For best fetch results, configure the source database as documented at [Setting Flashback Query](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oracle-db/preparing-database-oracle-goldengate.md#GUID-AE2D0BE3-F7C3-4A8C-B96C-0498AE71D244) in *Using Oracle GoldenGate with Oracle Database*. 

**Migrating Tables with support_mode = NONE and support_mode = PLSQL**

The ZDM_VALIDATE_SRC phase queries view `DBA_GOLDENGATE_SUPPORT_MODE` and reports list of tables with `support_mode = NONE` and `support_mode = PLSQL`. 

Set the following ZDM parameters to migrate such objects:

* `GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS`
* `RELOADOBJECTS-LIST_ELEMENT_NUMBER`



**Related Topics**

* [Handling Application Switchover in a Logical Migration](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-1455CCE0-A583-452B-8FA0-5B5F343EFE17)
* [GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-815ACFB2-E654-4857-909D-99AB1A4C3E01)
* [RELOADOBJECTS-LIST_ELEMENT_NUMBER](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-07CBC6C7-07E4-41C8-8D11-01D71ABC25C4)
* [DBA_GOLDENGATE_NOT_UNIQUE](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DBA_GOLDENGATE_NOT_UNIQUE.md#GUID-716FFDBA-2096-4D8B-B388-F4811011E0B1)



### Migrating METADATA {#GUID-2ED750F3-B9B7-4AD2-968D-A6E9DC0B8C21}

You can perform a phased migration using this feature. 

Migrating METADATA and DATA as separate phases in ZDM workflow has the following benefits: 

* Workflow performs regular EXPORT and a phased import that involves Pre-data Metadata Import which includes: 
    * USER and PROFILE creation
    * METADATA creation
    * DATA import
* This helps with inserting workflow customization in the workflow. 



If you specify a different GoldenGate schema name, then you will need to update the `GLOBALS` file. 

To import METADATA before DATA, set value of the [DATAPUMPSETTINGS_METADATAFIRST](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-FF276C20-4BD5-4613-8577-844D1283B0EC) parameter to `TRUE`. 

**Related Topics**

* [Zero Downtime Migration Process Phases](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-7D38CACC-DB50-475D-8193-E2C4055AFD68)



### Setting Advanced Data Pump Parameters {#GUID-B723C1D5-DE14-4A2E-B5EB-61AF8AE9273C}

Zero Downtime Migration automatically sets optimal defaults for Oracle Data Pump parameters to achieve better performance and ensure security of data. To further tune performance, change the export modes, or rename database objects, there are several Data Pump settings that you can configure in the migration response file.

These parameters are set in the response file at `$ZDM_HOME/rhp/zdm/template/zdm_logical_template.rsp`. 

#### Default Data Pump Parameter Settings for Zero Downtime Migration {#GUID-AE6D7CFD-47AE-4AAD-80AC-4850E60F2ED7}

The following table lists the Data Pump parameters set by Zero Downtime Migration, and the values they are set to. If there is a Zero Downtime Migration response file parameter available to override the default, it is listed in the Optional Zero Downtime Migration Response File Parameter to Override column. The override parameters are set in the response file at `$ZDM_HOME/rhp/zdm/template/zdm_logical_template.rsp`. 

**Table: Data Pump Parameter Defaults**

Data Pump Parameter | Default Value | Optional ZDM Response File Parameter to Override  
---|---|---  
EXCLUDE |  cluster (ADB-D, ADB-S)<br>indextype (ADW-S)<br>db_link (ADB)<br>statistics (User managed Target and ADB) |  Allows additional `EXCLUDE` entries to be specified. See [EXCLUDEOBJECTS-LIST_ELEMENT_NUMBER](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-1D3685DD-539E-4466-BF46-6CBD5F3CFD51) for information. **Note**Specifying invalid object types for `EXCLUDE` will lead to a Data Pump export error. Ensure that a valid object type is specified for the `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXCLUDETYPELIST` parameter. <br>To see a list of valid object types, query the following views: `DATABASE_EXPORT_OBJECTS` for `FULL` mode, `SCHEMA_EXPORT_OBJECTS` for `SCHEMA` mode, and `TABLE_EXPORT_OBJECTS` for `TABLE` and `TABLESPACE` mode. The values listed in the `OBJECT_PATH` column are the valid object types. <br>For example, specifying the invalid object type parameter in the response file will lead to export error. `ORA-39038: Object path "" is not supported for SCHEMA jobs.`  
PARALLEL  |  ZDM sets PARALLEL parameter by default as follows<br>For User managed DB :- (Sum of (2 x (no. of physical CPU) per node ) ) with Max 32 cap.<br>For ADB :- No. of OCPUs |  [DATAPUMPSETTINGS_DATAPUMPPARAMETERS_IMPORTPARALLELISMDEGREE](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-3558D915-2C04-40BA-83C3-E12539F2D8E5)<br>[DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXPORTPARALLELISMDEGREE](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-91741406-B08B-4B64-8FE1-6AD20A5B8772)  
CLUSTER |  ZDM always sets the Cluster mode as default  |  [DATAPUMPSETTINGS_DATAPUMPPARAMETERS_NOCLUSTER](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-68E385C1-C108-486B-ADC1-DA88ACA3532D)  
COMPRESSION |  COMPRESSION_ALGORITHM is set to BASIC(for 11.2) and MEDIUM (for 12.1+)<br>COMPRESSION is set to ALL |  N/A  
ENCRYPTION |  ENCRYPTION is set to ALL<br>ENCRYPTION_ALGORITHM is set to AES128 <br>ENCRYPTION_MODE is set to PASSWORD  |  N/A  
FILESIZE  |  FILESIZE is set to 5G  |  N/A  
FLASHBACK_SCN |  For OFFLINE_LOGICAL ZDM set FLASHBACK_TIME System time now.<br>For ONLINE LOGICAL ZDM uses neither FLASHBACK_SCN not FLASHBACK_TIME |  N/A  
REUSE_DUMPFILES |  Always set to YES |  N/A  
TRANSFORM  |  Always sets OMIT_ENCRYPTION_CLAUSE:Y for 19c+ targets<br>Always sets LOB_STORAGE:SECUREFILE<br>For ADB target, following transform is set by default<br>SEGMENT_ATTRIBUTES:N<br>DWCS_CVT_IOTS:Y<br>CONSTRAINT_USE_DEFAULT_INDEX:Y |  Allows additional TRANSFORM to be specified   
METRICS |  Always set to Yes |  N/A  
LOGTIME |  Always set to ALL |  N/A  
TRACE |  Always set to 1FF0b00  |  N/A  
LOGFILE |  Always set to Data Pump job name and created under specified export or import directory object.<br>Say if Data Pump job is ZDM_2_DP_EXPORT_8417 and directory object used is DATA_PUMP_DIR, then the operation log is created by name ZDM_2_DP_EXPORT_8417.log under DATA_PUMP_DIR. |  N/A  

#### Automatic Tablespace Creation {#GUID-C23B404A-9C3A-48A3-A265-D01E6774914F}

For logical migrations, Zero Downtime Migration automatically discovers the source database tablespaces associated with user schemas that are being migrated, and automatically create them in the target database before the Data Pump import phase.

> **note:** By default, all the tablespaces are automatically created. If you do not want to create a tablespace automatically, use the `TABLESPACEDETAILS_EXCLUDE` parameter and specify the tablespaces to be excluded from automatic creation at the target database. 

Zero Downtime Migration generates the DDL required to pre-create the tablespaces, creates the tablespaces on the target, and runs the generated DDL. 

Automatic tablespace creation is enabled by default for ADB-Dedicated and non-ADB targets. With automatic creation enabled, Zero Downtime Migration skips automatic creation for any tablespaces that are specified in the `REMAP` section in the response file, or that already exist in the target database. 

Zero Downtime Migration validates whether tablespace creation is supported on the given target. There are no limitations for co-managed database systems. If the target is an Autonomous Database system, the following limitations apply:

* Autonomous Database systems support only BIGFILE tablespaces, so Zero Downtime Migration enforces BIGFILE tablespace by default on Autonomous Database targets, and reports an error if SMALLFILE tablespaces are found. You can remap any SMALLFILE tablespaces instead.

* Autonomous Database Shared systems do not support the automatic creation of tablespaces. Automatic tablespace REMAP to DATA is applied by default.




Use the following response file parameters to automatically create the required tablespaces at target database.

* `TABLESPACEDETAILS_AUTOCREATE` is enabled by default for automatic tablespace creation. 

Note that if you set `TABLESPACEDETAILS_AUTOCREATE` to `FALSE`, then automatic tablespace remapping (`TABLESPACEDETAILS_AUTOREMAP`) is applied by default. If both `AUTOCREATE` and `AUTOREMAP` are set to `FALSE`, then none is applied. 

* `TABLESPACEDETAILS_USEBIGFILE` allows you to convert SMALLFILE tablespaces to BIGFILE tablespaces. Normally set to `FALSE` by default, Zero Downtime Migration enforces `TRUE` for Autonomous Database targets. 

* `TABLESPACEDETAILS_EXTENDSIZEMB` enables tablespaces to AUTOEXTEND to avoid extend errors, with a default NEXT EXTEND size of 512MB. 

* `TABLESPACEDETAILS_EXCLUDE` specifies tablespaces to be excluded from automatic creation at the target database during import of user schemas. By default 'SYSTEM', 'SYSAUX', 'USERS' tablespaces are excluded. 




> **note:** If the current usage is less than 100M, then a default size of 100M is set as the initial size. If current usage of tablespace is higher than 100M, then the actual current size is set as the initial size. `AUTOEXTEND` ensures that there is no maximum limit. 

For SMALLFILE, if the `incrementby` clause is 0 (zero), then the default value of 1 is applied to determine the `AUTOEXTEND ON NEXT` size. 

#### Automatic Tablespace Remap {#GUID-96E7666B-433F-4E83-BC0C-BE5B6E3FB9E2}

For logical migrations, Zero Downtime Migration can automatically remap tablespaces on the source database to a specified tablespace on the target database.

Zero Downtime Migration automatically discovers the source database tablespaces necessary for migration. With automatic remap enabled, Zero Downtime Migration discovers the source tablespaces that require remapping by excluding any tablespaces that meet the following conditions:

* Specified for remap in `DATAPUMPSETTINGS_METADATAREMAPS`

* Specified for exclude in `TABLESPACEDETAILS_EXCLUDE`

* Tablespaces with the same name that already exist on the target database




Use the following response file parameters to automatically remap the required tablespaces.

* `TABLESPACEDETAILS_AUTOREMAP` enables automatic tablespace remap. 

* `TABLESPACEDETAILS_REMAPTARGET` specifies the name of the tablespace on the target database to which to remap the tablespace on the source database. The default value is `DATA`. 




> **note:** Zero Downtime Migration sets AUTOREMAP to TRUE by default for ADB-Serverless targets. You can override this by setting `TABLESPACEDETAILS_AUTOREMAP=FALSE`. 

**Verifying Tablespace Remaps**

Run command `ZDMCLI migrate database` in evaluation mode (`-eval`) to ensure that all necessary tablespaces to be remapped are listed. If any tablespaces are missed, you remap them using the `DATAPUMPSETTINGS_METADATAREMAPS` parameter. 

> **note:** For a tablespace to be used as REMAP target, the user performing the import operation, for example, `SYSTEM`, should have some quota on the chosen tablespace. 

**Performance Considerations**

There is operational overhead involved in tablespace remapping that adds to the overall Data Pump import time. To optimize performance, review and drop unwanted tablespaces from the source database to minimize the number of remapped tablespaces. For more information, see the REMAP_* section in [What DataPump And Oracle RDBMS Parameters And Features Can Significantly Affect DataPump Performance ? (Doc ID 1611373.1)](https://support.oracle.com/rs?type=doc&id=1611373.1). 

**Remap of temporary Tablespaces**

* For Autonomous target database, ZDM remaps temporary tablespaces by default and remaps to TEMP by default.
* For non-Autonomous target database, opt for remapping temporary tablespaces and specify the target temporary tablespace to be remapped to, using the `TABLESPACEDETAILS_REMAPTEMPTARGET` parameter. 
* When you migrate to Oracle Autonomous Database Serverless in `FULL` mode, tablespaces are remapped to `DATA` by default. Tablespace creation is not allowed so, set the following parameter: `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXCLUDETYPELIST=TABLESPACE`



#### Metadata Remapping {#GUID-18740526-FC7C-4CC6-9BCD-3D7DEC48B768}

The `DATAPUMPSETTINGS_METADATAREMAPS*` parameter lets you rename database objects during a migration job. Specify the object to rename with **type**, then enter the **oldValue** and **newValue**. 

For example:

`DATAPUMPSETTINGS_METADATAREMAPS-1=type:REMAP_TABLESPACE,oldValue:TS_DATA_X,newValue:DATA`

`DATAPUMPSETTINGS_METADATAREMAPS-2=type:REMAP_TABLESPACE,oldValue:DBS,newValue:DATA`

Supported object types are REMAP_DATAFILE, REMAP_SCHEMA, REMAP_TABLE, and REMAP_TABLESPACE.

Quota grants for individual users to tablespaces are not remapped, so you must manually create these grants for tablespace DATA.

When migrating to an Autonomous Database Shared Infrastructure target, all tablespaces are automatically mapped to DATA. You can override this by explicitly mapping tablespaces to a different target in the response file.

See [METADATA_REMAP Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-0FC32790-91E6-4781-87A3-229DE024CB3D) for more information. 

#### Data Pump Error Handling {#GUID-A02592A2-6955-4337-84A5-051A70D0E9F7}

Some errors are ignored by Zero Downtime Migration. You must review any remaining errors appearing in the Data Pump log.

The following Data Pump errors are ignored by Zero Downtime Migration.

* ORA-31684: XXXX already exists

* ORA-39111: Dependent object type XXXX skipped, base object type

* ORA-39082: Object type ALTER_PROCEDURE: XXXX created with compilation warnings




Ensure that you clear all Cloud Premigration Advisor Tool (CPAT) reported errors to avoid any underlying Data Pump errors. 

### Migrating Schemas in Parallel Using Batches {#GUID-2D9E3EF0-216F-4113-AAD7-B3984C094115}

Zero Downtime Migration lets you run multiple schema migrations as batches in parallel.

You can specify schema batches that need to be migrated in parallel. Determine groups of dependent schemas and specify all dependent schemas as part of same batch. 

The migration work flow handles the schemas as batches and runs Data Pump export, dump upload, and import operations in parallel for each batch specified.

Any error that occurs in one batch does not affect the other batch operations.

A progress monitor identifies the progress of each batch progress, reports progress individually, and errors specific to a batch are reported at the completion of all batch Data Pump Import operations.

Zero Downtime Migration also supports random batch selection by batch count, where the identified user schemas to be migrated are grouped randomly by the specified number of batches and migrated in parallel.

A new migration job phase `ZDM_PARALLEL_EXPORT_IMPORT` is cumulative of `ZDM_DATAPUMP_EXPORT_SRC`, `ZDM_UPLOAD_DUMPS_SRC`, and `ZDM_DATAPUMP_IMPORT_TGT` phases, and it handles all three actions for an identified sublist of schemas per thread, that is, export, transfer, and import the sublist of schemas in parallel. 

To configure batch mode:

In the RSP, set `DATAPUMPSETTINGS_JOBMODE` parameter to `SCHEMA`. 

The batch mode of operation is not enabled by default; you must enable it by setting one of the following RSP parameters:

* `DATAPUMPSETTINGS_SCHEMABATCHCOUNT=*`integer`*` indicates how many schemas to include in each batch. 

In this case Zero Downtime Migration does not guarantee the set of schemas per batch (the Data Pump job).

* `DATAPUMPSETTINGS_SCHEMABATCH-n`, where n cam be -1, -2, 03, lets you specify a list schemas to place in a batch for parallel handling. 




Note that `DATAPUMPSETTINGS_SCHEMABATCHCOUNT` and `DATAPUMPSETTINGS_SCHEMABATCH` are mutually exclusive. 

Also, note that database initialization parameter `MAX_DATAPUMP_JOBS_PER_PDB` determines the maximum number of concurrent Oracle Data Pump jobs for each PDB. 

Restrictions

`DATAPUMPSETTINGS_JOBMODE` value other than `SCHEMA` is prohibited if a batch of schemas is specified. 

Specifying `INCLUDEOBJECTS` is prohibited if batch of schemas is specified. 

No SSH Source and parallel migration is not allowed - the job must be paused for manual upload of dumps that defeats the purpose exporting schemas in parallel.

`MAX_DATAPUMP_JOBS_PER_PDB` determines the maximum number of concurrent Oracle Data Pump jobs per PDB. Exceeding this limit causes ORA-39391: maximum number of Data Pump jobs (n) exceeded. 

### Migrating from Amazon Web Services RDS Oracle to Oracle Cloud {#GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA}

You can migrate an Oracle Database from Amazon Web Services (AWS) RDS to Oracle Autonomous Database (ADB), OCI user-managed databases, and Oracle Exadata Database Service on Cloud@Customer using the Zero Downtime Migration logical migration method (online and offline).

#### Setting Amazon as the Source Environment {#GUID-6C78BB87-F0B6-4621-B6E0-17464A3CA0DE}

In the Zero Downtime Migration logical migration response file, set the following parameters to migrate Oracle databases from Amazon Web Services:

`SOURCEDATABASE_ENVIRONMENT_NAME=AMAZON`

`SOURCEDATABASE_ENVIRONMENT_DBTYPE=RDS_ORACLE`

#### Configure Secure Connections {#GUID-A9FA05A2-F86E-41C5-A2B0-30E6CE2D7F2A}

Ensure that the subnet Amazon RDS security policy allows connections from Zero Downtime Migration to the DB instance on the specified secure port. See the AWS documentation for details:

[Scenarios for accessing a DB instance in a VPC](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.Scenarios.md)[Scenarios for accessing a DB instance not in a VPC](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.Scenarios.NotInVPC.md)

**Setting Endpoint Information**

1. Find the Amazon RDS Oracle Instance endpoint (DNS name) and port number in the RDS console DB Instance **Connectivity & security** tab. 

See [Finding the endpoint of your Oracle DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToOracleInstance.md#USER_Endpoint) for detailed help. 

2. Specify the endpoint and port number information in following Zero Downtime Migration logical response file parameters:

`SOURCEDATABASE_ADMINUSERNAME`

`SOURCEDATABASE_CONNECTIONDETAILS_HOST`

`SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME`

`SOURCEDATABASE_CONNECTIONDETAILS_PORT`

3. If connecting to Amazon RDS from the Zero Downtime Migration service host requires a proxy, then specify these logical response file parameters:

`SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME`

`SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT`




**Allowing Zero Downtime Migration to connect to Amazon RDS Oracle DB instance using SSL/TLS**

1. Enable Secure Socket Layer (SSL) or Transport Layer Security (TLS) in the Amazon RDS Oracle Instance to secure the connection from Zero Downtime Migration to Amazon RDS Oracle Instance. See [Encrypting client connections with SSL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Concepts.SSL.md) for details. 

2. Create an orapki wallet as detailed in [Updating applications to use new SSL/TLS certificates](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/ssl-certificate-rotation-oracle.md). 

3. Set the following parameters in the Zero Downtime Migration logical response file:

`SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME`

`SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_CREDENTIALSLOCATION`




#### Configuring the Data Transfer Method {#GUID-FA208E13-0B91-4CEB-820F-6F53811FEA6B}

To transfer the data from AWS, you have the following options:

* Amazon Simple Storage Service (Amazon S3) Bucket
* Database link (DBLINK)



##### Setting Up Database Link Transfer Method {#GUID-54C9DF90-666A-4F6C-B975-36B0B24C2B86}

To use a database link (DBLINK) to migrate Amazon RDS Oracle Database schema to Oracle Autonomous Database (ADB), you must have direct network connectivity between the Amazon RDS Oracle instance and the ADB target.

Set the following parameters in the Zero Downtime Migration logical response file.

1. Set the parameter `DATATRANSFERMEDIUM` to `DBLINK`. 

2. Set the parameter `SOURCEDATABASE_ENVIRONMENT_*` as shown in [Setting Amazon as a Source Environment](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-6C78BB87-F0B6-4621-B6E0-17464A3CA0DE). 




##### Setting Up S3 Bucket Data Transfer Medium {#GUID-2514A61A-031F-40D0-B229-14699A1A782F}

Zero Downtime Migration performs the following steps to migrate an Amazon RDS Oracle Database schema to Oracle Autonomous Database target using an S3 bucket:

* **Export dumps** \- Invoke the `DBMS_DATAPUMP` procedure in the Amazon RDS Oracle Database instance to generate dumps in `DATA_PUMP_DIR`. 

* **Upload dumps to S3 using RDSADMIN ** – Upload dumps from the RDS instance to the specified S3 bucket. 

* **Import dumps from S3 to ADB **– Import the schema to the ADB instance from the S3 bucket. 




To configure the environments to use the S3 bucket for data transfer, complete the following tasks.

**Complete AWS RDS Prerequisites**

The RDS Oracle instance must have been integrated with S3 and the DB user should be allowed to upload the dumps to S3 bucket via RDSADMIN package. 

1. **Integrate RDS and S3** \- Ensure that the RDS instance and S3 are integrated. 

Enable RDS and S3 integration for the Oracle Database instance if not configured already.

See [Amazon S3 integration](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/oracle-s3-integration.md) for the detailed steps. 

2. **S3 Access Key** \- Create and provide Zero Downtime Migration with the AWS S3 Access Key and Access secret. 

See [AWS Account and Access Keys](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-appendix-sign-up.md) for the detailed steps. 

3. **S3 and RDS region** \- Ensure that the S3 bucket and RDS Oracle Database instance are in the same region, for example us-east-2. 




**Set Required Response File Parameters**

Set the following parameters to perform a migration using the S3 bucket:

1. Set the parameter `DATATRANSFERMEDIUM` to `AMAZONS3`. 

2. Set the `SOURCEDATABASE_ENVIRONMENT_*` as shown in [Setting Amazon as a Source Environment](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-6C78BB87-F0B6-4621-B6E0-17464A3CA0DE). 

3. Set the following additional parameters:

`DUMPTRANSFERDETAILS_S3BUCKET_NAME=`

`DUMPTRANSFERDETAILS_S3BUCKET_REGION=`

`DUMPTRANSFERDETAILS_S3BUCKET_ACCESSKEY=`

`DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_NAME=`




### Migrating to Oracle Autonomous Database on Exadata Cloud@Customer {#GUID-24C15009-A984-494F-9239-583D14248079}

Zero Downtime Migration supports migrations to Oracle Autonomous Database on Exadata Cloud@Customer from any on-premises Oracle Database, including existing Exadata Cloud@Customer systems, using the logical migration method and NFS as a data transfer medium.

Supported Use Cases

The following migration scenarios are supported by Zero Downtime Migration:

* Oracle Exadata Database Service on Cloud@Customer (Gen 1 or Gen 2) source to Oracle Autonomous Database on Exadata Cloud@Customer target (given that the source and target databases have the same standard UID/GID for the Oracle user)

* On-premises Oracle Database source to Oracle Autonomous Database on Exadata Cloud@Customer target (given that the source database has a non-standard UID/GID for the Oracle user)




Migration Parameters

In addition to the required source and target connection parameters, set the following in the logical migration response file:

`MIGRATION_METHOD=OFFLINE_LOGICAL` or `ONLINE_LOGICAL`

`DATA_TRANSFER_MEDIUM=NFS`

Source Prerequisites

In addition to the usual source database prerequisites documented in [Source Database Prerequisites for Logical Migration](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-004B4498-49B9-45A8-BD1B-5449857139A0), you must also set up access to the Data Pump dump directory as detailed in the procedures below. 

Prerequisite Setup for Exadata Cloud@Customer Environments

1. In all Oracle RAC nodes:
```
[root@onprem ~]# cat /etc/fstab | grep nfsshare
nas-server.us.com:/scratch/nfsshare /u02/app/oracle/mount nfs defaults 0 0
[root@onprem ~]#
```


2. On the Autonomous Database target, mount the path 

`nas-server.us.com:/scratch/nfsshare`

to the Exadata infrastructure resource, giving you *`specified_mount_path`*/*`CDB`*/*`PDB_GUID`* 

For example:

/scratch/nfsshare/*`CDB`*/*`PDB_GUID`*

For information about the option to mount NFS, contact support for details.

3. On the source PDB, run the following:
```
SQL> create or replace directory DATA_PUMP_DIR_ADBCC as '/u02/app/oracle/mount/CDB/PDB_GUID';

Directory created.

SQL> select grantee from all_tab_privs where table_name = 'DATA_PUMP_DIR_ADBCC';

no rows selected

SQL> grant read, write on directory DATA_PUMP_DIR_ADBCC to SYSTEM;

Grant succeeded.
```


4. On the source, mount point permissions expected (`drwxr-x---`) 
```
[oracle@onprem opc]$ ls -ldrt /u02/app/oracle/mount/CDB/PDB_GUID
drwxr-x--- 2 oracle asmadmin 4096 Jul 12 11:34 /u02/app/oracle/mount/CDB/PDB_GUID
[oracle@onprem opc]$
```





Prerequisite Setup for On-Premises Environments

1. In all Oracle RAC nodes:
```
[root@onprem ~]# cat /etc/fstab | grep nfsshare
nas-server.us.com:/scratch/nfsshare /u02/app/oracle/mount nfs defaults 0 0
[root@onprem ~]#
```


2. Create a group with GID 1001 - miggrp
```
root> groupadd -g 1001 miggrp
```


3. Add the database user to this group.
```
root> usermod -aG migrp oracle
```


4. On the Autonomous Database target, mount the NFS share (Group should get `rwx`) 

`nas-server.us.com:/scratch/nfsshare`

to the Exadata infrastructure resource, giving you *`specified_mount_path`*/*`CDB`*/*`PDB_GUID`* 

For example:

/scratch/nfsshare/*`CDB`*/*`PDB_GUID`*

For information about the option to mount NFS, contact support for details.

5. Ensure that the directory is writable.

Touch `*`specified_mount_path`*/*`CDB`*/*`PDB_GUID`*/test.txt`

6. In the source PDB, run the following:
```
SQL> create or replace directory DATA_PUMP_DIR_ADBCC as '/u02/app/oracle/mount/CDB/PDB_GUID';

Directory created.

SQL> select grantee from all_tab_privs where table_name = 'DATA_PUMP_DIR_ADBCC';

no rows selected

SQL> grant read, write on directory DATA_PUMP_DIR_ADBCC to SYSTEM;

Grant succeeded.
```


7. On the source, mount point permission expected (`drwxrwx—`), and the group should match the migration dummy group created. 
```
[oracle@onprem opc]$ ls -ldrt /u02/app/oracle/mount/CDB/PDB_GUID
drwxrwx--- 2 1001 asmadmin 4096 Jul 12 11:34 /u02/app/oracle/mount/CDB/PDB_GUID
[oracle@onprem opc]$
```





**Prerequisites for performing online logical migration**: 

If the online logical migration involves Oracle GoldenGate on docker, set up the target Autonomous Database wallet in Oracle GoldenGate on docker. See, [Additional Logical Migration Prerequisites](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-1D392BDA-4D82-49FC-A5E4-C23398A81A19) . 

> **note:** If there is no connectivity to OCI endpoints,then specify the target database type in the `TARGETDATABASE_DBTYPE` parameter and skip the following parameters: 

* `TARGETDATABASE_OCID`
* `OCIAUTHENTICATIONDETAILS_* `



**Migrating to Oracle Autonomous Database on Exadata Cloud@Customer without OCI access granted to ZDM Host**: 

Follow the steps for performing a logical migration from on-premises Oracle Database source to Oracle Autonomous Database on Exadata Cloud@Customer without OCI access granted to the ZDM host: 

> **note:** When the OCI access is not available, then do not specify the OCI Authentication details (`OCIAUTHENTICATIONDETAILS_*`) and Target database OCID (`TARGETDATABASE_OCID`). 

* Set the following parameters:
```
TARGETDATABASE_DBTYPE=ADBCC
TARGETDATABASE_ADMINUSERNAME=<User performing import. For example, admin>
TARGETDATABASE_CONNECTIONDETAILS_HOST=<>
TARGETDATABASE_CONNECTIONDETAILS_SERVICENAME=<ADB service alias to be used to connect. For example, HIGH or
HIGH_TLS>
```


* If proxy is required to access the Oracle Autonomous Database on Exadata Cloud@Customer database, then specify the parameters as follows:
```
TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME=<>
TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT=<Port to be used>
```


* If the access to target is over TLS, then specify the parameters as follows:
```
TARGETDATABASE_CONNECTIONDETAILS_TLSDETAILS_*
```





**Related Topics**

* [TARGETDATABASE_DBTYPE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C97C3B81-02E0-4377-9323-E64A0B760844)
* [TARGETDATABASE_OCID](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-45C25BCD-438C-4A15-8326-1B22CCE38450)
* [OCIAUTHENTICATIONDETAILS_REGIONID](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-66AA3CC6-6B06-4A85-92CB-9D02EB469960)



### Migrating to Co-Managed Database Server with NFS Data Transfer Medium {#GUID-C62F892E-A45E-4CE4-90C7-3955BBD355F5}

The dumps in NFS mounted path are not readable for target database user unless the Unique Identifiers (UIDs) for the source database user matches with the target database user.

Complete the following prerequisites on the source database to prepare for a logical migration.

The following scenarios are possible: 

* If the IDs match, dumps are readable by default and no action is required.
* If the IDs do not match, ZDM automatically discovers the primary group of the target database user and changes the group of the dump to the primary group of the target database user. 

    > **note:** Currently, this option is not supported in IBM AIX. 

    * To perform this action, the source database user in the database server must be a part of a group having the same group ID as that of the target database user's primary group. 
    * If there is no group available, then create an auxiliary group on the source database server with GID matching to the GID of target database user. 
    * Make the source database user to be a part of the auxiliary group. 
    
    > **note:** As an example, refer to Step 1 through Step 3 of *Prerequisite Setup for Exadata Cloud@Customer Environments* topic in [Migrating to Oracle Autonomous Database on Exadata Cloud@Customer](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-24C15009-A984-494F-9239-583D14248079). 

* However, if GID is not available and the above two conditions cannot be met, as an option you can set value of the [DUMPTRANSFERDETAILS_PUBLICREAD](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-53C3274C-163F-4208-B862-C8D80281D2C5) to `TRUE` to make the dump files on NFS readable for others. 



> **note:** Any user having access to the network storage can read the Data Pump dumps if value of DUMPTRANSFERDETAILS_PUBLICREAD is `TRUE`. 

### Migrating to Autonomous Database Server with File Storage Service as the Data Transfer Medium {#GUID-CAD6E7BD-CE49-4BF1-BE8A-BC881B2BAA16}

ZDM allows to leverage Oracle Cloud Infrastructure File Storage Service (FSS) as the data transfer medium for physical and logical migration. 

* The use of FSS in logical migration avoids OSS upload or download of Datapump dumps. 
* ZDM attaches the FSS to ADB automatically part of the migration workflow. 
* You must ensure that the file storage service is accessible over the NFS protocol in all the source database servers. ADB allows ingress and egress traffic for the known NFS ports. 
* You can use File system as the supported data transfer medium for Datapump.



Complete the following prerequisites on the source database to prepare for a logical migration.

1. Ensure that network access exists between OCI and on-premises database. 

FastConnect or a Site-to-Site VPN are the options to connect to the on-premises data center to access data in an Autonomous Database from the file systems in an on-premises data center . See [FastConnect](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/fastconnect.htm) and [Site-to-Site VPN](https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/overviewIPsec.htm) for more information. 

2. Create a file system in OCI FSS. See [Creating File Systems](https://docs.oracle.com/en-us/iaas/Content/File/Tasks/creatingfilesystems.htm) for more information. 

> **note:** Step1 and step 2 do not apply for Autonomous Database on Exadata Cloud@Customer. 

3. For Dedicated infrastructure or for ADB database in private subnet, add Virtual Cloud Network (VCN) security rules for OCI File Storage Service (FSS) access in ADB. See [External File Storage Requirements](https://docs.oracle.com/en/cloud/paas/autonomous-database/dedicated/defsd/index.md#GUID-0F7DCFAD-4FA9-437B-A7C4-E416A8EC8179). 
4. Obtain the FQDN or IP address for the mount target: See [To view details of a mount target](https://docs.oracle.com/en-us/iaas/Content/File/Tasks/managingmounttargets.htm#Using_the_Console) to get its FQDN. 
5. Mount the exported filesystem in all Source Database server host and map to Data Pump export directory object path or specify the mounted path and a non-existent export directory object name for ZDM to create directory object in source database to be exported.
6. Enable the private endpoints for the Autonomous database for attaching it to a private File Storage service. 

    For more information, see:

    * [OCI File Storage Service](https://docs.oracle.com/en-us/iaas/Content/File/Concepts/filestorageoverview.htm)
    * [Configuring Network Access with Private Endpoints](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/private-endpoints-autonomous.md#GUID-60FE6BFD-B05C-4C97-8B4A-83285F31D575
    * See usage notes in [Summary of DBMS_CLOUD_ADMIN Subprograms](https://docs.oracle.com/en/cloud/paas/autonomous-database/serverless/adbsb/dbms-cloud-admin.md#GUID-D76B229E-781E-45C0-9F14-CAF30F9E6E3B). 



To migrate to ADB using FSS as the data transfer medium: 

* Specify the Data transfer medium for datapump dump transfer as NFS or FSS based on the type of file system used. Specify the following parameters: 
    * `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_NAME= `
    
    * `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_PATH`= 
    * `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME`= 
    * `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_PATH`= < to be left blank >
* Specify `DATA_TRANSFER_MEDIUM=NFS` for migrating to ADB target. 
* Specify `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME`=  Default: DATA_PUMP_DIR> that mounts the FSS to the target. For ADB, ZDM automounts FSS to this specified Directory Name. You need to mount FSS to all target database hosts in case of non ADB targets. 
* You must specify FSS or NFS host IP address and FSS mount point for migrating to ADB database if the` DATA_TRANSFER_MEDIUM` chosen is NFS. ZDM mounts the FSS in your Autonomous Database. 

You need to configure NFS/FSS and specify the mount points for ADBS and ADBD for data ingestion/data migration (Datapump Dumps staging). For ADB target ADB access you should be ADMIN user or a user with EXECUTE privilege on `DBMS_CLOUD_ADMIN`. 

* Set the following optional logical parameters: 
    * `DUMPTRANSFERDETAILS_SHAREDSTORAGE_NAME`= 
    * `DUMPTRANSFERDETAILS_SHAREDSTORAGE_HOST`= 
    * `DUMPTRANSFERDETAILS_SHAREDSTORAGE_PATH`= 

    The above parameter are required to specified for mounting NFS on ADB. AutoMounting of NFS in source or non-ADB is not supported as it needs root access.
    
    The AutoMount feature checks:

    * skip mount action - if the DATA_PUMP_DIR objects specified in `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME` is already mounted to the same storage. 
    * error - if the DATA_PUMP_DIR objects specified in `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME` is already mapped to a different path other than that specified in `DUMPTRANSFERDETAILS_SHAREDSTORAGE_PATH`. 



### Logical Migration for an Autonomous Database {#GUID-61BAE97C-6838-4E3D-882B-8E5828D68858}

You can perform a logical migration from an Autonomous Database to an Autonomous Database via DBLINK or OSS. 

ZDM supports both online and offline logical migration from an ADB (Oracle Autonomous Database Serverless (ADB-S) /Oracle Autonomous Database (ADB-D) source. 

* For online logical migration place the wallets for the source and target databases in location `/u02/deployments/Marketplace/etc` . 
* Create directory with name `Wallet_` for the wallets for the target databases. 
* You can either provide ADB connection details with the existing SOURCEDATABASE_CONNECTIONDETAILS properties or use a new response file parameter `SOURCEDATABASE_OCID`. 
* For source, place the wallet directly in `/u02/deployments/Marketplace/etc/ dir`. 
* Moreover, `dbname` case must match with the case found in the ` 'SELECT * from GLOBAL_NAME' query` output. 



> **note:** The value of `SOURCEDATABASE_ENVIRONMENT_DBTYPE` must be ADB-D/ADB-S. 

### Configuring Per-PDB Extract Using Oracle GoldenGate 21c {#GUID-22551640-4D0D-4329-AC9D-7AD403B520A8}

ZDM configures PDB extract for the following use cases: 

* On-premises Oracle PDB of version 21c or higher. 
* OCI Autonomous Database of version 19c or higher. 
* OCI non-Autonomous PDB of version 21c or higher.
* AWS RDS Oracle PDB of version 21c or higher. 



> **note:** Oracle GoldenGate 21c and higher versions support the PDB Extract feature. 

To create and register the Extract specific to a PDB, connect to the local `ggadmin` user in a specific pluggable database. 
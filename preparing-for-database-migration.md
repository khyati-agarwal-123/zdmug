## 4Preparing for a Physical Database Migration {#GUID-46459904-7B68-4B7B-8EC6-C66423CEB6B9}

Before starting a Zero Downtime Migration physical database migration you must, prepare the source and target databases, set parameters in the response file, and configure any required migration job customization. Configure connectivity between the source to target databases for online physical migration. 

> **note:** The hostname must be different in both source and target, if the hostname is the same, the migration will fail. 

See the [Zero Downtime Migration Release Notes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMRN-GUID-A1A467DC-FC06-4409-AF7F-BF0186CD8C54) for the latest information about new features, known issues, and My Oracle Support notes. 

### Prerequisites for Online Physical Migration {#GUID-011F9D78-E9FE-4FB6-B094-BED844C0F9E2}

You must configure connectivity prerequisites for online physical migration.If connectivity using SCAN and the SCAN port is not possible between the source and target database servers, set up an SSH tunnel from the source database server to the target database server.

**Related Topics**

* [ Set up an SSH Tunnel](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-808F9EBE-036E-48CF-B55F-396C3681754E)



### Preparing the Source and Target Databases {#GUID-E8A2DD5F-B447-465F-BE2E-55E79FFD092D}

There are several tasks you must complete on the source and target databases before configuring a migration job. The following are prerequisites that apply to both source and target database.

* Zero Downtime Migration online physical migrations leverage Oracle Data Guard, so you must have the same operating system and database version on both source and target.

* The Zero Downtime Migration physical migration work flow does not support cross-edition migration; however, you can migrate Standard Edition to Enterprise Edition using the logical migration work flow.

* The character set on the source database must be the same as the target database.

* The source and target databases must use a server parameter file (SPFILE).

* ZDM supports the source databases that are not using SPFILE for offline migration. For online migration, you must run the source database with SPFILE. 
* System time of the Zero Downtime Migration service host and source database server should be in sync with your Oracle Cloud Infrastructure target.
If the time on any of these systems varies beyond 6 minutes from the time on OCI, it should be adjusted. You can use `ntp time check` to synchronize the time if NTP is configured. If NTP is not configured, then it is recommended that you configure it. If configuring NTP is not an option, then you need to correct the time manually to ensure it is in sync with OCI time. 

* Set the `COMPATIBLE` database initialization parameter to the same value on the source and target database. See [Values for the COMPATIBLE Initialization Parameter in Oracle Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=UPGRD-GUID-1146B7A7-81EE-4F90-8A0A-8440A6618CD9) for valid values. 

* Ensure that both source and target SQLNET.ORA have the same encryption algorithm.

* Ensure that the target database time zone is same as the source or higher. If the source database time zone version is lower than the target database, then post migration, you must upgrade the database timezone. Time zone upgrade will encounter downtime when you adjust the time zone and this involves database to be restarted twice. The downtime involved in the time zone upgrade depends on the amount of data requiring change. Preferably, match the time zone version of the source and the target database to avoid time zone upgrade involving extended downtime. However, ZDM automates upgrading time zone for physical migration as a part of workflow. See [ZDM_TGT_UPGRADE_TIMEZONE_FILE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-A407807B-161B-4A45-A871-193154F95410) . 
* ZDM enables backup compression and encryption to be turned off. You can turn off the encryption when migrating between on-premises databases only. Encryption is mandatory when migrating to cloud targets. See [ZDM_RMAN_ENCRYPT_BACKUP](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-933E0659-62A3-4693-BBD6-49708576EFCB). 
* You can configure the section size to be used by RMAN. See [ZDM_RMAN_SECTION_SIZE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-FE0DE57E-EACE-479F-9B88-253134522EAF). 
* Ensure that `/tmp` is mounted with `execute` permission as a prerequisite for the source and target databases if you want to perform a physical migration. 



### Source Database Prerequisites {#GUID-25B07C59-8143-41CB-B431-3D9225CCFDD6}

Meet the following prerequisites on the source database before the Zero Downtime Migration process starts.

* **Set archiving mode**

    source database must be running in `ARCHIVELOG` mode. See [Changing the Database Archiving Mode](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ADMIN-GUID-C12EA833-4717-430A-8919-5AEA747087B9). 

* **Configure the TDE wallet** on Oracle Database 12c Release 2 and later. 

    Oracle Database 12c Release 2 and later, if the source database does not have Transparent Data Encryption (TDE) enabled, then it is mandatory that you configure the TDE wallet before migration begins. You need not encrypt the data in the source database; the data is encrypted at target using the wallet setup in the source database. The `WALLET_TYPE` can be `AUTOLOGIN` (preferred) or `PASSWORD` based. 

    re that the wallet `STATUS` is `OPEN` and `WALLET_TYPE` is `AUTOLOGIN` (For an `AUTOLOGIN` wallet type), or `WALLET_TYPE` is `PASSWORD` (For a `PASSWORD` based wallet type). For a multitenant database, ensure that the wallet is open on all PDBs as well as the CDB, and the master key is set for all PDBs and the CDB. 
    ```
    SQL> SELECT * FROM v$encryption_wallet;
    ```


    ling TDE on Oracle Database 11g Release 2 (11.2.0.4) and Oracle Database 12c Release 1 is optional.

* **For Oracle RAC:**

    If the source is an Oracle RAC database, and `SNAPSHOT CONTROLFILE` is not on a shared location, configure `SNAPSHOT CONTROLFILE` to point to a shared location on all Oracle RAC nodes to avoid the ORA-00245 error during backups to Oracle Object Store. 
    
    For example, if the database is deployed on ASM storage,
    ```
    $ rman target /
    RMAN> CONFIGURE SNAPSHOT CONTROLFILE NAME TO '+DATA/db_name/snapcf_db_name.f';
    ```
    
    
    If the database is deployed on an ACFS file system, specify the shared ACFS location in the above command.

    * **Check port connections**
    
        * Verify that port 22 on the source database server allows incoming connections from the Zero Downtime Migration service host.
        
        * Ensure that the scan listener ports (1521, for example) on the source database servers allow incoming connections from the target database servers and outgoing connections to the target database servers.
        
            Alternate SQL connectivity should be made available if a firewall blocks incoming remote connection using the SCAN listener port.

    * **Maintain RMAN backup strategy**

        To preserve the source database Recovery Time Objective (RTO) and Recovery Point Objective (RPO) during the migration, the existing RMAN backup strategy should be maintained.
        
        During the migration a dual backup strategy will be in place; the existing backup strategy and the strategy used by Zero Downtime Migration. 
        
        Avoid having two RMAN backup jobs running simultaneously (the existing one and the one initiated by Zero Downtime Migration). 
        
        If archive logs were to be deleted on the source database, and these archive logs are needed by Zero Downtime Migration to synchronize the target cloud database, then these files should be restored so that Zero Downtime Migration can continue the migration process.

    * **Configure RMAN to automatically back up to control file**
    
        If RMAN is not already configured to automatically back up the control file and SPFILE, then set `CONFIGURE CONTROLFILE AUTOBACKUP` to `ON` and revert the setting back to `OFF` after migration is complete. 
        ```
        RMAN> CONFIGURE CONTROLFILE AUTOBACKUP ON;
        ```
    
    
    * **Register with SRVCTL**
    
        If the source database is deployed using Oracle Grid Infrastructure and the database is not registered using SRVCTL, then you must register the database before the migration.
    
    * **For offline migrations**
    
        Plan to make sure no incoming transactions take place on the source database before the `ZDM_BACKUP_DIFFERENTIAL_SRC` phase, so that there is no loss of data during the migration. 
        
        Once Zero Downtime Migration starts generating backups and transfers them, any new transactions on the source won't be part of the backups and therefore the target in the cloud won't have those changes.
    
    * **Handling of DB_nK_CACHE_SIZE**
    
        Zero Downtime Migration automates the copy of [DB_nK_CACHE_SIZE](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DB_nK_CACHE_SIZE.md) values from the source `init` file to the target database. This automation benefits migrations where the source database uses any tablespace of variable block size other than `db_block_size`. In such cases, without Zero Downtime Migration handling of `DB_nK_CACHE_SIZE`, the restore process would fail. 

        > **note:** For migrations where non-CDB to PDB conversion takes place, you must set this value in the actual target CDB in which the source will be plugged in. 




### Target Database Prerequisites {#GUID-FD1B3A13-8723-4DFE-8776-B57CAC0FA38B}

You must create a placeholder target database before beginning a migration to the target environment. The following prerequisites must be met on the target database before you begin the Zero Downtime Migration process.

The placeholder target database is overwritten during migration, but it retains the overall configuration.

* For this release, only Grid Infrastructure-based database services are supported as targets. For example, an LVM-based instance or an instance created in a node without Grid Infrastructure are not supported targets.

* For the Oracle Exadata Database Service on Dedicated Infrastructure and Oracle Exadata Database Service on Cloud@Customer targets, the placeholder database must be created using the Control Plane, not Grid Infrastructure Database Services before database migration begins.

* **Size for the future**

    When you create the database from the console, ensure that your chosen shape can accommodate the source database, plus any future sizing requirements. A good guideline is to use a shape similar to or larger in size than source database.

    * **Set name parameters**
    
        * `DB_NAME`
    
        If the target database is Oracle Exadata Database Service on Dedicated Infrastructure or Oracle Exadata Database Service on Cloud@Customer, then the database `DB_NAME` should be the same as the source database `DB_NAME`. 
    
        If the target database is Oracle Cloud Infrastructure, then the database `DB_NAME` can be the same as or different from the source database `DB_NAME`. 
    
        * `DB_UNIQUE_NAME`
    
        If the target database is Oracle Cloud Infrastructure, Oracle Exadata Database Service on Dedicated Infrastructure, or Oracle Exadata Database Service on Cloud@Customer, the target database `DB_UNIQUE_NAME` parameter value must be unique to ensure that Oracle Data Guard can identify the target as a different database from the source database. 
    
        > **note:** Ensure that the name for source and target database are same and they also have the same letter case, lowercase or uppercase. 
    
    * **Match the source SYS password**
    
        Specify a `SYS` password that matches that of the source database. 
    
    * **Disable automatic backups**
    
        Provision the target database from the console without enabling automatic backups. 
    
        For Oracle Cloud Infrastructure and Oracle Exadata Database Service on Dedicated Infrastructure, do not select the **Enable automatic backups** option under the section **Configure database backups**. 
    
        For Oracle Exadata Database Service on Cloud@Customer, set Backup destination **Type** to `None` under the section **Configure Backups**. 
    
    * **Verify patch level**
    
        The target database version should be the same as the source database version. The target database patch level should also be the same as (or higher than) the source database.
    
        If the target database environment is at a higher patch level than the source database (for example, if the source database is at Jan 2020 PSU/BP and the target database is at April 2020 PSU/BP), then Zero Downtime Migration runs the datapatch utility as part of the migration.
    
    * **Verify time zone file version**
    
        The target placeholder database must have a time zone file version that is the same or higher than the source database. If that is not the case, then the time zone file should be upgraded in the target placeholder database. 
    
        To check the current time zone version, query the `V$TIMEZONE_FILE` view as shown here, and upgrade the time zone file if necessary. 
    ```
    SQL> SELECT * FROM v$timezone_file;
    ```
    
    
    * **Verify TDE wallet folder exists**
    
        Verify that the TDE wallet folder exists, and ensure that the wallet `STATUS` is `OPEN` and `WALLET_TYPE` is `AUTOLOGIN` (For an auto-login wallet type), or `WALLET_TYPE` is `PASSWORD` (For a password-based wallet). For a multitenant database, ensure that the wallet is open on all PDBs as well as the CDB, and the master key is set for all PDBs and the CDB. 
    ```
    SQL> SELECT * FROM v$encryption_wallet;
    ```


    * **For Oracle RAC targets:**
    
    If the target is an Oracle RAC database, then verify that SSH connectivity without a passphrase is set up between the Oracle RAC servers for the oracle user.
    
    * **Check disk groups size and usage**
        
        Check the size of the disk groups and usage on the target database (ASM disk groups or ACFS file systems) and make sure adequate storage is provisioned and available on the target database servers.
        
    * **Check connections**
        
        * Verify that ports 22 and 1521 (or the configured database listener port) on the target servers in the Oracle Cloud Infrastructure, Oracle Exadata Database Service on Dedicated Infrastructure, or Oracle Exadata Database Service on Cloud@Customer environment are open and not blocked by a firewall. 
        
        An open connection is required from the Zero Downtime Migration host and the and source database server.
        
        * Verify that port 22 on the target database server allows incoming connections from the Zero Downtime Migration service host.
        
    * **Capture the output of the RMAN SHOW ALL command**
        
        Capture output so that you can compare RMAN settings after the migration, then reset any changed RMAN configuration settings to ensure that the backup works without any issues.
        ```
        RMAN> show all;
        ```





### Setting Up the Transparent Data Encryption Keystore {#GUID-B294CAD7-63AD-44BA-BE22-C8BAAE211643}

For Oracle Database 12c Release 2 and later, if the source and target databases do not have Transparent Data Encryption (TDE) enabled, then it is mandatory that you configure the TDE keystore before migration begins.

> **note:** For Oracle Database 11.2 and 12.1 sources TDE wallet configuration is not required. If the source does not have a TDE wallet configured, any TDE configuration on the target is removed. After the migration, you must configure the TDE wallet at the target. 

> **note:** 
If you migrate Oracle Database 11.2 and 12.1 sources, for unencrypted sources, even when providing a TDE wallet (which is a requirement), ZDM will not be able to use ENCRYPT on RESTORE at the target database. This is applicable when you migrate and upgrade the CDB databases. You must perform any missing encryption tasks manually, post-migration.

TDE should be enabled and the `TDE WALLET` status on both source and target databases must be set to `OPEN`. The `WALLET_TYPE` can be `AUTOLOGIN`, for an auto-login keystore (preferred), or `PASSWORD`, for a password-based keystore. On a multitenant database, make sure that the keystore is open on all PDBs as well as the CDB, and that the master key is set for all PDBs and the CDB. 

> **note:** Ensure to open all the PDBs before running any of the commands described. 

If TDE is not already configured as required on the source and target databases, use the following instructions to set up the TDE keystore. 

> **note:** When the source TDE wallet is configured with `LOCAL_AUTOLOGIN` single sign-on certificate, ZDM does not copy the single sign-on, because the `LOCAL_AUTOLOGIN` single sign-on certificates are only valid on the nodes for which they are generated. Instead, you must enter the TDE wallet password to generate the `AUTOLOGIN` certificate for the target nodes. So, when the source is using the `LOCAL_AUTOLOGIN` wallet, post migration the target will have `AUTOLOGIN` wallet. ZDM will not generate the `LOCAL_AUTOLOGIN` wallet. 

> **note:** When the source or the target database is using keystore location different from the `FILE` or `ASM` wallet, ZDM does not migrate the TDE keys. Ensure that the wallet keys at the source that are accessible from the target because, when the keystore is available not on file system keystore, the export, import, or migration of the keys need to be handled using keystore specific tools. ZDM cannot migrate the keys from keystores that are different from `FILE` or `ASM`. 

ZDM encrypts *all* tablespaces for cases where TDE is mandatory for migrations involving backup/restore or direct migration using restore from service. 

ZDM now uses a single restore statement with `encrypt on restore` for the entire database for targets which have Oracle Database 19c and later versions: 
```
RESTORE DATABASE AS ENCRYPTED AS ENCRYPTED FROM TAG 'ZDM_JOB_10';
```


> **note:** This feature affects migrations that uses backup/restore or direct migration using restore from service. For migrations using ZDLRA or direct using active duplicate, ZDM does not perform any additional encryption, the encryption status on the target post restore is same as the source database. 

For a password-based keystore, you only need to do steps 1, 2, and 4; for an auto-login keystore, complete all of the steps.

1. Set `ENCRYPTION_WALLET_LOCATION` in the `$ORACLE_HOME/network/admin/sqlnet.ora` file. 
    ```
    /home/oracle>cat /u01/app/oracle/product/12.2.0.1/dbhome_4/network/admin/sqlnet.ora
    
    ENCRYPTION_WALLET_LOCATION=(SOURCE=(METHOD=FILE)
    (METHOD_DATA=(DIRECTORY=/u01/app/oracle/product/12.2.0.1/dbhome_4/network/admin/)))
    ```
    
    
    For an Oracle RAC instance, also set `ENCRYPTION_WALLET_LOCATION` in all the nodes. 

2. Create and configure the keystore.

    1. Connect to the database and create the keystore.
    ```
    $ sqlplus "/as sysdba"
    SQL> ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/u01/app/oracle/product/12.2.0.1/dbhome_2/network/admin'
    identified by password;
    ```
    
    
    2. Open the keystore.
    
    For a non-CDB environment, run the following command.
    ```
    SQL> ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY password;
    keystore altered.
    ```
    
    
    For a CDB environment, run the following command.
    ```
    SQL> ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY password container = ALL;
    keystore altered.
    ```
    
    
    3. Create and activate the master encryption key.
    
    For a non-CDB environment, run the following command.
    ```
    SQL> ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY password with backup;
    keystore altered.
    ```
    
    
    For a CDB environment, run the following command.
    ```
    SQL> ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY password with backup container = ALL;
    keystore altered.
    ```
    
    
    4. Query `V$ENCRYPTION_KEYS` to get the keystore status, keystore type, and keystore location. 
    ```
    SQL> SELECT * FROM v$encryption_keys;
    
    WRL_TYPE    WRL_PARAMETER
    --------------------            --------------------------------------------------------------------------------
    **STATUS**                         **WALLET_TYPE**          WALLET_OR FULLY_BAC    CON_ID
    ------------------------------ -------------------- --------- ---------            ----------
    FILE        /u01/app/oracle/product/12.2.0.1/dbhome_2/network/admin/
    **OPEN**                           **PASSWORD**             SINGLE    NO         0
    ```
    
    
    The configuration of a password-based keystore is complete at this stage, and the keystore is enabled with status `OPEN` and `WALLET_TYPE` is shown as `PASSWORD` in the query output above. 
    
    Continue to step 3 only if you need to configure an auto-login keystore, otherwise skip to step 4.

3. For an auto-login keystore only, complete the keystore configuration.

    1. Create the auto-login keystore.
                ```
                SQL> ADMINISTER KEY MANAGEMENT CREATE AUTO_LOGIN KEYSTORE FROM KEYSTORE
                '/u01/app/oracle/product/12.2.0.1/dbhome_2/network/admin/' IDENTIFIED BY password;
                keystore altered.
                ```
                
                
    2. Close the password-based keystore.
                ```
                SQL> ADMINISTER KEY MANAGEMENT SET KEYSTORE CLOSE IDENTIFIED BY password;
                keystore altered.
                ```


    3. Query `V$ENCRYPTION_WALLET` to get the keystore status, keystore type, and keystore location. 
    ```
    SQL> SELECT * FROM v$encryption_wallet;
    WRL_TYPE WRL_PARAMETER
    -------------------- --------------------------------------------------------------------------------
    **STATUS** **WALLET_TYPE** WALLET_OR FULLY_BAC CON_ID
    ------------------------------ -------------------- --------- --------- ---------
    FILE /u01/app/oracle/product/12.2.0.1/dbhome_2/network/admin/
    **OPEN** **AUTOLOGIN** SINGLE NO
    ```
    
    
    In the query output, verify that the TDE keystore `STATUS` is `OPEN` and `WALLET_TYPE` set to `AUTOLOGIN`, otherwise the auto-login keystore is not set up correctly. 
    
    This completes the auto-login keystore configuration.

4. Copy the keystore files to the second Oracle RAC node.

    If you configured the keystore in a shared file system for Oracle RAC, or if you are enabling TDE for a single instance database, then no action is required.
    
    If you are enabling TDE for Oracle RAC database without shared access to the keystore, copy the following files to the same location on second node.
    
    * `/u01/app/oracle/product/12.2.0.1/dbhome_2/network/admin/ew*`
    
    * `/u01/app/oracle/product/12.2.0.1/dbhome_2/network/admin/cw*`
    



> **note:** ZDM can migrate all the wallet files and sub-directories configured under `WALLET_ROOT`. If the source has PDB wallets in isolated mode, ZDM migrates all of the wallet's files in sub-directories under `WALLET_ROOT`. 

**Related Topics**

* [Using Transparent Data Encryption](https://docs.oracle.com/en/database/oracle/oracle-database/21/asoag/asopart1.md)



### Using Supported Data Transfer Media {#GUID-5BA60AAB-AF12-467C-8214-E4B12EB084D9}

The Zero Downtime Migration physical migration process involves creating a backup of the source database and restoring it to the target database, or using and existing backup. 

**Oracle Cloud Infrastructure Object Storage**

OCI Object Storage is supported as a backup medium when migrating a database to Oracle Cloud Infrastructure, Oracle Exadata Database Service on Dedicated Infrastructure, or any on-premises Oracle Exadata Database Service on Cloud@Customer target.

Zero Downtime Migration service either initiates the source database backup as part of the migration work flow, or you can specify an existing backup already in the Object Storage bucket, and Zero Downtime Migration restores it to the target environment, so Object Storage must be accessible from both the source and target environments.

The source database is backed up to the specified container and restored to the target using RMAN SQL*Net connectivity.

The Zero Downtime Migration service host uses an SSH connection to the source and target database servers to install and configure the backup module software necessary to back up to and restore from Object Storage. The backup from the source database to Object Storage takes place over an RMAN channel.

Make sure that the Object Storage bucket is created using the Oracle Cloud Service Console as appropriate. 

Also, make sure adequate storage is provisioned and available on the object store to accommodate the source database backup.

**Zero Data Loss Recovery Appliance**

Zero Data Loss Recovery Appliance is supported as a backup medium for migrating a database to an Oracle Exadata Database Service on Cloud@Customer target or an Oracle Exadata Database Machine.

If Zero Data Loss Recovery Appliance is chosen as backup medium, then you must ensure that the Zero Data Loss Recovery Appliance has a valid backup of the source database, because Zero Downtime Migration does not initiate a backup to Zero Data Loss Recovery Appliance as part of the work flow.

You must also ensure that all instances of the database are up before initiating a backup to Zero Data Loss Recovery Appliance. The duplicate database operation might fail if the backup is initiated when an instance is down.

The Zero Downtime Migration service accesses the backup in Zero Data Loss Recovery Appliance and restores it to Oracle Exadata Database Service on Cloud@Customer. The Zero Data Loss Recovery Appliance access credentials and wallet location are mandatory input parameters, so that Zero Downtime Migration can handle the Zero Data Loss Recovery Appliance wallet setup at the target database. 

Any transfer of redo stream between the source and the target database server, in either direction, takes place over a SQL*Net link. 

Refer to the Zero Data Loss Recovery Appliance documentation for information about creating backups.

**Network File System (NFS)**

NFS is supported as a backup medium when migrating a database to an Oracle Exadata Database Service on Cloud@Customer target, or any on-premises Oracle Exadata Database Machine target.

If you choose to back up the database to an NFS mount, then the Zero Downtime Migration service initiates the source database backup (or you can specify a pre-existing backup) and restores it to the Exadata target environment. The NFS should be accessible from both the source and target environments. 

The source database is backed up to the specified path and restored to Oracle Exadata Database Service on Cloud@Customer using RMAN SQL*Net connectivity.

When you perform a migration using offline migration through NFS, SQL*Net connectivity between the source and target are not needed.

### Setting Physical Migration Parameters {#GUID-892533DD-94BA-405F-8C27-47F02BA9E06C}

Set the required physical migration response file parameters. Get the response file template, `$ZDM_HOME/rhp/zdm/template/zdm_template.rsp`, which is used to create your Zero Downtime Migration response file for the database migration procedure, and edit the file as described here. 

> **note:** Currently, only online physical migration is supported when DATA_TRANSFER_MEDIUM=DIRECT. 

The following response file settings show you how to configure a typical use case. To further customize your configuration you can find additional parameters described in [Zero Downtime Migration Physical Migration Response File Parameters Reference](zero-downtime-migration-physical-migration-response-file-parameters-reference.md#GUID-9DA94B7A-04C7-40D9-B3CA-8F680F618C3F). 

TGT_DB_UNIQUE_NAME

Set `TGT_DB_UNIQUE_NAME` to the target database `DB_UNIQUE_NAME` value. To find `DB_UNIQUE_NAME` run 
```
SQL> show parameter db_unique_name
```


For Cloud type Exadata Cloud at Customer Gen 1, set `TGT_DB_UNIQUE_NAME` to a different `DB_UNIQUE_NAME` not currently in use. 

PLATFORM_TYPE

Set `PLATFORM_TYPE` to one of the following: 

* `VMDB` \- Oracle Cloud Infrastructure virtual machine or bare metal targets. 

* `EXACS` \- Oracle Exadata Database Service on Dedicated Infrastructure 

* `EXACC` \- Oracle Exadata Database Service on Cloud@Customer 

* `NON_CLOUD` \- On-premises Exadata Database Machine 




MIGRATION_METHOD

Set `MIGRATION_METHOD` to one of the following: 

* `ONLINE_PHYSICAL` \- Oracle Data Guard (online) 

* `OFFLINE_PHYSICAL` \- RMAN backup and restore (offline). Note that this is the only migration method supported for Oracle Database Standard Edition 2. 




DATA_TRANSFER_MEDIUM

`DATA_TRANSFER_MEDIUM` specifies the media used for the source database backup. 

* `OSS` \- Oracle Data Guard using Object Storage Service (OSS) for standby initialization. 

Supported for `PLATFORM_TYPE` set to Oracle Cloud Infrastructure (`VMDB`), Oracle Exadata Database Service on Dedicated Infrastructure(`EXACS`), and Oracle Exadata Database Service on Cloud@Customer (`EXACC`). 

Also set `ZDM_LOG_OSS_PAR_URL` to the Cloud Object Store pre-authenticated URL if you want to upload migration logs onto Cloud Object Storage. For information about getting a pre-authenticated URL see Oracle Cloud documentation at [https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm#usingconsole](https://docs.cloud.oracle.com/en-us/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm#usingconsole). 

When you perform a migration using backup and restore (`OFFLINE_PHYSICAL`) through Object Storage Service, SQL*Net connectivity between the source and target are not needed. 

See [Using Supported Data Transfer Media](preparing-for-database-migration.md#GUID-5BA60AAB-AF12-467C-8214-E4B12EB084D9) for more information about using OSS. 

* `ZDLRA` \- Oracle Data Guard using ZDLRA for standby initialization. 

    Supported for `PLATFORM_TYPE` set to Oracle Exadata Database Service on Cloud@Customer (`EXACC`) or on-premises Exadata Database Machine (`NON_CLOUD`), and set the following parameters. 

    * Set `SRC_ZDLRA_WALLET_LOC` for the wallet location, for example, 
    ```
    SRC_ZDLRA_WALLET_LOC=/u02/app/oracle/product/12.1.0/dbhome_3/dbs/zdlra
    ```
    
    
    * Set `TGT_ZDLRA_WALLET_LOC` for the wallet location, for example, `TGT_ZDLRA_WALLET_LOC=*`target_database_oracle_home`*/dbs/zdlra`. 
    
    * Set `ZDLRA_CRED_ALIAS` for the wallet credential alias, for example, 
    ```
    ZDLRA_CRED_ALIAS=zdlra_scan:listener_port/zdlra9:dedicated
    ```


    See [Using Supported Data Transfer Media](preparing-for-database-migration.md#GUID-5BA60AAB-AF12-467C-8214-E4B12EB084D9) for more information about using ZDLRA. 

* `NFS` \- Oracle Data Guard using backup location such as NFS. 

    Supported for `PLATFORM_TYPE` set to Oracle Exadata Database Service on Cloud@Customer (`EXACC`) or on-premises Exadata Database Machine (`NON_CLOUD`). 
    
    Also set `BACKUP_PATH` to specify the actual NFS path which is made accessible from both the source and target database servers, for example, an NFS mount point. The NFS mount path should be same for both source and target database servers. This path does not need to be mounted on the Zero Downtime Migration service host. 
    
    Note the following considerations:

    * The path set in `BACKUP_PATH` should have ‘rwx’ permissions for the source database user, and at least read permissions for the target database user. 
    
    * In the path specified by `BACKUP_PATH`, the Zero Downtime Migration backup procedure will create a directory, `$BACKUP_PATH/*`dbname`*`, and place the backup pieces in this directory. 

See [Mount Options for Oracle files for RAC databases and Clusterware when used with NFS on NAS devices (Doc ID 359515.1) ](https://support.oracle.com/rs?type=doc&id=359515.1)See [Using Supported Data Transfer Media](preparing-for-database-migration.md#GUID-5BA60AAB-AF12-467C-8214-E4B12EB084D9) for more information about using NFS. 

* `DIRECT` \- Uses RMAN active database duplication or restore from service to transfer data directly from the source to the target. 

The transfer method (restore from service or active duplicate) is configured with `ZDM_RMAN_DIRECT_METHOD`, which is set to `RESTORE_FROM_SERVICE` by default. 

ZDM uses the default service and only if you want to use a service other than the default service, then provide the `ZDM_SRC_DB_RESTORE_SERVICE_NAME` parameter. 

> **note:** Here, the source database is the primary source itself. 

See [Using Direct Data Transfer](preparing-for-database-migration.md#GUID-6AD20F6E-BDAB-40A0-9EEB-D345BDBFEAA8) for more information about direct data transfer, and see [Using an Existing Standby to Instantiate the Target Database](preparing-for-database-migration.md#GUID-71F345C5-5ED1-4C5D-AD54-76784D4AE221) to directly transfer data from a standby. 

* `EXTBACKUP` \- Oracle Data Guard with existing backup in external location. 

Supported for `PLATFORM_TYPE` set to Oracle Exadata Database Service on Cloud@Customer (`EXACC`) or on-premises Exadata Database Machine (`NON_CLOUD`) 

See [Using an Existing RMAN Backup as a Data Source](preparing-for-database-migration.md#GUID-1AAE687D-ED0C-481B-B4BD-89DFB72C479E) for more information. 




Additional Oracle Cloud Object Storage Settings

When `DATA_TRANSFER_MEDIUM=OSS`, set the following additional parameters to access Oracle Cloud Object Storage. 

* Set `HOST` to the cloud storage REST endpoint URL. 
    
    * For Oracle Cloud Infrastructure storage the typical value format is `HOST=https://swiftobjectstorage.us-phoenix-1.oraclecloud.com/v1/*`ObjectStorageNamespace`*`
    
    To find the Object Storage Namespace value, log in to the Cloud Console and select **Menu**, **Administration**, **Tenancy Detail**, and in the **Object Storage Settings** section find **Value against entry Object Storage Namespace:**
    
    * For Oracle Cloud Infrastructure Classic storage the typical value format is `HOST=https://acme.storage.oraclecloud.com/v1/Storage-*`tenancy name`*`
    
* Set the Object Storage bucket `OPC_CONTAINER` parameter. 
    
    The bucket is also referred to as a container for Oracle Cloud Infrastructure Classic storage.
    
    


TGT_SSH_TUNNEL_PORT

If SSH tunneling is set up, set the `TGT_SSH_TUNNEL_PORT` parameter. 

Data and Redo Locations

Zero Downtime Migration automatically discovers the location for `data`, `reco`, and `redo` (for non-Exadata systems) storage volumes from the specified target database. If you need to override the discovered values, specify the target database data files storage (ASM or ACFS) location using the appropriate set of parameters. 

* ASM: `TGT_DATADG`, `TGT_REDODG`, and `TGT_RECODG`
* ACFS: `TGT_DATAACFS`, `TGT_REDOACFS`, and `TGT_RECOACFS`



SKIP_FALLBACK

Set `SKIP_FALLBACK=TRUE` if you do not want to ship redo logs from the target to the source standby, either voluntarily or because there is no connectivity between the target and the source. 

TGT_SKIP_DATAPATCH

Zero Downtime Migration runs the datapatch utility by default as part of the migration process if the target database environment is at a higher patch level than the source database (for example, if the source database is at Jan 2020 PSU/BP and the target database is at April 2020 PSU/BP). 

If you want to skip this task set the `TGT_SKIP_DATAPATCH=FALSE` response file parameter. 

*`PHASE_NAME`*_MONITORING_INTERVAL 

Set *`PHASE_NAME`*_MONITORING_INTERVAL=*`n mins`*  if you want Zero Downtime Migration to monitor and report the status of backup and restore operations at the configured time interval during the migration. The default interval value is 10 minutes. To disable monitoring, set these values to 0 (zero). 
```
ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL=
ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL=
ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL=
ZDM_CLONE_TGT_MONITORING_INTERVAL=
ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL=
ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL=
```


ZDM_BACKUP_RETENTION_WINDOW

Set `ZDM_BACKUP_RETENTION_WINDOW=*`number of days`*` if you wish to retain source database backup after the migration. 

ZDM_SRC_TNS_ADMIN

Set `ZDM_SRC_TNS_ADMIN=*`TNS_ADMIN value`*` in case of custom location. 

ZDM_APPLY_LAG_MONITORING_INTERVAL

Set `ZDM_APPLY_LAG_MONITORING_INTERVAL` = `{NONE \| DAILY \| WEEKLY}`, to monitor redo apply lag after standby database creation. This phase queries the current redo apply lag, and waits until redo apply is finished before resuming the job. See [ZDM_APPLY_LAG_MONITORING_INTERVAL](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-F7C34BFC-5C42-41F7-AFF6-511533C9A284). 

### Using an Existing RMAN Backup as a Data Source {#GUID-1AAE687D-ED0C-481B-B4BD-89DFB72C479E}

Zero Downtime Migration lets you use an existing level 0 backup to skip the full backup phase of a migration job.

This method is supported for Oracle Exadata Database Service on Cloud@Customer or on-premises Exadata Database Machine targets.

Zero Downtime Migration takes level 0 and level 1 backups on the fly for both online and offline physical migration jobs. During a migration job, Zero Downtime Migration lets you re-use existing source database backup in place of performing a full back up.

All types of backup devices, such as DISK, SBT_TAPE, and ZDLRA, are supported as data transfer media for this migration method.

Only level 0 backups with `incremental_level=0` are valid for this use case. 

To use an existing RMAN backup, set the following response file parameters.
```
ZDM_USE_EXISTING_BACKUP=TRUE
ZDM_BACKUP_TAG=RMAN backup tag
```


> **note:** For ZDLRA, you must bring up the pre-existing backup. However, skip the `ZDM_USE_EXISTING_BACKUP` parameter for ZDLRA. 

When you run the ZDMCLI database migration command in evaluation mode (`ZDMCLI database migration -eval`), Zero Downtime Migration verifies the existence of the backup, checks that it is valid, and displays whether the backup is available and validated in the job output. 

In migrate mode (`ZDMCLI database migration`), Zero Downtime Migration performs the same steps done in evaluation mode, then skips full backup and performs other backup operations like incremental and differential backup. 

**Creating a Backup**

To take a valid RMAN backup to use as a migration source, you can run the following commands.

For DISK:
```
RUN {
ALLOCATE CHANNEL channel_name DEVICE TYPE DISK FORMAT 'directory_path/%d_backup_%U';
ALTER SYSTEM ARCHIVE LOG CURRENT;
BACKUP AS COMPRESSED BACKUPSET FORCE INCREMENTAL LEVEL 1 FOR RECOVER OF TAG 'backup_tag'
DATABASE FORMAT 'directory_path/%d_backup_%U_DBF' SECTION SIZE 4G ;
}
```


For SBT_TAPE:
```
RUN {
ALLOCATE CHANNEL channel_name DEVICE TYPE SBT FORMAT '%d_%I_%T-%s_%p'
PARMS 'SBT_LIBRARY=path/libopc.so, SBT_PARMS=(OPC_PFILE=path/OPC/mzdm.conf)';
ALTER SYSTEM ARCHIVE LOG CURRENT;
BACKUP AS COMPRESSED BACKUPSET FORCE INCREMENTAL LEVEL 1 FOR RECOVER OF TAG 'backup_tag'
DATABASE FORMAT '%d_%I_%T-%s_%p_DBF' SECTION SIZE 4G ;
}
```


**Creating a Standby Control File Backup**

Also, create a standby control file backup in the specified path and provide read permissions to the backup pieces for the target database user. For example,
```
RMAN> BACKUP CURRENT CONTROLFILE FOR STANDBY FORMAT 'BACKUP_PATH/lower_case_dbname/standby_ctl_%U';
```


Where `standby_ctl_%U` is a system-generated unique file name. 

**Alternate Use Case**

If the `ZDM_BACKUP_TAG` value is provided and `ZDM_USE_EXISTING_BACKUP=FALSE` the Zero Downtime Migration will create a full backup with the provided tag. 

**Using Backups with Password Authentication**

To use an existing backup with backup password authentication, you can specify the password using the `-backuppasswd *`password`*` option in the command `migrate database`. 

You can also specify a backup wallet path by using `-backupwallet` in the command `migrate database`. 

### Using Direct Data Transfer {#GUID-6AD20F6E-BDAB-40A0-9EEB-D345BDBFEAA8}

Zero Downtime Migration supports direct data transfer during a physical migration to avoid backing up the source database to an intermediate store such as Object Storage or NFS.

**Active Database Duplication**

RMAN active database duplication is supported in Oracle Database 11g (11.2) and later releases.

**Restore From Service**

RMAN restore from service is supported in Oracle Database 12g (12.1) and later releases. Oracle MAA best practices recommend using active duplication for Oracle Database 11.2 and using restore from service for Oracle Database 12.1 and later.

Because the connection is initiated from the target database host, both active duplication and restore from service direct transfer methods require SQL*Net connectivity from the target to the source database.

You must also set up non-interactive access between the source and target. See [Providing Passwords Non-Interactively Using a Wallet](provide-passwords-non-interactively-using-wallet.md#GUID-CED7A2D9-6AD9-4527-BE29-2F036B6CEDCC). 

### Using an Existing Standby to Instantiate the Target Database {#GUID-71F345C5-5ED1-4C5D-AD54-76784D4AE221}

To reduce the load on your primary database system, Zero Downtime Migration can use an existing standby database to instantiate the standby in the target environment in a physical migration. 

This migration option is only available when you are using direct data transfer with RMAN restore from service.

Because Oracle Database 11.2 doesn't support RMAN restore from service, it is therefore not supported for migration from an existing standby.

In the previous versions of Zero Downtime Migration, it was required to create a standby control file backup. However, from the 21.4 version of Zero Downtime Migration, you do not require to create a standby control file backup.

Ensure Snapshot Control File is on Shared Storage

In an Oracle Real Application Clusters (Oracle RAC) environment, the snapshot control file location must be on a shared file system- that is, a storage that is accessible to all Oracle RAC instances or ASM.

The snapshot control file location in the RMAN configuration points to the non-shared location in the ORACLE_HOME/dbs/ directory by default.

In cases when data is transferred from a primary database, Zero Downtime Migration configures the snapshot file location, but for direct data transfer from the standby database, Zero Downtime Migration does not configure the location. 

You must ensure that RMAN is configured correctly for direct data transfer from the standby. 

The example RMAN commands in the following steps set the configuration for the location of the snapshot control file for your cluster database; therefore, ensure that the directory location is shared by all nodes that perform backups.

1. Configure the snapshot control file on one node of source primary database, as shown in this example.
```
RMAN> CONFIGURE SNAPSHOT CONTROLFILE NAME TO
'+ASM_STBM000038VM5/dbhome2_prim/snapcf_dbhome21.f';

new RMAN configuration parameters:
CONFIGURE SNAPSHOT CONTROLFILE NAME TO
'+ASM_STBM000038VM5/dbhome2_prim/snapcf_dbhome21.f';
new RMAN configuration parameters are successfully stored

RMAN> show SNAPSHOT CONTROLFILE NAME;

RMAN configuration parameters for database with db_unique_name DBHOME2_PRIM
are:
CONFIGURE SNAPSHOT CONTROLFILE NAME TO
'+ASM_STBM000038VM5/dbhome2_prim/snapcf_dbhome21.f';
```


2. Configure the snapshot control file on one node of the source standby database, as shown in this example.
```
RMAN> CONFIGURE SNAPSHOT CONTROLFILE NAME TO
'+ASM_STBM000038VM5/DBHOME2_STBY/snapcf_dbhome21.f';

new RMAN configuration parameters:
CONFIGURE SNAPSHOT CONTROLFILE NAME TO
'+ASM_STBM000038VM5/DBHOME2_STBY/snapcf_dbhome21.f';
new RMAN configuration parameters are successfully stored

RMAN> show SNAPSHOT CONTROLFILE NAME;

RMAN configuration parameters for database with db_unique_name DBHOME2_STBY
are:
CONFIGURE SNAPSHOT CONTROLFILE NAME TO
'+ASM_STBM000038VM5/DBHOME2_STBY/snapcf_dbhome21.f';
```





Enable Target Database Instantiation from the Standby

To request instantiation of the target database from an existing standby, set the following parameters in the physical migration response file.

`ZDM_USE_EXISTING_STANDBY = TRUE` (default FALSE) 

`ZDM_STANDBY_DB_CONNECT_STRING=*`connection string to access existing standby`*` (optional) 

Keeping Source and Target in Sync

Once the target database is instantiated it is kept in sync by registering with the primary and applying archive redo logs shipped from primary.

Set the following parameters ZDM_USE_EXISTING_STANDBY = TRUE (default FALSE) and ZDM_STANDBY_DB_CONNECT_STRING=connection string to access existing standby (optional). If you want to use the existing standby option, then skip the ZDM_SRC_DB_RESTORE_SERVICE_NAME parameter as that is for the source database. By default, ZDM tries to infer the standby database connect string from the Data Guard configuration and you can specify ZDM_STANDBY_DB_CONNECT_STRING to override ZDM's standby database connect string discovery. 

### Preparing for Automatic Application Switchover {#GUID-E469DD80-6C5A-4105-A08D-8A976097F560}

To minimize or eliminate service interruptions on the application after you complete the database migration and switchover, prepare your application to automatically switch over connections from the source database to the target database.

> **note:** In physical migrations, Autonomous Database targets are not supported for automatic application switchover. 

In the following example connect string, the application connects to the source database, and when it is not available the connection is switched over to the target database.
```
(DESCRIPTION=
(FAILOVER=on)(LOAD_BALANCE=on)(CONNECT_TIMEOUT=3)(RETRY_COUNT=3)
(ADDRESS_LIST=
(ADDRESS=(PROTOCOL=TCP)(HOST=source_database_scan)(PORT=1521))
(ADDRESS=(PROTOCOL=TCP)(HOST=target_database_scan)(PORT=1521)))
(CONNECT_DATA=(SERVICE_NAME=**zdm_prod_svc**)))
```


On the source database, create the service, named zdm_prod_svc in the examples.
```
srvctl add service -db clever -service **zdm_prod_svc** -role PRIMARY
-notification TRUE -session_state dynamic -failovertype transaction
-failovermethod basic -commit_outcome TRUE -failoverretry 30 -failoverdelay 10
-replay_init_time 900 -clbgoal SHORT -rlbgoal SERVICE_TIME -preferred clever1,clever2
-retention 3600 -verbose
```


If the `db_domain` changes between the source and target then the connect string specified in the application should cater to both for failover to be effective. 
```
(DESCRIPTION_LIST=
(FAILOVER=ON)
(LOAD_BALANCE=ON)
(DESCRIPTION=
(ADDRESS= (PROTOCOL=TCP) (HOST=SRC_SCAN) (PORT=1521))
(CONNECT_DATA=
(SERVICE_NAME=SVC.SRC_DOMAIN)))
(DESCRIPTION=
(ADDRESS= (PROTOCOL=TCP) (HOST=TGT_SCAN) (PORT=1521))
(CONNECT_DATA=
(SERVICE_NAME= SVC.TGT_DOMAIN))
```


> **note:** See Also:Oracle MAA white papers about client failover best practices on the [MAA Best Practices - Oracle Database](https://www.oracle.com/database/technologies/high-availability/oracle-database-maa-best-practices.md) page at <https://www.oracle.com/goto/maa>. 

[High Availability](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ADFNS-GUID-9DB4AA4F-8273-4761-9A51-7FAD6F8298F0) in *Oracle Database Development Guide*

### Using Oracle Data Guard Broker Role Switchover {#GUID-000723DE-532F-44DC-A0B5-2B04890228F5}

In physical migrations, Zero Downtime Migration can leverage the Oracle Data Guard broker to manage database role switchover.

Typically, the Zero Downtime Migration service manages database role switchover after the data migration is complete. You can alternatively enable an option that lets the broker handle the switchover.

The Data Guard Broker is a feature of Data Guard, which is available via the online migration workflow. The Data Guard broker is a distributed management framework that automates the creation, maintenance, and monitoring of Data Guard configurations. You can use a Data Guard broker configuration to improve usability and centralize management and monitoring of the Oracle Data Guard configuration. Using the broker helps not only to set up replication, but also performs gap detection, switchover, and switchback operations. 

> **note:** Currently, ZDM broker enabled migrations from single instances is only supported if the source environment has an existing Data Guard broker enabled configuration. All the Oracle RAC sources are supported. If you want to migrate a single instance database with no active Data Guard Broker configuration, proceed to migrate with ZDM without the Data Guard Broker. 

Prerequisites

Broker-managed role switchovers require two-way SQL*Net connectivity.

Note that broker configuration is not supported for Oracle Database 11.2.0.4.

Enabling Broker Database Role Switchover

You can enable or disable the broker-managed role switchover option using the `ZDM_USE_DG_BROKER` response file parameter. 

`ZDM_USE_DG_BROKER = TRUE | FALSE`

`ZDM_USE_DG_BROKER` is set to `FALSE` by default, meaning that the broker does not handle the switchover. 

Broker Option Validation

When the broker-managed switchover option is enabled, Zero Downtime Migration verifies that the source primary database has no existing standby that is not broker-managed. This check is done because the broker configuration cannot co-exist with a non-broker managed standby.

With the broker option enabled, in cases when the primary (source) database does not have an existing standby, or has a standby that is already managed by the broker, Zero Downtime Migration creates a broker configuration to manage the target (Data Guard standby) database.

### Configuring Resiliency to Intermittent Network Failures {#GUID-BCFE112D-C177-43CF-BA67-FC7F1454FCF0}

Zero Downtime Migration physical migrations are resilient to intermittent network failures that can cause backups or SSH connectivity to fail.

Zero Downtime Migration can auto-detect intermittent network failures. Zero Downtime Migration automatically retries the RMAN retry-able errors, and some retry customization is available.

SSH connection retries are customized using the following parameters:

`SRC_SSH_RETRY_TIMEOUT`

`TGT_SSH_RETRY_TIMEOUT`

You can customize RMAN backup retries with following parameters:

`ZDM_OPC_RETRY_WAIT_TIME`

`ZDM_OPC_RETRY_COUNT`

`ZDM_OPC_RETRY_WAIT_TIME`

### Converting a Non-CDB Database to a CDB During Migration {#GUID-FAA12E81-05F5-4E30-884B-8369C6C092BB}

As part of the physical migration process, Zero Downtime Migration can handle conversion of a non-CDB source database to a PDB of the same version in the cloud. The conversion process transforms the source non-CDB into a target PDB that is then plugged into an existing CDB in the target.

Downtime will increase due to the non-CDB to CDB conversion. This process is offline (no redo transport and apply), and no rollback is possible.

ZDM physical migration allows the conversion between Non-CDB sources and PDB targets. Define the name of the target PDB before starting the migration with the `ZDM_NONCDBTOPDB_PDB_NAME` response file parameter. 

**Source non-CDB Database Prerequisites**

* Oracle Database 12c or later versions, because this is when multitenant architecture became available

* Same character set as the target CDB




**Target Database CDB and PDB Prerequisites**

* The target CDB must not contain a PDB with same name as the resulting converted PDB, because Zero Downtime Migration will create the PDB.

* The target database must be at least the same major version as the source database.

    * If the minor version is different on the target, it must be a higher minor version than the source database.
    * If the patch level is different, you must set the response file parameter `TGT_SKIP_DATAPATCH=FALSE`. 
* Ensure that the target CDB database has sufficient resources such as memory and CPU, to accommodate migrating a non-CDB database and converting to a PDB, that is plugged into the target CDB database.



**Transparent Data Encryption Requirements**

* For source credentials, the `migrate database` command must include either `-tdekeystorepasswd` or the `-tdekeystorewallet` auto-login option. 

* If any of these options is used then the target credentials must be also provided by using either `-tgttdekeystorepasswd` or the `-tgttdekeystorewallet` auto-login option. 




**Application Express Requirements**

* If Application Express (APEX) is not installed on the source there are no further requirements.

* If APEX exists on the source, and the source database is a non-CDB, you must choose one of the following options:

    * Remove APEX from the source non-CDB.
    * Verify that the APEX version on the target CDB is the same as that on the source. 

If APEX is not at the same version conversion is not possible; APEX schemas vary between versions and the target PDB will not be able to open.




The target CDB is not dropped in the process, and the presence or absence of other PDBs does not affect the outcome of the conversion and plug-in.

Parameters in the response file are set as follows:

* (Required) `NONCDBTOPDB_CONVERSION`: Set to `TRUE` to indicate that you want to convert a source database from non-CDB to PDB. 
* (Optional) `NONCDBTOPDB_SWITCHOVER`: Set to `TRUE` for a physical migration using Data Guard switchover, to execute switchover operations during a migration job with non-CDB to PDB conversion enabled. 



The following are examples of the ZDMCLI `migrate database` command usage for converting a non-CDB to a PDB during migration using the TDE credentials. 

**Example interactively supplying passwords:**
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-sourcedb source_db_unique_name_value
-sourcenode source_database_server_name
-srcroot
-targetnode target_database_server_name
-backupuser Object_store_login_user_name
-rsp response_file_location
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/user/bin/sudo
-tdekeystorepasswd
-tgttdekeystorepasswd
```


**Example using auto-login wallet for CDB TDE keystore:**
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-sourcedb source_db_unique_name_value
-sourcenode source_database_server_name
-srcroot
-targetnode target_database_server_name
-backupuser Object_store_login_user_name
-rsp response_file_location
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/user/bin/sudo
-tdekeystorepasswd
-tgttdekeystorewallet /scratch/credentials/cdbtde.sso
```


### Migrating an On-Premises Database to an On-Premises Exadata Database Machine {#GUID-3DAA04E3-4846-412B-9BE7-A94E22E8029B}

An on-premises migration to an on-premises Exadata Database Machine target using Zero Downtime Migration works the same way as a migration to a cloud target. In the response file, you indicate that the migration target is on-premises by setting `PLATFORM_TYPE=NON_CLOUD`. 

Just like in cloud migration scenarios, you must provision the target database with the shape and size desired, including configuring any initialization parameters, before starting the migration. The target database is expected to be the same major version as the source database, Oracle Grid Infrastructure is mandatory at the target database, and target datafiles can be stored on ASM or ACFS.

One aspect where an on-premises to on-premises migration is different from migrating to the cloud is in the handling of Transparent Data Encryption (TDE). On the cloud, TDE is mandatory for Oracle Database 12.2 and later releases; however, for an on-premises to on-premises migration, TDE must be configured at the target only if TDE is used at the source. You must configure TDE at the target before the migration starts; Zero Downtime Migration does not configure it for you.

You can specify that TDE is not configured at the source or target by setting the response file parameter `ZDM_TDE_MANDATORY=FALSE`. This parameter can only be used when you set `PLATFORM_TYPE=NON_CLOUD`. With `ZDM_TDE_MANDATORY=FALSE` set, Zero Downtime Migration does not require TDE at the target when the source is not using TDE, and does not encrypt the target on restore. 

For an on-premises Exadata target database migration, `MIGRATION_METHOD` can be set to `ONLINE_PHYSICAL` or `OFFLINE_PHYSICAL`, and `DATA_TRANSFER_MEDIUM` can be set to any of the values supported by Zero Downtime Migration. Set the remaining parameters as you would for a cloud migration. 

### Creating an Oracle Cloud Native Disaster Recovery Strategy {#GUID-777D2891-BEDA-4F38-ABA2-5A097C7FE6A0}

In a typical migration, ZDM migrates a source database to only one target database (single point of failure). Now you can create a DR (Disaster Recovery) strategy during post-migration to be able to respond to and recover from an event that negatively affects your business operations.

Two target databases are instantiated during the migration (target primary database and target standby database), where both can be in different regions (to reduce impacts of natural disasters). During post-migration, Oracle Data Guard Broker configuration is restored in both target databases to allow cloud native operations like switchover and failover (in the Oracle Database Cloud Service console).

Prerequisites: 

1. Instantiate both target database and target standby database before beginning the migration process. These environments are assumed to be configured using Data Guard (DG) Broker. 
* When ExaCS/ExaCC is used for platform_type, this can be done using DG Associations (in the Oracle Database Cloud Service console).
* For VMDB platform_type, the configuration can be done (manually) using DGMGRL Oracle Data Guard Broker CLI tool.
2. Supported migration methods: Only online migrations are supported (where Oracle Data Guard is used).
3. Supported data transfer methods: OSS, ZDLRA, Direct. 
4. Supported Platform type: ExaCS, ExaCC, VMDB (ZDLRA is not supported).
5. Set the parameters in the response file as follows: 

    Required: 
    
    * Set TGT_STBY_NODE to the target standby database IP address to indicate that you want to configure a DR strategy on post-migration.
    * TGT_STBY_DB_UNIQUE_NAME: can identify the target standby as a different database from both the source database and the target database.
    * For connectivity to the target standby database, set TGT_STBY_AUTH= `zdmauth` and configure TGT_STBY_SUDO_USER (target_standby_database_server_login_user_name),TGT_STBY_IDENTITY_FILE (ZDM_installed_user_private_key_file_location), and TGT_STBY_SUDO_PATH (sudo_path). 
    
    Optional:
    
    * To keep the target standby database in sync during the migration, set ZDM_TGT_STBY_CASCADING to `TRUE` (default value) to allow redo log shipping from the target database or set to `FALSE` to allow the redo log shipping from the source database (primary). 
    * Set TGT_STBY_ZDLRA_WALLET_LOC if you want to use ZDLRA as the data transfer medium.

    > **note:** This use case is only supported for Non-CDB to PDB migrations and for CDB to CDB migrations, migrations that require conversion are not currently supported. 




When migrating NON-CDB to PDB using regular migration, it will get plugged into the target CDB. The target CDB can be setup to have a DR setup (for example using Oracle Data Guard Association in the console) of its own and when the NON-CDB is plugged into the target CDB, it would be replicated via the target CDB.

For DR migrations using ZDM, the source database will not be added (during post migration) to the OCI Oracle Data Guard Broker Configuration so it won't be managed by the OCI Console. However, the source database will still continue to receive the redo logs from the target database after the switchover.

### Migration and Upgrade of CDB Databases {#GUID-9CB27A1B-CC96-4125-B762-6640A4AED554}

ZDM performs migration of a CDB database to a CDB database and later upgrades the target CDB database to a version of `ORACLE_HOME` provided in the `ZDM_UPGRADE_TARGET_HOME` parameter. 

ZDM supports migration and upgrade of CDB for the target environment VMDB (Oracle Base Database Service) using `dbcli` utility. For other target environments apart from Oracle Base Database Service, Oracle Exadata Database Service on Dedicated Infrastructure, and Oracle Exadata Database Service on Cloud@Customer, ZDM uses autoupgrade to perform upgrade of CDB. 

ZDM supports migration and upgrade of CDB for Oracle Exadata Database Service on Dedicated Infrastructure and Oracle Exadata Database Service on Cloud@Customer target environment using the `dbaascli` utility. 

> **note:** 

For VMDB/EXACC/EXACS CDB upgrade target auth plugin, dbuser, and sudoasuser are not supported. Only zdmauth is the supported plugin in this scenario.

> **note:** You must ensure that the source database has TDE configured and you provide the wallet details, this ensures that post the migration and upgrade, the Oracle Database 19c target database has TDE configured. For upgrade and migrate scenario, TDE is mandatory for all releases. This is mandatory for all source versions, including Oracle Database 11.2 and Oracle Database 12.1. TDE is mandatory in the Oracle Cloud, for Oracle Database 12.2 and higher versions. For more information, see [Setting Up the Transparent Data Encryption Keystore](https://docs.oracle.com/en/database/oracle/zero-downtime-migration/21.4/zdmug/preparing-for-database-migration.md#GUID-B294CAD7-63AD-44BA-BE22-C8BAAE211643). 

> **note:** Currently, for Oracle Base Database Service, when you provision an Oracle Database 19c, the GI provisioned is also of the same version Oracle Database 19c. Currently, it is not possible to upgrade the GI from Oracle 19c to Oracle 23ai either. So effectively, it is currently not possible to install a Oracle Database 23ai database on the VMDB system initially created with Oracle Database 19c/GI. So, for the migration and upgrade scenario, provision the database version at the same version as source and provide an additional database version running at a higher version. 

Autoupgrade is used only for non_cloud targets. For all cloud platforms such as Oracle Exadata Database Service on Dedicated Infrastructure, Oracle Exadata Database Service on Cloud@Customer as well as Oracle Base Database Service, cloud tooling is used for upgrade. For Oracle Exadata Database Service on Dedicated Infrastructure and Oracle Exadata Database Service on Cloud@Customer `dbaascli` is used and `dbcli` for Oracle Base Database Service. 

As a prerequisite, provision ORACLE_HOME in Oracle Base Database Service, Oracle Exadata Database Service on Dedicated Infrastructure, and Oracle Exadata Database Service on Cloud@Customer environments as follows: 

* Provision ORACLE_HOME in VMDB using the following command: 

`/opt/oracle/dcs/bin/dbcli create-dbhome -v `

> **note:** For the use cases that do not involve Non-CDB to PDB conversion, the provisioned target database must be of the same version as the source database. 

* Provision ORACLE_HOME in EXACC/EXACS using console or by using the following command: 

`dbaascli dbhome create --version `




The upgrade starts after the completion of migration. Perform the following steps: 

1. Select the same version of the source and the target CDB.
2. Provision the `ORACLE_HOME` to the version you want to upgrade in the target database. 
3. Provide the path of the provisioned `ORACLE_HOME` in the `ZDM_UPGRADE_TARGET_HOME` parameter. 



### Migration, Upgrade, and Conversion of a Non-CDB to a PDB Database {#GUID-60D66FEB-C070-493A-9958-E22F555EB831}

In a single migration job, ZDM can migrate a non-CDB database to the target environment, upgrade the database to a PDB, and then plug in the upgraded PDB into the provided target CDB. 

ZDM supports migration and plug in of a non-CDB source database to a higher version target database using `autoupgrade.jar` to perform upgrade precheck, fixups and upgrade+plug operation. For example, the source non-CDB database could be of version Oracle Database 12.1 and target database could be a CDB of version Oracle Database 19c. 

> **note:** For the upgrade of Non-CDB database, Oracle Database 11g Release 2 (11.2.0.4) , `catbundle.sql ` is required. ZDM validates in the target database, the `catbundle.sql` location for the upgrade scenario of Oracle Database 11g Release 2 (11.2.0.4) database. 

The following inputs are required for this type of migration:

1. The source database is a non-CDB, presumably at a lower release than target CDB (for example 12.1)
2. A CDB on the target host at the desired database version (for example, 19c).
3. A database `ORACLE_HOME` of the same major version as the current source non-CDB version located on the target host. 

This home is used by ZDM to create an auxiliary Non-CDB database that is used during the migration process.

Set the `ZDM_PRE_UPGRADE_TARGET_HOME` response file parameter to the path of this `ORACLE_HOME`. 



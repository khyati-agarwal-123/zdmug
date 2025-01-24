## 6Preparing for a Hybrid Database Migration {#GUID-13AEFEA4-C18C-4AF4-9F3E-ABDEF496D847}

ZDM introduces hybrid, cross-platform Transportable Tablespace (XTTS) database migration support for cross-platform and cross-version migration using RMAN transportable tablespaces and Datapump metadata import/export.

You must fulfill the following pre-requisites to perform a successful hybrid migration: 

* Ensure that the source and target databases have the same timezone.

> **note:** The timezone related entries are not migrated and fail if different timezone is set. 

* For TDE

    * If the source database uses TDE and tablespaces are encrypted, the target database should also have TDE configured. Use either of the following options to provide the source and target TDE passwords: 
    * Using the following parameters with the `zdmcli migrate database` command using the ZDMCLI command line:
    ```
    -tdekeystorepasswd for the source database
    -tgttdekeystorepasswd for the target database
    ```


    * Using the following response file parameters: [WALLET_TDEKEYSTORE](https://docs.oracle.com/en/database/oracle/zero-downtime-migration/21.5/zdmug/zero-downtime-migration-hybrid-migration-response-file-parameters-reference.md#GUID-36D5138C-0A48-4218-95B4-2BFB225419F4) parameter for the source database and the [WALLET_TGTTDEKEYSTORE](https://docs.oracle.com/en/database/oracle/zero-downtime-migration/21.5/zdmug/zero-downtime-migration-hybrid-migration-response-file-parameters-reference.md#GUID-A00CF768-337E-4CBC-B552-3B10DB58D303) parameter for the target database. 
    
    > **note:** Unencrypted sources will not be encrypted at target so you must perform the encryption post migration. 

* Character set related limitations

    * Ensure that the source and target databases have compatible characterset. (Same characterset and NLS character set.)
    * Ensure that the source charset is a subset of the target charset (and no clob column exists).
* For source databases: Set the source compatible version to the same or a lower version than the target. If the source tablespaces are encrypted, then you must provide the source and target TDE password.
* For target databases

    * Ensure that the connectivity exists and provide a shared NFS/cluster file system.
    * The target CDB must not have tablespace with the same name as source. Any source tablespace to be migrated should not exist on the target, else the datapump import will fail.
    * The source database must be at least Oracle Database 11.2.0.4.
    * This feature will only be available in Oracle Database 19c (19.24). 

    For the target database, ensure that it contains the fix available in 35564253.

* `SYSDBA` is needed for hybrid migration source and target database users. This is because ZDM uses RMAN to run tablespace metadata import/export. 



Following are the workflow phases as well as their brief description in order of their execution:

Phase name | Description  
---|---  
ZDM_VALIDATE_TGT | Performs validations at the target  
ZDM_VALIDATE_SRC | Perform validations at the source  
ZDM_SETUP_XTTS_SRC | Sets up Zero Downtime Migration helper modules on the source server  
ZDM_SETUP_XTTS_TGT | Sets up Zero Downtime Migration helper modules on the target server  
ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC | Validates the export directory object (if applicable), and checks for sufficient space and permission for specified user in the source database to export dumps.   
ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT | Verifies that the Data Pump import directory object exists.  
ZDM_VALIDATE_XTTS_SRC | Performs XTTS related validations at the source such as self-containment of tablespaces  
ZDM_VALIDATE_XTTS_TGT | Performs XTTS related validations at the target such as ensuring tabelspaces names are not in use already which are part of migration  
ZDM_XTTS_BACKUP_FULL_SRC | Performs full backup of the sourcetablespaces (Initial L0)  
ZDM_XTTS_BACKUP_INCR_SRC | Second backup of the source tablespaces (First L1)  
ZDM_XTTS_COPYFILES | Copies files needed to perform restore at target such as source discovery file, transport xml, any TDE wallets.  
ZDM_XTTS_RESTORE_FULL_TGT | Performs Initial restore of the tablespaces at the target  
ZDM_XTTS_BACKUP_INCR_RO_SRC | Sets the tablespaces to read only and takes the third & final backup of the tablesapces (Second L1). Additionally RMAN exports the datapump dump of the metadata of tablespaces  
ZDM_XTTS_COPYFILES_RO | Copies files needed to perform restore at target such as the latest transport xml and TDE wallets to account for any changes to the wallet  
ZDM_PREPARE_DATAPUMP_SRC | Creates a new directory object for Data Pump, if required.  
ZDM_PREPARE_DATAPUMP_TGT | Creates a new directory object for Data Pump, if required.  
ZDM_DATAPUMP_EXPORT_METADATA_SRC | Performs export any additional source meta data other than the tablespace meta data exported by RMAN datapump execution such as Users, PL/SQL, Triggers, and so on.  
ZDM_TRANSFER_DUMPS_METADATA_SRC |   
ZDM_DATAPUMP_IMPORT_USER_TGT | Imports Users and Profiles as the first import so that the subsequent tablespace import can succeed  
ZDM_XTTS_RESTORE_INCR_TGT | Executes RMAN to perform the final restore and import of the tablespace metadata exported by RMAN during ZDM_XTTS_BACKUP_INCR_RO_SRC phase  
ZDM_DATAPUMP_IMPORT_TGT | Imports any additional metadata other than users, profiles & tablespaces not performed in previous imports  
ZDM_POST_DATAPUMP_SRC | Removes any Data Pump directory object created by Zero Downtime Migration  
ZDM_POST_DATAPUMP_TGT | Fixes any invalid objects in the target database.   
ZDM_REFRESH_MVIEW_TGT |   
ZDM_POST_ACTIONS |   
ZDM_CLEANUP_SRC | Performs clean up at the source such as removing helper modules copied during the setup phase  
ZDM_CLEANUP_TGT | Performs clean up at the target such as removing helper modules copied during the setup phase  

The following parameters are applicable for hybrid migration: 

* `MIGRATION_METHOD=OFFLINE_XTTS `
* `DATA_TRANSFER_MEDIUM=NFS `
* `SOURCEDATABASE_CONNECTIONDETAILS_HOST`
* `SOURCEDATABASE_CONNECTIONDETAILS_PORT`
* `SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME`
* `SOURCEDATABASE_ADMINUSERNAME`
* `TARGETDATABASE_CONNECTIONDETAILS_HOST`
* `TARGETDATABASE_CONNECTIONDETAILS_PORT`
* `TARGETDATABASE_CONNECTIONDETAILS_SERVICENAME`
* `TARGETDATABASE_ADMINUSERNAME`
* `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_NAME`
* `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_PATH`
* `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME`
* `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_PATH`
* `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXPORTPARALLELISMDEGREE`
* `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_IMPORTPARALLELISMDEGREE`
* `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_NOCLUSTER=FALSE `
* `DATAPUMPSETTINGS_EXPORTVERSION`
* `DUMPTRANSFERDETAILS_PUBLICREAD=FALSE `
* `IGNOREEXPORTERRORS`
* `IGNOREIMPORTERRORS`
* `RMANSETTINGS_BACKUPTAG`
* `RMANSETTINGS_COMPRESSIONALGORITHM=MEDIUM `
* `RMANSETTINGS_PUBLICREAD=FALSE `
* `RMANSETTINGS_RETENTIONWINDOW`
* `RMANSETTINGS_SECTIONSIZE=64G `
* `RMANSETTINGS_SRCCHANNELS=10 `
* `RMANSETTINGS_TGTCHANNELS=10 `
* `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE`
* `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP`
* `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT`
* `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP`
* `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME`
* `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME`
* `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT`
* `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL`
* `SOURCEDATABASE_ENVIRONMENT_DBTYPE=ORACLE `
* `SOURCEDATABASE_ENVIRONMENT_NAME=ORACLE `
* `TABLESPACEDETAILS_EXCLUDE= `
* `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE`
* `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP`
* `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT`
* `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP`
* `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME`
* `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME`
* `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT`
* `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL`
* `TARGETDATABASE_DBTYPE`
* `WALLET_DATAPUMPENCRYPTION`
* `WALLET_SOURCEADMIN`
* `WALLET_TARGETADMIN`
* `WALLET_TDEKEYSTORE`
* `WALLET_TGTTDEKEYSTORE`
* `WALLET_USERACTION`


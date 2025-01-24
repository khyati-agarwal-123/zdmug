## DZero Downtime Migration Process Phases {#GUID-DA18E66B-2943-4C67-91B7-0CF3E0B0DC6B}

The migration job process in Zero Downtime Migration runs in operational phases as a work flow. The tables below describe the phases for physical and logical migrations.

Example D-1 Listing Zero Downtime Migration Process Phases

Run the ZDMCLI `migrate database` command with the `-listphases` option to list the operational phases for your migration job, as shown here. 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -sourcedb zdmsdb
-sourcenode ocicdb1 -srcauth zdmauth -srcarg1 user:opc
-srcarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-srcarg3 sudo_location:/usr/bin/sudo -targetnode ocidb1
-backupuser backup_user@example.com
-rsp /u01/app/zdmhome/rhp/zdm/template/zdm_template_zdmsdb.rsp
-tgtauth zdmauth -tgtarg1 user:opc
-tgtarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-tgtarg3 sudo_location:/usr/bin/sudo **-listphases**
```


**Table D-1 Physical Migration Phase Descriptions**

Phase name | Online/Offline | Useraction scope | Description  
---|---|---|---  
ZDM_GET_SRC_INFO |  Both | SOURCE |  Gets information about the source database  
ZDM_GET_TGT_INFO |  Both | TARGET |  Gets information about the target database  
ZDM_SETUP_SRC |  Both | SOURCE |  Sets up Zero Downtime Migration helper modules on the source server  
ZDM_SETUP_TGT |  Both | TARGET |  Sets up Zero Downtime Migration helper modules on the target server  
ZDM_SETUP_STBY | Online  | TARGET | Sets up Zero Downtime Migration helper modules on the target standby server.  
ZDM_GET_STBY_INFO | Online  | TARGET | Gets information about the source database.  
ZDM_PRECHECKS_STBY | Online  | TARGET | Performs preliminary validation at the target standby such as connectivity validation to source scan, db_name, character set validation.  
ZDM_PREUSERACTIONS_STBY  | Online  | TARGET | Runs migration pre-user actions, if any, at the target standby  
ZDM_OBC_INST_STBY | Online  | TARGET | Installs Oracle Database Cloud Backup Module at the target standby  
ZDM_VALIDATE_STBY | Online  | TARGET | Performs validations at the target standby.  
ZDM_BACKUP_INCREMENTAL_STBY_SRC | Online  | TARGET | Performs incremental backup of the source database before performing target standby full restore.  
ZDM_COPYFILES_TGT_STBY | Online  | TARGET |  Offline Copies Oracle password file and TDE wallets from source to target standby.  
ZDM_PREPARE_STBY | Online  | TARGET | Prepares target standby for Data Guard standby creation  
ZDM_SETUP_TDE_STBY | Online  | TARGET | Copies TDE wallet files from the source to the target standby keystore location.  
ZDM_CLONE_STBY | Online  | TARGET | Instantiates Target Standby and creates Data Guard standby.  
ZDM_DUPLICATE_TGT_STBY | Online  | TARGET | Duplicates Source database at Target Standby and creates Data Guard standby.  
ZDM_RESTORE_TGT_STBY | Online  | TARGET | Restores from service Target Standby to create Data Guard standby.  
ZDM_RECOVER_TGT_STBY | Online  | TARGET | Recovers from service Target Standby to create Data Guard standby.  
ZDM_FINALIZE_STBY | Online  | TARGET | Finalizes Data Guard standby preparation of the target standby. Converts the target standby database to RAC if originally provisioned as Oracle RAC.  
ZDM_POST_MIGRATE_TGT_STBY  | Online  | TARGET | Performs any post database migration tasks such restoring DBA directories at target standby.  
ZDM_POSTUSERACTIONS_STBY | Online  | TARGET |  Performs any post-migration user actions at the target standby.  
ZDM_CLEANUP_STBY  | Online  | TARGET | Performs clean up at the target standby.  
ZDM_GET_STBY_INFO | Online  | TARGET | Standby - Gets information about the target standby database.  
ZDM_DUPLICATE_TGT | Online  | TARGET | Instantiates the target database using RMAN active duplicate.  
ZDM_PREUSERACTIONS |  Both | SOURCE |  Runs migration pre-user actions, if any, at the source  
ZDM_PREUSERACTIONS_TGT |  Both | TARGET |  Runs migration pre-user actions, if any, at the target  
ZDM_VALIDATE_TGT |  Both | TARGET |  Performs validations at the target  
ZDM_OBC_INST_SRC |  Both | SOURCE |  Installs Oracle Database Cloud Backup Module at the source  
ZDM_OBC_INST_TGT |  Both | TARGET |  Installs Oracle Database Cloud Backup Module at the target  
ZDM_BACKUP_FULL_SRC |  Both | SOURCE |  Performs full backup of the source database  
ZDM_BACKUP_INCREMENTAL_SRC |  Both | SOURCE |  Performs incremental backup of the source database  
ZDM_DISCOVER_SRC  |  Both | SOURCE |  Performs database discovery at the source for setting up Data Guard  
ZDM_COPYFILES  |  Both | SOURCE |  Copies Oracle password file and TDE wallets from source to target  
ZDM_SETUP_TDE_TGT |  Both | TARGET |  Copies TDE wallet files from the source to the target keystore location  
ZDM_RESTORE_TARGET |  Online | TARGET |   
ZDM_RECOVER_TARGET |  Online | TARGET |   
ZDM_OSS_RESTORE_TGT |  Offline | TARGET |  Performs full database restore  
ZDM_BACKUP_DIFFERENTIAL_SRC |  Offline | SOURCE |  Performs differential backup of the source database  
ZDM_OSS_RECOVER_TGT |  Offline | TARGET |  Performs incremental restore, recovery database, and opens database with reset logs  
ZDM_PREPARE_TGT  |  Both | TARGET |  Prepares target for Data Guard standby creation  
ZDM_CLONE_TGT  |  Online | TARGET |  Creates Data Guard standby from the Cloud backup  
ZDM_FINALIZE_TGT  |  Both | TARGET |  Finalizes Data Guard standby preparation of the target. Converts the target database to RAC if originally provisioned as Oracle RAC.  
ZDM_CONFIGURE_DG_SRC |  Online | SOURCE |  Registers the Cloud standby with the source  
ZDM_MONITOR_APPLY_LAG |  Online | SOURCE |  When the job pauses at ZDM_CONFIGURE_DG_SRC phase, ZDM monitors apply lag periodically in this phase at specified interval.<br>  > **note:** This phase is applicable only when value of ZDM_APPLY_LAG_MONITORING_INTERVAL is not `None`.   
ZDM_SWITCHOVER_SRC  |  Online | SOURCE |  Initiates switchover actions at the source  
ZDM_SWITCHOVER_TGT  |  Online | TARGET |  Completes switchover actions at the target  
ZDM_POST_DATABASE_OPEN_TGT |  Both | TARGET |  Performs activities after database is opened, such as restore pluggable database state, DBA directories, RMAN configuration  
ZDM_DATAPATCH_TGT |  Both | TARGET |  Runs datapatch at the target  
ZDM_SHUTDOWN_SRC |  Both | SOURCE |  Shuts down source database at the end of the migration  
ZDM_POSTUSERACTIONS  |  Both | SOURCE |  Performs any post-migration user actions at the source  
ZDM_POSTUSERACTIONS_TGT |  Both | TARGET |  Performs any post-migration user actions at the target  
ZDM_CLEANUP_SRC |  Both | SOURCE |  Performs clean up at the source  
ZDM_CLEANUP_TGT |  Both | TARGET |  Performs clean up at the target  
TIMEZONE_UPGRADE_PREPARE_TGT | Both | TARGET |  Performs prepare upgrade operation when the timezone version of the source database is less than the timezone version of the target database. <br>Skip this phase if the timezone version is same. > **note:** This phase is applicable only when value of the ZDM_TGT_UPGRADE_TIMEZONE parameter is TRUE.   
TIMEZONE_UPGRADE_TGT | Both | TARGET |  Performs timezone upgrade operation when the timezone version of the source database is less than the timezone version of the target database. <br>Skip this phase if the timezone version is same. > **note:** This phase is applicable only when value of the ZDM_TGT_UPGRADE_TIMEZONE parameter is TRUE.   
ZDM_RESTORE_TGT | Offline | TARGET | Performs full restore of the database.  
ZDM_RECOVER_TGT | Offline | TARGET | Performs incremental restore of the database.  
ZDM_MANIFEST_TO_CLOUD | Both (ExaCS/ExaCC only) | SOURCE | Updates dbaas wallet to update TDE wallet password and sys password that dbaas tooling depends on for any lifecycle operation.  
ZDM_NONCDBTOPDB_PRECHECK | Both (non-cdb to pdb conversion use case only) | SOURCE | For the migrations involving conversion to CDB, this pase executes $DB_HOME/rdbms/admin/noncdb_to_pdb.sql in precheck mode to identify any incompatibility between non-cdb and target CDB.  
ZDM_NONCDBTOPDB_CONVERSION | Both (non-cdb to pdb conversion use case only) | SOURCE | Performs unplug/plug of the non-cdb migrated from the source to convert it to a PDB in target CDB by executing $DB_HOME/rdbms/admin/noncdb_to_pdb.sql .  
ZDM_POST_MIGRATE_TGT | Both | TARGET | Performs any post database migration tasks such restoring PDB state, DBA directories.  
ZDM_COPYFILES_DIFFERENTIAL | Offline | SOURCE | Offline Copies Oracle password file and TDE wallets from source to target. This is identical to ZDM_COPYFILES, this is additional copy of the TDE wallet and password file done to ensure that the target has the latest encryption keys in case if the keys got updated at the source before differential backup   
ZDM_PRECHECKS_SRC | Both | SOURCE | Performs preliminary validation at the source such as connectivity validation to target scan .  
ZDM_PRECHECKS_TGT | Both | TARGET | Performs preliminary validation at the target such as connectivity validation to source scan, db_name, character set validation.   

**Table D-2 Logical Migration Phase Descriptions**

Phase name | Online/Offline | Useraction scope | Description  
---|---|---|---  
ZDM_VALIDATE_TGT |  Both | TARGET |  Verifies that the target database exists, discovers the database type, and validates access credentials, security, and connectivity.<br>For an Autonomous Database target, sets up the access to the target database from the Zero Downtime Migration node by downloading the target database access wallet with OCI REST services and discovers the target OCPU.<br>Online only: verifies `ggadmin` user privileges.   
ZDM_VALIDATE_SRC |  Both | SOURCE |  Validates the source database access credentials, database parameter settings.<br>Online only: verifies `ggadmin` user privileges and GoldenGate capture support for objects in source database. <br>Online only: ZDM now ensures that same GoldenGate schema name is used for source and configuration file `GLOBALS`.   
ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC |  Both | SOURCE |  Validates the export directory object (if applicable), and checks for sufficient space and permission for specified user in the source database to export dumps. Checks if the specified Oracle Cloud Object Store buckets, data bucket, and wallet bucket are accessible from the source. Also validates the proxy configuration if applicable.  
ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT |  Both | TARGET |  Verifies that the Data Pump import directory object exists. <br>If a pre-existing DBLINK was specified, checks if it exists and is valid, and ensures that the Autonomous Database requirements for the DBLINK and wallet files are met.<br>For a co-managed target database, ensures that the OCI OSS data bucket and wallet bucket are valid and accessible from the target server. Ensures that the local path to download the dump is valid and has sufficient space.  
ZDM_PREPARE_DATAPUMP_SRC |  Both | SOURCE |  Creates a new directory object for Data Pump, if required. Creates OCI Auth Token to access OCI OSS bucket if required.  
ZDM_DATAPUMP_ESTIMATE_SRC |  Both | SOURCE |  Performs Data Pump export dump size estimation.<br>See also, [DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ESTIMATEBYSTATISTICS](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-71A1EFA0-BB9B-4962-A785-7B3EBE0FE99E). <br>Not used with DBLINK  
ZDM_PREPARE_DATAPUMP_TGT |  Both | TARGET |  Creates a new directory object for Data Pump, if required. Stores OCI Auth token in the database for secure OSS access. <br>If migrating via DBLINK, and a DBLINK must be created, creates the necessary database credentials to access the source and create a new DBLINK. <br>Ensures Autonomous Database security requirements are met using DBLINK over SSL.  
ZDM_PARALLEL_EXPORT_IMPORT |  Offline | NA |  When using parallel schema migration, this phase, is cumulative of the ZDM_DATAPUMP_EXPORT_SRC, ZDM_UPLOAD_DUMPS_SRC, and ZDM_DATAPUMP_IMPORT_TGT phases, and handles all three actions for a sublist of schemas per thread, that is export, transfer, and import the sublist of schemas in parallel.  
ZDM_DATAPUMP_EXPORT_SRC |  Both | SOURCE |  Starts and monitors the Data Pump Export on the source database.  
ZDM_UPLOAD_DUMPS_SRC |  Both | SOURCE |  Uploads Data Pump dump files from the source to OCI OSS.  
ZDM_DATAPUMP_IMPORT_TGT |  Both | TARGET |  Starts import of Data Pump Dumps to the target database, either from the OCI OSS bucket or via DBLINK, and monitors the Data Pump import progress.  
ZDM_DATAPUMP_IMPORT_METADATA_TGT |  Both | TARGET |  Imports the generated METADATA_ONLY Dumps in the target database.  
ZDM_DATAPUMP_IMPORT_DATA_TGT |  Both | TARGET |  Imports the generated DATA_ONLY Dumps in the target database.  
ZDM_DATAPUMP_IMPORT_OPTIMISE_TGT |  Both | TARGET |  Imports INDEX and STATISTICS by using the METADATA_ONLY dumps.  
ZDM_POST_DATAPUMP_SRC |  Both | SOURCE |  Removes any Data Pump directory object created by Zero Downtime Migration.  
ZDM_POST_DATAPUMP_TGT |  Both | TARGET |  Fixes any invalid objects in the target database. Removes the database access and OCI OSS access credentials that were created for the migration. Removes any DBLINK created by Zero Downtime Migration. Optionally, removes source database dumps stored in OCI OSS bucket.  
ZDM_POST_ACTIONS |  Both | NA |  Removes Autonomous Database access wallet from the Zero Downtime Migration node.  
ZDM_PRE_MIGRATION_ADVISOR |  Both | NA |  Runs the Oracle Database Premigration Advisor Tool on the migration job.  
ZDM_VALIDATE GG_HUB |  Online | NA |  Verifies GoldenGate Microservices REST endpoints, software configuration, health, and connectivity to the source and target databases.  
ZDM_PREPARE_GG_HUB |  Online | NA |  Registers database connection details and credentials with GoldenGate Microservices.  
ZDM_ADD_HEARTBEAT_SRC |  Online | SOURCE |  Creates GoldenGate heartbeat table in the source database. If the table already exists, sets update frequency to 60 seconds.  
ZDM_ADD_SCHEMA_TRANDATA_SRC |  Online | SOURCE |  Prepares the source database schemas for instantiation by enabling schema level supplemental logging.  
ZDM_CREATE_GG_EXTRACT_SRC |  Online | SOURCE |  Starts the GoldenGate Extract process at the source database  
ZDM_ADD_HEARTBEAT_TGT |  Online | TARGET |  Creates the GoldenGate heartbeat table in the target database. If the table already exists, sets update frequency to 60 seconds.  
ZDM_ADD_CHECKPOINT_TGT |  Online | TARGET |  Creates GoldenGate checkpoint table in the target database to track Replicat progress.  
ZDM_CREATE_GG_REPLICAT_TGT |  Online | TARGET |  Creates GoldenGate Replicat process for the target database.  
ZDM_START_GG_REPLICAT_TGT |  Online | TARGET |  Starts GoldenGate Replicat process for the target database.  
ZDM_MONITOR_GG_LAG |  Online | NA |  Polls the GoldenGate checkpoint and heartbeat data to measure end-to-end apply lag until lag decreases below desired threshold.  
ZDM_SWITCHOVER_APP |  Online | TARGET |  Performs any manual validation of the target database if required and performs the switchover for the target database.  > **note:** No action is performed by the Zero Downtime Migration workflow.   
ZDM_POST_SWITCHOVER_TGT |  Online | TARGET |  Performs post-switchover actions for the target database.  
ZDM_REFRESH_MVIEW |  Online and Offline | TARGET |  Refreshes the materialized views post migration.  
ZDM_PREPARE_SWITCHOVER_APP |  | SOURCE |  Performs the following functions: <br>1. Monitors and wait for the ongoing workload on source database to be completed and the source database to become idle.<br>2. Stops GoldenGate Extract.<br>3. Waits for GoldenGate Replicat to complete the Replicat apply process.<br>Stops GoldenGate Replicat.
ZDM_ADVANCE_SEQUENCES |  |  |  Performs the following functions: <br>1. Queries the sequence values in the source database.<br>2. Advances the sequences in the target database. All sequences are advanced as part of this phase except the sequences matching the following queries:<br>```select name\|\|'_SEQ' from sys.dba_queues select name\|\|'_SHSEQ' from sys.dba_queues sequences which have secondary = 'Y' in dba_objects sequences which starts with 'ISEQ$$_'```
ZDM_RM_GG_EXTRACT_SRC |  Online | SOURCE |  Deletes GoldenGate Extract process on source database  
ZDM_RM_GG_REPLICAT_TGT |  Online | TARGET |  Deletes GoldenGate Replicat process on target database  
ZDM_DELETE_SCHEMA_TRANDATA_SRC |  Online | SOURCE |  Disables schema level supplemental logging on source database  
ZDM_RM_HEARTBEAT_SRC |  Online | SOURCE |  Drops the GoldenGate heartbeat table in source database, if the table was created by Zero Downtime Migration. Otherwise, resets update frequency to original setting.  
ZDM_RM_CHECKPOINT_TGT |  Online | TARGET |  Drops the GoldenGate checkpoint table in the target database.  
ZDM_RM_HEARTBEAT_TGT |  Online | TARGET |  Drops the GoldenGate heartbeat table in the target database, if the table was created by Zero Downtime Migration. Otherwise, resets the update frequency to the original value.  
ZDM_CLEAN_GG_HUB |  Online | NA |  Deletes the database connection details and credentials saved with GoldenGate Microservices
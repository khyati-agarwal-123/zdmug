## HZero Downtime Migration ZDMCLI Command Reference {#GUID-A9ADB5E5-DA46-498A-91C8-CB2D863DF43A}

ZDMCLI is the command line interface for Zero Downtime Migration migration operations.

To run ZDMCLI commands, go to the `/bin` directory in the Zero Downtime Migration software home and enter one of the commands listed in the topics below. For example: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
```


To list help pages for any ZDMCLI command use the `-help` option. The following command lists the help for all of the ZDMCLI commands. 
```
zdmuser> $ZDM_HOME/bin/zdmcli -help
```


To get the current release information for your Zero Downtime Migration software, run ZDMCLI with the `-build` option. 
```
zdmuser> $ZDM_HOME/bin/zdmcli -build
```


The following topics describe the Zero Downtime Migration ZDMCLI command usage and options.

### ZDMCLI Options {#GUID-8BDC6003-BC3B-4D1C-8A85-3B2B9B5C981E}

You can run options on ZDMCLI without specifying a command.

Syntax
```
$ZDM_HOME/bin/zdmcli -option
```


Options

**Table H-1 ZDMCLI Options**

Option | Description  
---|---  
-build | Returns Zero Downtime Migration version information, including Cloud Premigration Advisor version.  

### abort job {#GUID-A41AE395-56D0-4BA6-8E69-F65A5D38BFED}

Terminates the specified job, if running.

Syntax
```
$ZDM_HOME/bin/zdmcli abort job
-jobid job_id
```


Options

**Table H-2 ZDMCLI abort job Options**

Option | Description  
---|---  
-jobid *`job_id`* | Unique job ID value (integer) for the scheduled job. The job ID is assigned when the job is scheduled.  

### add imagetype {#GUID-62D4F291-B55B-416C-A5CE-8F73AB29C96E}

Configures a new image type of the specified name and its associated user actions.

Syntax
```
$ZDM_HOME/bin/zdmcli add imagetype
-imagetype image_type
-basetype CUSTOM_PLUGIN
[-useractions user_action_list]
```


Options

**Table H-3 ZDMCLI add imagetype Options**

Option | Description  
---|---  
`-imagetype *`image_type`*` |  Name of the image type to be created  
`-basetype CUSTOM_PLUGIN` |  The base image type for which the image type is created.<br>Note that `CUSTOM_PLUGIN` is the only valid value for this mandatory argument.   
`-useractions *`user_action_list`*` |  Comma-separated list of user action names  

### add useraction {#GUID-A37D4FCB-A617-4E64-9866-E22D51959DF3}

Configures a new user action of the specified name with its associated script and action file.

Syntax
```
$ZDM_HOME/bin/zdmcli add useraction
-useraction user_action_name
-actionscript script_name
[-actionfile file_name]
{-pre \| -post}
-optype MIGRATE_DATABASE
[-phase operation_phase]
[-onerror {ABORT \| CONTINUE}]
[-runscope
{ONENODE \|
ALLNODES \|
AUTO}]
[-outputfrom useraction_names]
-asuser db_user
```


Options

**Table H-4 ZDMCLI add useraction Options**

Option | Description  
---|---  
`-useraction *`user_action_name`*` |  Name of the user action  
`-actionscript *`script_name`*` |  Script file to be run  
`-actionfile *`file_name`*` |  File associated with and needed by the user action  
`-pre` |  Runs the user action before the operation  
`-post` |  Runs the user action after the operation  
`-optype MIGRATE_DATABASE` |  Defines the operation for which the user action is configured as `MIGRATE_DATABASE`.   
`-phase *`phase_of_operation`*` |  Migration operation phase for which the user action is configured  
`-onerror {ABORT \| CONTINUE}` |  The response if the user action encounters an error during execution  
`-runscope {ONENODE \| ALLNODES \| AUTO}` |  The servers on which the user action is run. <br>Specify *`AUTO`* to choose the run scope based on the other command options.   
`-outputfrom *`useraction_names`*` |  Comma-separated list of user action names whose output is to be supplied to the current user action.  
`-asuser *`db_user`* ` |  Specify the database user to run SQL commands in the user action. See [WALLET_USERACTION](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-38FD145F-B908-48A8-ADDA-2A92DADFB3E3) for specifying the password required for processing user action as the specified user.   

Examples

For more examples, see [Registering User Actions](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-56DA9423-2DD4-4CC9-8F82-5F594753F23D). 

### migrate database {#GUID-37ABF830-2FC3-4F71-9132-DF05DCFABBB9}

Performs a migration of a database to the Oracle Cloud.

Syntax
```
$ZDM_HOME/bin/zdmcli migrate database
[-sourcedb source_db_unique_name_value |
-sourcesid source_oracle_sid}
-rsp zdm_template_path
-sourcenode source_host_name
-targetnode target_host_name
[-targethome target_home]
[-eval]
[-tdekeystorepasswd
[-tgttdekeystorepasswd]]
[-tdemasterkey]
[-useractiondata user_action_data]
[-imagetype]
[-backupuser user_name
[-restoreuser user_name]]
[-backuppasswd]
[-dvowner database_vault_owner]
[-tdewallet wallet_path
[-tgttdewallet wallet_path]]
[-tdekeystorewallet tde_wallet_path
[-tgttdekeystorewallet tde_wallet_path]]
[-sourcesyswallet sys_wallet_path]
[-osswallet oss_wallet_path
[-ossrestorewallet oss_restore_wallet_path]]
[-dvwallet dv_wallet_path]
[-backupwallet backup_wallet_path]
[{-srcroot \|
-srccred cred_name \|
-srcuser user_name \|
{-srcsudouser sudo_user_name -srcsudopath sudo_binary_path} |
{-srcauth plugin_name
[-srcarg1 user:source_database_server_login_user_name
-srcarg2 identity_file:ZDM_installed_user_private_key_file_location
-srcarg3 sudo_location:sudo_location]}}]
{-tgtroot \|
-tgtcred cred_name \|
-tgtuser user_name \|
{-tgtsudouser sudo_user_name -tgtsudopath sudo_binary_path} |
{-tgtauth plugin_name
[-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:sudo_location]}}
[-schedule {timer_value \| NOW}]
[-pauseafter phase]
[-stopafter phase]
[-listphases]
[-ignoremissingpatches patch_name
[,patch_name...]]
[-ignore
[ALL]
[,WARNING]
[,PATCH_CHECK]
[,NLS_CHECK]
[,NLS_NCHAR_CHECK]
[,ENDIAN_CHECK]
[,VAULT_CHECK]
[,DB_NK_CACHE_SIZE_CHECK]]]
[-incrementalinterval timer_minutes]
[-advisor |
-ignoreadvisor |
-skipadvisor]
[-genfixup {YES \| NO}
[-srcred src_dbuser_wallet
[-tgtcred tgt_db_user_wallet]
```


Options

**Table H-5 ZDMCLI migrate database Options**

Option | Description  
---|---  
`-advisor` |  Similar to `-eval`, `migrate database` will run the minimum phases required for exclusively running Cloud Premigration Advisor Tool (CPAT) on the migration job, without actually running the migration job against the source and target   
`-ignoreadvisor` |  Runs Cloud Premigration Advisor Tool (CPAT), but ignores errors generated by CPAT.  
`-skipadvisor` |  Skips the CPAT phase of the migration job  
`-backuppasswd *`password`*` |  In a physical migration, allows you to<br>1\. Create an RMAN backup with provided backup password <br>2\. Use an existing RMAN backup with backup password authentication  
`-backupuser *`user_name`*` |  Name of the user allowed to back up or restore the database  
`-restoreuser *`user_name`*` |  Name of the user allowed to restore the database  
`-backupwallet *`backup_wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host containing the RMAN backup password  
`-dvwallet *`dv_wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host, containing the credential for the Oracle Database Vault owner  
`-osswallet *`oss_wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host containing the credential for the Object Storage Service (OSS) backup user  
`-ossrestorewallet *`oss_restore_wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host containing the credential for the Object Storage Service (OSS) restore backup user  
`-sourcesyswallet *`sys_wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host containing the `SYS` password of the source database   
`-tdewallet *`wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host containing the Transparent Data Encryption (TDE) keystore password  
`-tgttdewallet *`wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host containing target container database Transparent Data Encryption (TDE) keystore password<br>If you are converting a non-multitenant source database to a multitenant architecture on the target, that is a pluggable database (PDB), then you can also create an auto-login wallet for the target container database (CDB) TDE keystore password.  
`-dvowner *`database_vault_owner`*` |  Name of the Oracle Database Vault owner allowed to grant or revoke SYS privileges  
`-eval` |  Evaluates the migration job without actually running the migration job against the source and target  
`-genfixup {YES \| NO}` |  Displays remedial scripts for failing checks when a migration is run with Cloud Premigration Advisor Tool (CPAT) enabled. Any remedies that can be run automatically on the source database are run.<br>See [Cloud Premigration Advisor Tool Support](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMRN-GUID-893235FB-5035-43BB-8E5B-0C3787ACE348) for CPAT use cases supported by Zero Downtime Migration.   
`-ignore [ALL][,WARNING][,PATCH_CHECK][,NLS_CHECK][,NLS_NCHAR_CHECK][,ENDIAN_CHECK][,VAULT_CHECK][,DB_NK_CACHE_SIZE_CHECK]` |  Ignore all checks or specific type of checks:<br>`ALL` \- ignore all checks <br>`WARNING` \- ignore checks with warning status <br>`PATCH_CHECK` \- patch level should be the same <br>`NLS_CHECK` \- source and target database NLS should be same <br>`NLS_NCHAR_CHECK` \- source and target database NLS NCHAR should be same <br>`NLS_NCHAR` \- source and target database NLS should be same <br>`ENDIAN_CHECK` \- source and target database Endian should be same <br>`VAULT_CHECK` \- vault enabled should be consistent for source and target <br>`DB_NK_CACHE_SIZE_CHECK`\- set DB_NK_CACHE_SIZE in the target database if it is set in the source database.   
`-ignoremissingpatches` |  Proceeds with the migration even though the specified patches, which are present in the source path or working copy, might be missing from the destination path or working copy  
`-imagetype *`image_type`*` |  Name of the user action imagetype  
`-incrementalinterval *`timer_minutes`*` |  Run periodic incremental RMAN backup every interval (in minutes)  
`-listphases` |  Lists the phases for the migration job  
`-pauseafter *`phase`*` |  Pause the job after running the specified phase  
`-rsp *`zdm_template_path`*` |  Location of the Zero Downtime Migration response file  
`-schedule *`timer_value`*` |  Scheduled time to run the migration, in ISO-8601 format.<br>For example, `2016-12-21T19:13:17+05`  
`-sourcedb *`source_db_unique_name_value`*` |  the `DB_UNIQUE_NAME` of the source database you want to migrate   
`-sourcenode *`source_host_name`*` |  Host on which the source database is running  
`-sourcesid *`source_oracle_sid`*` |  `ORACLE_SID` of the source single instance database without Grid Infrastructure   
`-srcauth *`plug-in_name`* [*`plug-in_args`*]``-srcauth sudoasuser` |  Use the `zdmauth` or `dbuser` authentication plug-in to access the source database server, and enter the following arguments: <br>```-srcarg1 user:source_database_server_login_user_name -srcarg2 identity_file:ZDM_installed_user_private_key_file_location -srcarg3 sudo_location:sudo_location```<br> If you don't specify the sudo location, the default (/usr/bin/sudo) is used by Zero Downtime Migration.<br>See [Migrating with Database User Privileges ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C03E5EBB-CA6D-4FDA-83D8-0591158E7068) for more information related to `dbuser` authentication. <br>Perform super user operations as `sudoasuser` user name on the source database server. 
`-srccred *`cred_name`*` |  Credential name with which to associate the user name and password credentials to access the source database server  
`-srcroot` |  Directs Zero Downtime Migration to use `root` credentials to access the source database server   
`-srcsudopath *`sudo_binary_path`*` |  Location of `sudo` binary on the source database server   
`-srcsudouser *`user_name`*` |  Perform super user operations as `sudo` user name on the source database server   
`-srcuser *`user_name`*` |  Name of the privileged user performing operations on the source database server  
`-stopafter *`phase`*` |  Truncates the work flow by the specified phase, and upon completion of the specified phase, the migration job is marked with status completed.<br>There is no option to resume the job beyond this phase.<br>For example, if you intend to stop the work flow after Data Guard setup, then specifying `-stopafter ZDM_CONFIGURE_DG_SRC` stops the job at this phase and the job is marked completed once the `ZDM_CONFIGURE_DG_SRC` is completed successfully.   
`-targethome *`target_home`*` |  Location of the target database `ORACLE_HOME`  
`-targetnode *`target_host_name`*` |  Target server to which the source database is migrated  
`-tdekeystorepasswd` |  Transparent Data Encryption (TDE) keystore password, required for password-based keystore or wallet  
`-tdekeystorewallet *`tde_wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host containing the Transparent Data Encryption (TDE) keystore password  
`-tdemasterkey` |  Transparent Data Encryption (TDE) master encryption key  
`-tgtauth *`plugin_name`* [*`plugin_args`*]``-tgtauth sudoasuser ` |  Use the `zdmauth` authentication plug-in to access the target database server, and enter the following arguments: ```-tgtarg1 user:target_database_server_login_user_name -tgtarg2 identity_file:ZDM_installed_user_private_key_file_location -tgtarg3 sudo_location:sudo_location```<br>If you don't specify the sudo location the default (/usr/bin/sudo) is used by Zero Downtime Migration.<br>Name of the sudoasuser performing operations on the target database server.  
`-tgtcred *`cred_name`*` |  Credential name with which to associate the user name and password credentials to access the target database server  
`-tgtroot` |  Use `root` credentials to access the target database server   
`-tgtsudopath *`sudo_binary_path`*` |  Location of the `sudo` binary on the target database server   
`-tgtsudouser *`user_name`*` |  Perform super user operations as `sudo` user name on the target database server   
`-tgttdekeystorepasswd` |  Target container database TDE keystore password  
`-tgttdekeystorewallet *`tde_wallet_path`*` |  Full path for the auto-login wallet file on the Zero Downtime Migration host that contains the target TDE keystore password  
`-tgtuser *`user_name`*` |  Name of the user performing operations on the target database server  
`-useractiondata *`user_action_data`*` |  Value to be passed to *`useractiondata`* parameter of the user action script   
`-srcred *``*` |  Used for root user and sudo user to make connections to the source node.  
`-tgtcred *``*` |  Used for root user and sudo user to make connections to the target node.  

Examples

ZDMCLI `migrate database` options for an Autonomous Database migration: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -rsp file_path -sourcenode host
-srcauth zdmauth -srcarg1 user:username -srcarg2 identity_file:ssh_key_path
-srcarg3 sudo_location:sudo_path -eval [-advisor [-ignoreadvisor] | -skipadvisor]]
```


ZDMCLI `migrate database` options for a co-managed database migration: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -rsp file_path -sourcenode host
-srcauth zdmauth -srcarg1 user:username -srcarg2 identity_file:ssh_key_path
-srcarg3 sudo_location:sudo_path -targetnode host -tgtauth zdmauth
-tgtarg1 user:username -tgtarg2 identity_file:ssh_key_path
-tgtarg3 sudo_location:sudo_path -eval [-advisor [-ignoreadvisor] | -skipadvisor]]
```


### modify imagetype {#GUID-4470F308-B1D0-49BA-8F5E-8641CF7C67EC}

Modifies the list of user actions associated with the specified image type.

Syntax
```
$ZDM_HOME/bin/zdmcli modify imagetype
-imagetype image_type_name
-useractions user_action_list
```


Options

**Table H-6 ZDMCLI modify imagetype Options**

Option | Description  
---|---  
`-imagetype *`image_type`*` |  Name of the image type to be modified  
`-useractions *`user_action_list`*` |  Comma-separated list of user actions that you want to associate with the image type, for example:<br>`-useractions userAction1, userAction2`  

### modify job {#GUID-DA91DA0D-3A68-4283-9158-602E060C2457}

Allows you to modify the Oracle GoldenGate Extract and Replicat parameters of a running migration job.

Syntax
```
zdmuser> $ZDM_HOME/bin/zdmcli modify job
-jobid job_id
-rsp response_file_path
```


Options

**Table H-7 ZDMCLI modify job Options**

Option | Description  
---|---  
`-jobid *`job_id`*` |  Unique job ID value (integer) for the scheduled migration job<br>The job ID is assigned when the migration job is scheduled.  
`-rsp *`response_file_path`*` |  Location of the Zero Downtime Migration response file  

### modify useraction {#GUID-B23DE967-6622-464F-ABCA-FFE605C9BB52}

Modifies the configuration of the user action with the specified name.

Syntax
```
$ZDM_HOME/bin/zdmcli modify useraction
-useraction user_action_name
[-actionscript script_name]
[-actionfile file_name]
[-pre | -post]
[-optype MIGRATE_DATABASE]
[-phase phase]
[-onerror {ABORT \| CONTINUE}]
[-runscope
{ONENODE \|
ALLNODES \|
AUTO}]
[-outputfrom useraction_names]
```


Options

**Table H-8 ZDMCLI modify useraction Options**

Option | Description  
---|---  
`-useraction *`user_action_name`*` |  Name of the user action  
`-actionscript *`script_name`*` |  Script file to be run  
`-actionfile *`file_name`*` |  Accompanying file needed by the user action  
`-pre` |  Runs the user action before the operation  
`-post` |  Runs the user action after the operation  
`-optype MIGRATE_DATABASE` |  Defines the operation for which the user action is configured as `MIGRATE_DATABASE`  
`-onerror {ABORT \| CONTINUE}` |  Defines whether to stop or continue running if an error occurs while the user action is running  
`-runscope {ONENODE \| ALLNODES \| AUTO}` |  The servers where the user action will be run. <br>Specify *`AUTO`* to choose the run scope based on the other command options.   
`-outputfrom *`useraction_names`*` |  Comma-separated list of user action names whose output is to be supplied to the current user action.  

### query audit {#GUID-3C8C2CAF-A466-44EF-8AC6-6A2EF6BAC28B}

Displays the migration job audit records.

Syntax
```
$ZDM_HOME/bin/zdmcli query audit
[[  [-operation
{ add \|
abort \|
modify \|
migrate \|
grant \|
revoke \|
query \|
resume \|
suspend }]
[ -entity
{ client \|
role \|
database \|
user \|
audit \|
imagetype \|
useraction}] |
[-user user_name]
[-client client_name] |
[-from timestamp -to timestamp] |
-before timestamp |
-since timestamp |
-first number |
-last number] |
-record record_id |
-config]
```


Options

**Table H-9 ZDMCLI query audit Options**

Option | Description  
---|---  
`-operation { add \| abort \| modify \| migrate \| grant \| revoke \| query \| resume \| suspend }` |  The operation type for which to display audit records  
`-entity { client \| role \| database \| user \| audit \| imagetype \| useraction }` |  The entity for which to display audit records  
`-user *`user_name`*` |  Name of the user who ran the migration operations  
`-client *`client_name`*` |  Client cluster name where migration operations were executed  
`-from *`timestamp`*` |  Date for getting a range of audit records, in the format YYYY-MM-DD.  
`-to *`timestamp`*` |  Date for getting a range of audit records, in the format YYYY-MM-DD  
`-since *`timestamp`*` |  Date to get audit records since the date provided, in the format YYYY-MM-DD  
`-before *`timestamp`*` |  Date to get audit records before the date provided, in the format YYYY-MM-DD  
`-first *`number`*` |  First number of audit records to get from the query  
`-last *`number`*` |  Last number of audit records to get from the query  
`-record *`record_ID`*` |  Audit record ID  
`-config` |  Show maximum record configuration  

### query job {#GUID-B540D801-7B39-40A3-BDD8-12891710ABA2}

Gets the current status of scheduled migration jobs.

Syntax
```
$ZDM_HOME/bin/zdmcli query job
[-jobid job_id
[-jobtype]]
[-sourcenode source_host_name
[-sourcedb db_name |
-sourcesid sid]]
[-targetnode target_host_name]
[-latest]
[-eval |
-migrate]
[-status
{SCHEDULED \|
EXECUTING \|
UNKNOWN \|
TERMINATED \|
FAILED \|
SUCCEEDED \|
PAUSED \|
ABORTED}]
[-phase]
[-dbname database_name]
[-since timer_value]
[-upto timer_value]
[-brief]
[-statusonly]
```


Options

**Table H-10 ZDMCLI query job Options**

Option | Description  
---|---  
`-jobid *`job_id`*` |  Unique job ID value (integer) for the scheduled migration job<br>The job ID is assigned when the migration job is scheduled.  
`-job_type` |  Returns the type of the scheduled job  
`-sourcenode *`source_host_name`*` |  Server on which the source database is running  
`-sourcedb *`db_name`*` |  Name of the source database to be migrated  
`-sourcesid *`sid`*` |  The `ORACLE_SID` of the source single instance database without Grid Infrastructure   
`-targetnode *`target_host_name`*` |  Target server to which the database is migrated  
`-latest` |  Returns the most recent job that matches the given criteria  
`-eval` |  Returns evaluation jobs only  
`-migrate` |  Returns migration jobs only  
`-status {SCHEDULED \| EXECUTING \| UNKNOWN \| TERMINATED \| FAILED \| SUCCEEDED \| PAUSED \| ABORTED}` |  Returns jobs that match the specified job status  
`-phase` |  Returns the status of the given phase. If the phase supplied by the user is invalid, the query returns an error.```./zdmcli query job -jobid 33 -phase ZDM_PREUSERACTIONS_TGT -statusonly # exmaple.com: Audit ID: 153 # Job ID: 33 # ZDM_PREUSERACTIONS_TGT:PENDING ```
`-dbname *`unique_db_name`*` |  Specifies the database `DB_UNIQUE_NAME` value   
`-since *`timer_value`*` |  Date from which to get the jobs, in ISO-8601 format. For example: 2016-12-21T19:13:17+05  
`-upto *`timer_value`*` |  Upper limit time to which to get the jobs, in ISO-8601 format. For example: 2016-12-21T19:13:17+05  
`-brief` |  Returns job details summary only  
`-statusonly` |  Returns only the job status and current phase name  

Usage Notes

To identify if the current run is a resumption of a PAUSED migration job, a restart of a FAILED migration job, or a fresh start for a migration job, use the following options:

* `zdmcli query job -latest` gets the latest job without considering the `job_type`
* `zdmcli query job -latest -migrate` gets the latest non-evaluation migration job 
* `zdmcli query job -latest -eval` get the latest evaluation migration job 



The `zdmcli query` job reports Oracle GoldenGate replication metrics for online logical migration. If a job has completed the phase `ZDM_CREATE_GG_EXTRACT_SRC` but not the phase `ZDM_START_GG_REPLICAT_TGT`, then `zdmcli query` job reports only the following metrics: - 

* Extract status (Running, Abended, and so on.)
* Extract write checkpoint (number of trail files generated)

If a job has completed both ZDM_CREATE_GG_EXTRACT_SRC and ZDM_START_GG_REPLICAT_TGT phases, then `zdmcli query` job reports the following metrics: - 

* Extract status (Running, Abended, and so on).
* Extract write checkpoint (number of trail files generated)
* Replicat status (Running, Abended, and so on).
* Replicat read checkpoint (number of trail files applied)
* End-to-end heartbeat lag (in seconds)
* Replication throughput (in GBph)



Example:
```
Oracle GoldenGate replication metrics:
Extract "EXTABC" status: Running
Extract "EXTABC" trail files generated: 2
Replicat "REPDE" status: Running
Replicat "REPDE" trail files applied: 1
End-to-end heartbeat lag: 600 seconds
Replication throughput: 1.0 GBph
```


### query useraction {#GUID-91A2DEE3-4448-4F29-8509-7678A5AEEF79}

Displays the configuration of a user action.

Syntax
```
$ZDM_HOME/bin/zdmcli query useraction
[-useraction user_action_name | -imagetype image_type
[-optype MIGRATE_DATABASE]]
```


Options

**Table H-11 ZDMCLI query useraction Options**

Option | Description  
---|---  
`-useraction *`user_action_name`*` |  Name of the user action  
`-imagetype *`image_type`*` |  Specify the image type name  
`-optype MIGRATE_DATABASE` |  Operation for which the user action is configured  

### resume job {#GUID-FC85A7E2-E1A9-4845-A282-469674B076EA}

Resumes a specified job that was paused.

Syntax
```
$ZDM_HOME/bin/zdmcli resume job
-jobid job_id
[-pauseafter pause_phase |
-rsp zdm_logical_template_path]
[-rerun {BACKUP\|RESTORE}]
[ -ignore
{IMPORT_ERRORS \| EXPORT_ERRORS}]
[ -skip
{SWITCHOVER \|
UPGRADE \|
DG_CONFIG \|
DG_CONFIG_STBY} ]
```


Options

**Table H-12 ZDMCLI resume job Options**

Option | Description  
---|---  
`-jobid *`job_id`*` |  Unique job ID value (integer) for the scheduled job<br>The job ID is assigned when the migration job is scheduled.  
`-pauseafter *`pause_phase`*` |  Pauses the migration job after running the specified phase  
`-rerun {BACKUP\|RESTORE}` |  In a physical migration, you can choose to rerun RMAN backup or restore operations on resume.<br>`BACKUP` causes Zero Downtime Migration to re-run phases starting from the `ZDM_BACKUP_INCREMENTAL_SRC` phase. As part of the re-run of the backup phase, an RMAN crosscheck is first run to ensure that the source controlfile is consistent with any backups present on the backup media. <br>`RESTORE` triggers rerun of the restore-related phases which includes dropping the existing target database, restoring spfile, restoring controlfile and datafiles. <br>See the Usage Notes below for more information.  
`-rsp *`zdm_logical_template_path`*` |  Specify the migration response file if you modified any parameters to be picked up by Zero Downtime Migration on resume. <br>This option can only be used with logical migration jobs.  
`-ignore {IMPORT_ERRORS \| EXPORT_ERRORS}` |  Ignores the Data Pump errors in the latest phase executed. <br>Specifying this on resume of `FAILED` job lets the job to proceed to next action in the respective phase and further proceed to follow-on phases in the job without re-executing Data Pump action. <br>The phase status that `FAILED` is marked with status `IGNORED_ON_FAILURE`. <br>`-ignore EXPORT_ERRORS` ignores all error seen in Data Pump export and ZDM job proceeds to the next workflow actions. <br>`-ignore IMPORT_ERRORS` ignores all error seen in Data Pump import and ZDM job proceeds to the next workflow actions.   
`-skip {SWITCHOVER \| UPGRADE \| DG_CONFIG \| DG_CONFIG_STBY}` |  Resumes the job after the switchover has been performed from outside of ZDM. <br>When ZDM resume job is started with this option, ZDM does not throw an error when the role of the source database is not PRIMARY and/or the role of the target database is not STANDBY.  > **note:** If the role switchover of the source and target database is successful, the job continues.  Resumes the job after the UPGRADE has been performed from outside of ZDM or when you want to skip the UPGRADE operation. When the ZDM resumes, job is started with this option. ZDM does not perform UPGRADE operation on resume.Following values are available: <br>* -skip DG_CONFIG: Skips Data Guard configuration between the source and target databases.<br>-skip DG_CONFIG_STBY: Skips Data Guard configuration for target standby database (Disaster Recovery case).



Usage Notes

See [Resume a Migration Job](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-B911632D-4DEB-484D-84C0-3D7D4C23620A)

The `-rerun {BACKUP\|RESTORE}` option which enables the phase rerun provides you an option to retry backup or restore procedures on failure. You might use this option if 

* Backups are accidentally deleted before the restore is attempted/completed. 

* The TDE wallet is changed after Zero Downtime Migration has already copied the wallet to the target, so the differential backup source would encrypt the backup with the new wallet but the incremental restore will fail because the target will not have the latest wallet. In this case using `-rerun` will trigger a re-copy of the wallet and subsequently rerun of restore including dropping and recreating the target. 




### suspend job {#GUID-60EE723B-BC44-4583-9989-14C5CEC0FC3E}

Suspends the specified job if running. Executing `suspend job` stops the ongoing job at the current work flow phase and allows jobs to be resumed later. 

Syntax
```
$ZDM_HOME/bin/zdmcli suspend job
-jobid job_id
```


Options

**Table H-13 ZDMCLI suspend job Options**

Option | Description  
---|---  
`-jobid *`job_id`*` |  Unique job ID value (integer)<br>The job ID number is assigned when the migration job is scheduled.  

### delete job {#GUID-0E20C8BD-F4A7-473F-8902-A5765EA5E102}

Deletes the specified job or jobs from the repository.

Syntax
```
$ZDM_HOME/bin/zdmcli delete job -help
{ -jobids
-status
{SCHEDULED \|
EXECUTING \|
UNKNOWN \|
TERMINATED \|
FAILED \|
SUCCEEDED \|
PAUSED \|
ABORTED} }
[-force]
[-verbose]

-jobids  Comma separated list of one or more jobs to be deleted
-status {SCHEDULED \| EXECUTING \| UNKNOWN \| TERMINATED \| FAILED \| SUCCEEDED \| PAUSED \| ABORTED}
Job status for which to get the jobs.
-force Forcibly delete the parent job and make all its child jobs independent
-verbose Include additional information
```


Options

**Table H-14 ZDMCLI delete job Options**

Option | Description  
---|---  
`-jobids job_ids` | Comma separated list of one or more jobs to be deleted.  

### delete useraction {#GUID-20547352-7F5D-4B09-B1E7-9EC9489F5C9F}

Deletes the specified user action.

Syntax
```
$ZDM_HOME/bin/zdmcli delete useraction
-useraction  user_action_name
```


Options

**Table H-15 ZDMCLI delete useraction Options**

Option | Description  
---|---  
-useraction *`user_action_name`* | Name of the user action.  

### delete imagetype {#GUID-AF17679F-D322-4A20-A5BF-3178237B968C}

Deletes an existing image type.

Syntax
```
$ZDM_HOME/bin/zdmcli delete imagetype
-imagetype image_type
```


Options

**Table H-16 ZDMCLI delete useraction Options**

Option | Description  
---|---  
-imagetype *`image_type`* |  Name of the image type to be deleted. For a custom image type, use the image type name.<br>The following options are available:<br> * `ORACLEDBSOFTWARE` (default) for Oracle Database software <br> *`ORACLEGISOFTWARE` for Oracle Grid Infrastructure software <br> *`ORACLEGGSOFTWARE` for Oracle GoldenGate software <br>*`LINUXOS` for Oracle Linux Operating System <br> * `SOFTWARE` for all other software 
## 8Migrating Your Database with Zero Downtime Migration {#GUID-C20DB7D4-E0CE-4B50-99D0-B16C18DDD34B}

Evaluate the database migration job, run the job, and perform other operations during and after a database migration.

See the [Zero Downtime Migration Release Notes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMRN-GUID-A1A467DC-FC06-4409-AF7F-BF0186CD8C54) for the latest information about known issues, My Oracle Support notes, and runbooks. 

### Evaluate the Migration Job {#GUID-7A058A40-2372-4628-9E6E-7E1DA18A38F2}

Zero Downtime Migration provides options and tools for evaluating the migration job before you run it against the production database.

Ensure that you have met all of the prerequisites and completed the required preparations described in [Preparing for a Physical Database Migration](preparing-for-database-migration.md#GUID-46459904-7B68-4B7B-8EC6-C66423CEB6B9) before you begin the migration procedures in this topic. 

In addition be sure the following tasks are done:

* Obtain the necessary access credentials required.

If Oracle Cloud Infrastructure Object Storage is used as the backup medium, obtain the Object Storage access credential. The user ID for the Oracle Cloud Infrastructure Console user and an auth token for Object Storage is required. If you are not using an existing auth token, a new auth token can be generated using the Oracle Cloud Infrastructure Console.

If the source database server is accessed with the root user, then you need the root user password. If the source and target database serves are accessed with a private key file, then you need the private key file. The SYS password for the source database environment is also required. 

If Zero Data Loss Recovery Appliance is used as the backup medium, get the Zero Data Loss Recovery Appliance virtual private catalog (VPC) user credentials.

* Prepare the Zero Downtime Migration response file.

The database migration is driven by a response file that captures the essential parameters for accomplishing the task.

Use the sample RSP templates in `$ZDM_HOME/rhp/zdm/template/` file for example entries needed to set up the response file for your particular source, target, and backup environments. 




The ZDMCLI `migrate database` command has options to let you test the migration before you run it in production. The options are highlighted in the following syntax examples. 

ZDMCLI `migrate database` syntax for an Autonomous Database migration: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-rsp file_path
-sourcedb source_db_unique_name_value
-sourcenode host
-srcauth zdmauth
-srcarg1 user:username
-srcarg2 identity_file:ssh_key_path
-srcarg3 sudo_location:sudo_path**
-eval [-advisor [-ignoreadvisor] | -skipadvisor]]**
```


ZDMCLI `migrate database` syntax for a co-managed database migration: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-rsp file_path
-sourcedb source_db_unique_name_value
-sourcenode host
-srcauth zdmauth
-srcarg1 user:username
-srcarg2 identity_file:ssh_key_path
-srcarg3 sudo_location:sudo_path
-targetnode host
-tgtauth zdmauth
-tgtarg1 user:username
-tgtarg2 identity_file:ssh_key_path
-tgtarg3 sudo_location:sudo_path**
-eval [-advisor [-ignoreadvisor] | -skipadvisor]]**
```


See the following topics for information about using the ZDMCLI `migrate database` options to evaluate your migration. 

#### Using the ZDMCLI MIGRATE DATABASE -eval Option {#GUID-CCDA358F-3EB4-48D6-856B-5A6BEC3C94CD}

Before submitting the database migration job for the production database, perform a test migration to determine how the process might fare with your configuration and settings.

It is highly recommended that for each migration job, you first run `migrate database` in evaluation mode. Evaluation mode allows you to correct any potential problems in the setup and configuration before performing the actual migration on a production database. 

In evaluation mode, the migration process runs without effecting the changes. It is safe to run a migration job in evaluation mode as many times as needed before running the actual migration job.

The `migrate database` output indicates the migration job ID, which you can use to query the status of the job. 

To run an evaluation of the migration job, run the ZDMCLI command `migrate database` with the `-eval` option, as shown in the following example. 

Log in to the Zero Downtime Migration service host and switch to the `zdmuser` installed user. 
```
su - zdmuser
```


If connectivity to the source database server is done through root credentials then the command would be the following:
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -sourcedb source_db_unique_name_value
-sourcenode source_database_server_name -srcroot
-targetnode target_database_server_name
-backupuser Object_store_login_user_name
-rsp response_file_location
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/usr/bin/sudo **-eval**
```


For the prompts, specify the source database SYS password and the source database server root user password. If the backup destination is Object Store (Bucket), then specify user swift authentication token. If the backup destination is Storage Classic (Container) then specify your tenancy login password.

For example,
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -sourcedb zdmsdb -sourcenode ocicdb1
-srcroot -targetnode ocidb1 -backupuser backup_user@example.com
-rsp /u01/app/zdmhome/rhp/zdm/template/zdm_template_zdmsdb.rsp -tgtauth zdmauth
-tgtarg1 user:opc -tgtarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk -tgtarg3
sudo_location:/usr/bin/sudo -eval

Enter source database zdmsdb SYS password:
Enter source user "root" password:
Enter user "backup_user@example.com" password:
```


If connectivity to the source database server is through SSH key, then the command would be:
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -sourcedb source_db_unique_name_value
-sourcenode source_database_server_name -srcauth zdmauth
-srcarg1 user:source_database_server_login_user_name
-srcarg2 identity_file:ZDM_installed_user_private_key_file_location
-srcarg3 sudo_location:/usr/bin/sudo -targetnode target_database_server_name
-backupuser Object_store_login_user_name -rsp response_file_location
-tgtauth zdmauth -tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/usr/bin/sudo **-eval**
```


For the prompts, specify the source database SYS password. If the backup destination is Object Store (Bucket), then specify user swift authentication token. If the backup destination is Storage Classic (Container), then specify your tenancy login password.
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -sourcedb zdmsdb -sourcenode ocicdb1 -srcauth zdmauth
-srcarg1 user:opc -srcarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-srcarg3 sudo_location:/usr/bin/sudo -targetnode ocidb1 -backupuser backup_user@example.com
-rsp /u01/app/zdmhome/rhp/zdm/template/zdm_template_zdmsdb.rsp -tgtauth zdmauth -tgtarg1 user:opc
-tgtarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk -tgtarg3 sudo_location:/usr/bin/sudo -eval

Enter source database zdmsdb SYS password:
Enter user "backup_user@example.com" password:
```


Note that if a source single instance database is deployed without a Grid Infrastructure home, then in the above command use `-sourcesid` in place of `-sourcedb`. 

Also, if a source database is configured for a `PASSWORD` based wallet, then add the `-tdekeystorepasswd` option to the command above, and for the prompt, specify the source database TDE keystore password value. 

Note that the `–backupuser` argument takes the Object Storage access user or Zero Data Loss Recovery Appliance VPC user, and is skipped if NFS is the backup medium. For NFS, the source database user should have ‘rwx’ access to the NFS path provided. 

The migration command checks for patch compatibility between the source and target home patch level, and expects the target home patch level to be equal to or higher than the source. If the target home patch level is not as expected, then the migration job is stopped and missing patches are reported. You can either patch the target home with the necessary patches or you can force continue the migration by appending the `–ignore PATCH_CHECK` or `-ignore ALL` option to the migration command. 

If there is no SSH connectivity to the source database server and the target is Oracle Cloud Infrastructure co-managed database then, run the following command:
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-rsp response_file_location
-targetnode target_database_server_name
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/usr/bin/sudo
-eval
```


When you run `migrate database` with `-eval`, Zero Downtime Migration only runs a subset of the migration job phases. For example, a logical migration job run with `-eval` would only run the following phases: 

`ZDM_VALIDATE_SRC`

`ZDM_VALIDATE_TGT`

`ZDM_SETUP_SRC`

`ZDM_PRE_MIGRATION_ADVISOR`

`ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC`

`ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT`

`ZDM_PREPARE_DATAPUMP_SRC`

`ZDM_DATAPUMP_ESTIMATE_SRC`

`ZDM_CLEANUP_SRC`

The `migrate database` output indicates the job ID for the migration job, which you can use to query the status of the job. 

If you want to run the command without providing passwords at the command line, see [Providing Passwords Non-Interactively Using a Wallet](provide-passwords-non-interactively-using-wallet.md#GUID-CED7A2D9-6AD9-4527-BE29-2F036B6CEDCC). 

#### Zero Downtime Migration Schema Analysis with Cloud Premigration Advisor Tool {#GUID-26CF2B21-EF8A-458A-931D-1873FEF8AA5A}

For logical migration jobs, Zero Downtime Migration is integrated with the Cloud Premigration Advisor Tool (CPAT) to analyze the source database during a migration job, and advise you about database features and constructs that are problematic.

CPAT provides the following benefits to your migration evaluation:

* Warns you about any features used by your database that aren't supported in the target environment

* Makes suggestions for remedial changes and/or parameters to use for the Data Pump export and import operations

* Optionally generates remedial scripts for failing checks that you can run against the source database




> **note:** For Autonomous databases, ZDM does not have the traditional DBA role, hence, CPAT might report that the admin role is not present for the specified user. In such case, grant the required admin roles to the user as follows:
    ```
    connect ADMIN;
    GRANT OCPROLE TO ;
    GRANT DWROLE TO ;
    ```


When you run a logical migration using `ZDMCLI migrate database`, CPAT is run by default as phase `ZDM_PRE_MIGRATION_ADVISOR`. Checks are customized based on response file input parameter settings. 

See [Cloud Premigration Advisor Tool Support](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMRN-GUID-893235FB-5035-43BB-8E5B-0C3787ACE348) for CPAT use cases supported by Zero Downtime Migration. 

The following are options you can use with `ZDMCLI migrate database` to customize how CPAT runs, or to skip the CPAT phase. 

* **-advisor** only runs the following minimum migration job phases required for exclusively running Cloud Premigration Advisor Tool (CPAT) on the migration. 

`ZDM_VALIDATE_SRC`

`ZDM_VALIDATE_TGT`

`ZDM_SETUP_SRC`

`ZDM_PRE_MIGRATION_ADVISOR`

`ZDM_CLEANUP_SRC`

* **-ignoreadvisor** ignores any problems or errors reported by CPAT. 

* **-skipadvisor** skips the CPAT phase in a migration job. 

* **-genfixup {YES \| NO}** generates remedial scripts for failing checks that you can run if you choose. 




The migration job output displays the checks performed, descriptions of any problems, and actions you can take to resolve the issues, as shown in this example.
```
Cloud Premigration Advisor Tool Version 21.1.0-10
Cloud Premigration Advisor Tool completed with overall result: Action Required
Cloud Premigration Advisor Tool generated report location:
/scratch/app/u02/base/zdm/zdm_db12151_1/out/premigration_advisor_report.json
RESULT: Action Required

Schemas Analyzed (2): HR01,HR02
A total of 29 checks were performed
There were 0 checks with Failed results
There were 1 checks with Action Required results: nls_character_set_conversion
(0 relevant objects)
There were 3 checks with Review Required results: timezone_table_compatibility_higher
(0 relevant objects), has_role_privileges (0 relevant objects),
has_sys_privileges (0 relevant objects)
There were 1 checks with Review Suggested results: has_default_tablespace_not_data
(2 relevant objects) nls_character_set_conversion
RESULT: Action Required
DESCRIPTION: Check for issues caused by conversion of character data
from the source to the target database character set, such as expansion of
character values beyond column length or loss of invalid character codes.
ACTION: Scan the schemas to be migrated using Database Migration
Assistant for Unicode (DMU) and analyze all possible convertibility issues.

FIXUP SCRIPT:
/scratch/app/u02/base/zdm/zdm_db1215_1/zdm/lib/cpatfixups/gg_force_logging.sql
```


See [migrate database](zero-downtime-migration-zdmcli-command-reference.md#GUID-37ABF830-2FC3-4F71-9132-DF05DCFABBB9) for more information about the command options, and see [Cloud Premigration Advisor Tool (CPAT) Analyzes Databases for Suitability of Cloud Migration (Doc ID 2758371.1)](https://support.oracle.com/rs?type=doc&id=2758371.1) for more information about Cloud Premigration Advisor Tool. 

> **note:** Excluding TABLES in the migration response file does not exclude it from CPAT analysis. SCHEMAS can be excluded from CPAT if the entire schema was excluded. 

The presence of an Oracle Cloud unsupported table can lead to Action Required status in the CPAT report. You can submit a new migration job with `-ignoreadvisor` to ignore the Action Required error and proceed to migration, provided that all other CPAT Action Required errors are addressed. 

#### Configuring Migrations for Remote CPAT Invocation {#GUID-7973872B-ED7C-433C-BCB6-DDEAF4EC55BB}

Zero Downtime Migration handles running Cloud Premigration Advisor Tool (CPAT) based on the specified source database access. In some cases, such as Amazon RDS Oracle as a source, Zero Downtime Migration ensures that CPAT is run remotely in a secure manner.

To have Zero Downtime Migration run CPAT validation remotely, set the response file parameter `RUNCPATREMOTELY=true`. 

A connect string is constructed with your settings from the following parameters:

* `SOURCEDATABASE_ADMINUSERNAME`

* `SOURCEDATABASE_CONNECTIONDETAILS_HOST`

* `SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME`

* `SOURCEDATABASE_CONNECTIONDETAILS_PORT`

* `SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME`




For more information about CPAT, see [Cloud Premigration Advisor Tool (CPAT) Analyzes Databases for Suitability of Cloud Migration (Doc ID 2758371.1)](https://support.oracle.com/rs?type=doc&id=2758371.1)

### Migrate the Database {#GUID-C91B8800-6CE3-44FD-80E4-699569140CEB}

Perform the database migration with Zero Downtime Migration using the `ZDMCLI migrate database` command. 

Ensure that you have met all of the prerequisites and completed the required preparations described in [Preparing for a Physical Database Migration](preparing-for-database-migration.md#GUID-46459904-7B68-4B7B-8EC6-C66423CEB6B9) before you begin the migration procedures in this topic. 

In particular be sure the following tasks are done:

* Obtain the necessary access credentials required.

    If Oracle Cloud Infrastructure Object Storage is used as the backup medium, obtain the Object Storage access credential. The user ID for the Oracle Cloud Infrastructure Console user and an auth token for Object Storage is required. If you are not using an existing auth token, a new auth token can be generated using the Oracle Cloud Infrastructure Console.
    
    If the source database server is accessed with the root user, then you need the root user password. If the source and target database serves are accessed with a private key file, then you need the private key file. The SYS password for the source database environment is also required. 
    
    If Zero Data Loss Recovery Appliance is used as the backup medium, get the Zero Data Loss Recovery Appliance virtual private catalog (VPC) user credentials.

* Prepare the Zero Downtime Migration response file.

    The database migration is driven by a response file that captures the essential parameters for accomplishing the task.
    
    Use the sample $ZDM_HOME/rhp/zdm/template/zdm_template.rsp file for example entries needed to set up the response file for your particular source, target, and backup environments.

* Determine if the migration process needs to be paused and resumed before you start the database migration. Once the migration job is initiated the job system runs the job as configured.

    If the migration job needs to pause and resume at a particular point, then see the topics List Migration Job Phases and Pause and Resume Migration Job (cross references below) for more details.




**Physical Migration with Root Credentials**

    The database migration job is submitted from the Zero Downtime Migration service host by the `zdmuser` user using the ZDMCLI command `migrate database`. 
    
    For a physical migration, if connectivity to the source database server is through root credentials, then the command would be:
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
    -tgtarg3 sudo_location:/usr/bin/sudo
    ```


For the prompts, specify the source database SYS password and source database server root user password. If the backup destination is Object Store (Bucket), then specify user swift authentication token. If the backup destination is Storage Classic (Container), then specify your tenancy login password.

For example:
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-sourcedb zdmsdb
-sourcenode ocicdb1
-srcroot
-targetnode ocidb1
-backupuser backup_user@example.com
-rsp /u01/app/zdmhome/rhp/zdm/template/zdm_template_zdmsdb.rsp
-tgtauth zdmauth
-tgtarg1 user:opc
-tgtarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-tgtarg3 sudo_location:/usr/bin/sudo

Enter source database zdmsdb SYS password:
Enter source user "root" password:
Enter user "backup_user@example.com" password:
```


**Physical Migration with SSH Key**

For a **physical migration**, if connectivity to the source database server is through **SSH key**, then the command would be: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-sourcedb source_db_unique_name_value
-sourcenode source_database_server_name
-srcauth zdmauth
-srcarg1 user:source_database_server_login_user_name
-srcarg2 identity_file:ZDM_installed_user_private_key_file_location
-srcarg3 sudo_location:/usr/bin/sudo
-targetnode target_database_server_name
-backupuser Object_store_login_user_name
-rsp response_file_location
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/usr/bin/sudo
```


For the prompts, specify the source database SYS password. If the backup destination is Object Store (Bucket), then specify user swift authentication token. If the backup destination is Storage Classic (Container), then specify your tenancy login password.

For example,
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-sourcedb zdmsdb
-sourcenode ocicdb1
-srcauth zdmauth
-srcarg1 user:opc
-srcarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-srcarg3 sudo_location:/usr/bin/sudo
-targetnode ocidb1
-backupuser backup_user@example.com
-rsp /u01/app/zdmhome/rhp/zdm/template/zdm_template_zdmsdb.rsp
-tgtauth zdmauth
-tgtarg1 user:opc
-tgtarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-tgtarg3 sudo_location:/usr/bin/sudo

Enter source database zdmsdb SYS password:
Enter user "backup_user@example.com" password:
```


If a source single instance is deployed without a Grid Infrastructure home, then in the command above use `-sourcesid` in place of `-sourcedb`. 

If the source database is configured for a `PASSWORD` based wallet, then add the `-tdekeystorepasswd` option to the command above, and for the prompt, specify the source database TDE keystore password value. 

Note that the `–backupuser` argument takes the Object Storage access user or Zero Data Loss Recovery Appliance VPC user and is skipped if NFS is the backup medium. For NFS, the source database user should have ‘rwx’ access to the NFS path provided. 

**Logical Migration to Autonomous Database**

For a **logical migration to Autonomous Database**, the command would be: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-rsp file_path
-sourcedb source_db_unique_name_value
-sourcenode host
-srcauth zdmauth
-srcarg1 user:username
-srcarg2 identity_file:ssh_key_path
-srcarg3 sudo_location:sudo_path
-eval [-advisor [-ignoreadvisor] | -skipadvisor]]
```


**Logical Migration to a Co-Managed Database**

For a logical migration to a co-managed system, the command would be: 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-rsp file_path
-sourcedb source_db_unique_name_value
-sourcenode host
-srcauth zdmauth
-srcarg1 user:username
-srcarg2 identity_file:ssh_key_path
-srcarg3 sudo_location:sudo_path
-targetnode host
-tgtauth zdmauth
-tgtarg1 user:username
-tgtarg2 identity_file:ssh_key_path
-tgtarg3 sudo_location:sudo_path
[-ignoreadvisor | -skipadvisor]
```


**Logical Migration to a Co-Managed Database without SSH access**

If there is no SSH connectivity to the source database server and the target is Oracle Cloud Infrastructure co-managed database then, run the following command:
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-rsp response_file_location
-targetnode target_database_server_name
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/usr/bin/sudo
```


**Patch Compatibility**

The migration command checks for patch compatibility between the source and target home patch level, and expects the target home patch level to be equal to or higher than the source. If the target home patch level is not as expected, then the migration job is stopped and missing patches are reported. You can either patch the target home with the necessary patches or you can force continue the migration by appending the `–ignore PATCH_CHECK` or `-ignore ALL` option to the migration command. 

**Job ID Value**

The command result output indicates the job ID for the migration job, which you can use to query the status of the job.

**Running Migrations Non-Interactively**

If you want to run the command without providing passwords at the command line, see [Providing Passwords Non-Interactively Using a Wallet](provide-passwords-non-interactively-using-wallet.md#GUID-CED7A2D9-6AD9-4527-BE29-2F036B6CEDCC). 

### Post-Migration Tasks {#GUID-50A5B7BE-D92E-43AF-A46D-8E08DF9C7E64}

When the migration job completes, make sure to complete any relevant post-migration tasks.

**Update the global_name**

The global_name refers to the full name of a database (including its domain) which uniquely identifies it from any other database. During migration the global_name of the source system is retained. This is fine as long as it does not contain the domain_name.

If the domain_name is part of the global_name, update the global_name manually on the target system to include the new domain name.

**Configure TDE wallet after migration from Oracle Database 11.2 and 12.1 sources**

For Oracle Database 11.2 and 12.1 sources, TDE wallet configuration is not required. If the source does not have a TDE wallet configured, any TDE configuration on the target is removed. After the migration, you must configure the TDE wallet at the target before creating any new tablespaces.

### Query Migration Job Status {#GUID-BDA0B981-2552-4EB7-B0AE-D1930DD92B25}

You can query the migration job status while the job is running.

Query the status of a database migration job using the ZDMCLI `query job` command, specifying the job ID. The job ID is shown in the command output when the database migration job is submitted. 
```
zdmuser> $ZDM_HOME/bin/zdmcli query job -jobid job-id
```


You can find the console output of the migration job in the file indicated (Result file path:) in the `query job` command output. You can see migration progress messages in the specified file 

### List Migration Job Phases {#GUID-1796BE5B-4E18-4792-B088-2B8135EEDF54}

You can list the operation phases involved in the migration job.

To list the operation phases involved in the migration job, add the `-listphases` option in the ZDMCLI `migrate` command. This option will list the phases involved in the operation. 

For example,
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -sourcedb zdmsdb -sourcenode ocicdb1 -srcauth zdmauth
-srcarg1 user:opc -srcarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk -srcarg3 sudo_location:/usr/bin/sudo
-targetnode ocidb1 -backupuser backup_user@example.com -rsp /u01/app/zdmhome/rhp/zdm/template/zdm_template_zdmsdb.rsp
-tgtauth zdmauth -tgtarg1 user:opc -tgtarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-tgtarg3 sudo_location:/usr/bin/sudo **-listphases**
```


### Pause a Migration Job {#GUID-02812170-21B6-4C09-8BC4-5588576EDD41}

You can pause a migration job at any point after the `ZDM_SETUP_TGT` phase, and resume the job at any time. 

To pause a migration job, specify the `–pauseafter` option in the `ZDMCLI` `migrate` command with a valid phase to be paused after. 

In the following example, if you specify `-pauseafter ZDM_SETUP_TGT`, the migration job will pause after completing the `ZDM_SETUP_TGT` phase. 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database -sourcedb zdmsdb -sourcenode ocicdb1
-srcauth zdmauth -srcarg1 user:opc
-srcarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-srcarg3 sudo_location:/usr/bin/sudo -targetnode ocidb1
-backupuser backup_user@example.com -rsp /u01/app/zdmhome/rhp/zdm/template/zdm_template_zdmsdb.rsp -tgtauth zdmauth
-tgtarg1 user:opc -tgtarg2 identity_file:/home/zdmuser/.ssh/zdm_service_host.ppk
-tgtarg3 sudo_location:/usr/bin/sudo **-pauseafter ZDM_SETUP_TGT**
```


**Choosing a Migration Job Phase to Pause After**

Choose a valid phase after `ZDM_CONFIGURE_DG_SRC` that is listed in the `migrate database ... -listphases` command output. 

Note that the `-pauseafter` option allows only one phase to be specified. 

The following are some cases where you may need to pause a migration job, and resume after some manual steps:

* Convert the Standby Database to TDE. If the source is not encrypted, of if ZDLRA is used as the data transfer medium, or the Oracle Database version is earlier than 12.2, you need to encrypt the target Cloud database.
* Enable Active Data Guard (optional)
* Monitor Data Guard Configuration Health, before the switchover
* Test the Cloud database (optional). You can convert the standby to the primary without doing the application switchover, which can be used to duplicate the source database for testing in the Cloud. 



**Best Practices for Pausing a Physical Migration Job**

In a physical migration, if you use `-pauseafter` at phase `ZDM_CONFIGURE_DG_SRC`, then at the end of the execution of the phase, a standby is created at the target database and synchronization occurs between source and target databases. 

Pausing the migration job after `ZDM_CONFIGURE_DG_SRC` is recommended so that you can do the following: 

* Perform the application switchover to the cloud
* Manually encrypt the cloud database if ZDLRA is the backup method or your database release is earlier than Oracle Database 12.2 
* Perform a failover to test the cloud database without making changes to the on-premises database



**Preserving Log Files During a Paused Migration Job**

To prevent source and target database log files from getting cleaned up between pausing and resuming a migration job, log files are written to `$ORACLE_BASE/zdm/zdm_*`db_unique_name`*_*`zdm_job_id`*/zdm/log` in their respective source and target database servers. 

Ensure that all of the archive logs generated during and after the `ZDM_BACKUP_INCREMENTAL_SRC` phase are available, preferably until switchover, or at least until the target and source databases are in sync. Older archive logs, the archive logs generated prior to `ZDM_BACKUP_INCREMENTAL_SRC` are not needed. 

### Resume a Migration Job {#GUID-B911632D-4DEB-484D-84C0-3D7D4C23620A}

A paused job can be resumed any time by running the `ZDMCLI` `resume job` command, specifying the respective job ID. 
```
zdmuser> $ZDM_HOME/bin/zdmcli resume job -jobid Job_ID
[-pauseafter valid-phase | -rsp zdm_logical_template]
[-rerun {BACKUP\|RESTORE}]
[-ignore
{IMPORT_ERRORS \| EXPORT_ERRORS}]
-skip SWITCHOVER
```


Scheduling Another Pause on Resume

To schedule another pause, specify the `–pauseafter` option in the `resume job` command with a valid phase to be paused after, as shown in the example below. 

Choose a valid phase later than that at which the phase is currently paused. Phases are listed in the `migrate database ... -listphases` command output. 
```
zdmuser> $ZDM_HOME/bin/zdmcli resume job -jobid Job_ID
-pauseafter valid-phase
```


Rerunning RMAN Backup or Restore Operation on Resume

In a physical migration job, the `-rerun` option allows you to trigger the related migration job phases to rerun when you resume the job. 

The `RESTORE` option value specifies that Zero Downtime Migration should re-run of the restore-related phases, which includes dropping the existing target database, restoring spfile, and restoring controlfile and datafiles. 

The BACKUP option value specifies that Zero Downtime Migration should re-run phases starting from the `ZDM_BACKUP_INCREMENTAL_SRC` phase. As part of the re-run of the backup phase, an RMAN crosscheck is first run to ensure that the source controlfile is consistent with any backups present on the backup media. 

The main reason you might want the phases to re-run is to retry backup or restore procedures on migration job failure. A possible use case for re-running the backup would be if the backups are accidentally deleted before the restore is attempted or completed. Another reason could be that if the TDE wallet is changed after Zero Downtime Migration has already copied the wallet to the target, then the differential backup source would encrypt the backup with the new wallet, but the incremental restore will fail because the target will not have the latest wallet. In this case rerunning triggers a re-copy of the wallet and subsequently rerun of restore including dropping and recreating the target database.

Additional ZDM Job Specific Export or Import Error Ignore Option

To ignore Data Pump errors, specify the `–ignore` option in the `resume job` command.
```
zdmcli resume job -jobid  [-ignore {IMPORT_ERRORS \| EXPORT_ERRORS}]
```


This option lets the job proceed to next action in the respective phase and further proceed to follow-on phases in the job without re-executing Data Pump. You can ignore job specific export or import errors without ignoring all errors using the [IGNOREEXPORTERRORS](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C7998D83-C17B-4055-A826-357264915B26) and the [IGNOREIMPORTERRORS](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-17B3D582-E236-4AD0-95DF-C12CE896204E) parameters. This ignores the specified errors and attempts a re-import or re-export action. 

> **note:** Include the default errors and append additional errors if any. If the IGNOREEXPORTERRORS and IGNOREIMPORTERRORS parameters are updated for resume action, then updated response file is required to be supplied to resume job as follows:` zdmcli resume job -jobid  -rsp `. 

The phase status that `FAILED` is marked with status `IGNORED_ON_FAILURE`. 

`-ignore EXPORT_ERRORS` ignores all error seen in Data Pump export and ZDM job proceeds to the next workflow actions. 

`-ignore IMPORT_ERRORS` ignores all error seen in Data Pump import and ZDM job proceeds to the next workflow actions. 

Skipping switchover

In case of failure during the switchover phase, you can fix any issues and perform a manual switchover. To avoid any issues with the ZDM flow, you can specify that a manual switchover has been performed and that ZDM should skip the switchover phase. Resume the migration job with:

`zdmcli resume job -jobid  -skip SWITCHOVER`

Modifying Migration Parameters on Resume

In a logical migration job, some parameters can be modified upon either pausing the job or a job failure. When you are ready to resume the job, specify the modified response file in the command `resume job`, and the changes are picked up by Zero Downtime Migration when the job is resumed. 

To modify parameters on resume, specify the `–rsp` option in the `resume job` command with the path to the logical migration response file containing the modified parameters, as shown below. 
```
zdmuser> $ZDM_HOME/bin/zdmcli resume job -jobid Job_ID
-rsp modified_zdm_logical_template
```


The following considerations and limitations apply:

* Only certain properties can be modified
* Any property modification is rejected if the phase in which it is used is already completed
* Removing or adding properties to the file is considered a modification, and Zero Downtime Migration performs a validation to determine if those adds/deletes can be included.
* Terminated (aborted) jobs and evaluation jobs cannot be resumed.



The response file paramters references identify which parameters are modifiable and until which phase they can be changed. Note that the phases before which they can be set will differ if `DATA_TRANSFER_MEDIUM` is set to `DBLINK`. 

**Table: Migration Parameters Modifiable on Resume**

Parameter | When can it be modified?  
---|---  
`SOURCEDATABASE_CONNECTIONDETAILS_HOST` |  Modify at any phase  
`SOURCEDATABASE_CONNECTIONDETAILS_PORT` |  Modify at any phase  
`OCIAUTHENTICATIONDETAILS_*` |  Modify at any phase  
`TARGETDATABASE_CONNECTIONDETAILS_HOST` |  Modify at any phase  
`TARGETDATABASE_CONNECTIONDETAILS_PORT` |  Modify at any phase  
`TARGETDATABASE_CONNECTIONDETAILS_TLSDETAILS_*` |  Modify at any phase  
`TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` |  Modify at any phase  
`TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_*` |  Modify at any phase  
`SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_*` |  Modify at any phase  
`SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` |  Modify at any phase  
`SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_*` |  Modify at any phase  
`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_HOST` |  Modify at any phase  
`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PORT` |  Modify at any phase  
`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_TLSDETAILS_*` |  Modify at any phase  
`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` |  Modify at any phase  
`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_*` |  Modify at any phase  
`GOLDENGATEHUB_*` |  Until `ZDM_PREPARE_GG_HUB` phase is `COMPLETED`  
`GOLDENGATESETTINGS_EXTRACT_*` |  Until `ZDM_CREATE_GG_EXTRACT_SRC` phase is `COMPLETED`  
`GOLDENGATESETTINGS_REPLICAT_*` |  Until `ZDM_CREATE_GG_REPLICAT_TGT` phase is `COMPLETED`  
`GOLDENGATESETTINGS_ACCEPTABLELAG` |  Until `ZDM_MONITOR_GG_LAG` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DELETEDUMPSINOSS` |  Until `ZDM_POST_DATAPUMP_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_FIXINVALIDOBJECTS` |  Until `ZDM_POST_DATAPUMP_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_TABLEEXISTSACTION` |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_USERMETADATA` |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`  
`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_IMPORTPARALLELISMDEGREE` |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXPORTPARALLELISMDEGREE` |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_SKIPCURRENT` |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ESTIMATEBYSTATISTICS` |  Until `ZDM_DATAPUMP_ESTIMATE_SRC` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_RETAININDEX` |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`  
`DATAPUMPSETTINGS_METADATAREMAPS-*`LIST_ELEMENT_NUMBER`*` |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`  
`DATAPUMPSETTINGS_DATABASELINKDETAILS_NAME` |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATABASELINKDETAILS_WALLETBUCKET_*` |  Until `ZDM_PREPARE_DATAPUMP_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_DATABUCKET_*` |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC` phase is `COMPLETED`  
`DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_*` |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC` phase is `COMPLETED`  
`DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_*` |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT` phase is `COMPLETED`  
`DATAPUMPSETTINGS_CREATEAUTHTOKEN` |  Until `ZDM_PREPARE_DATAPUMP_TGT` phase is `COMPLETED`  
`INCLUDEOBJECTS-*`LIST_ELEMENT_NUMBER`*` |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`  
`EXCLUDEOBJECTS-*`LIST_ELEMENT_NUMBER`*` |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`  
`OCIPROXY_*` |  Modify at any phase  
`DUMPTRANSFERDETAILS_*` |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  
`TABLESPACEDETAILS_AUTOCREATE` |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  
`TABLESPACEDETAILS_USEBIGFILE` |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  
`TABLESPACEDETAILS_EXTENTSIZEMB` |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  
`TABLESPACEDETAILS_AUTOREMAP` |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  
`TABLESPACEDETAILS_REMAPTARGET` |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  
`TABLESPACEDETAILS_EXCLUDE` |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  
`WALLET_*` |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  
`IGNOREEXPORTERRORS` |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`  
`IGNOREIMPORTERRORS` |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  

### Suspend and Resume a Migration Job {#GUID-BE5A131F-549C-403C-8EFD-1846FBE65D69}

You can suspend a migration job at any point, and resume the job at any time.

To suspend a migration job run the following command.
```
zdmuser> $ZDM_HOME/bin/zdmcli suspend job -jobid job_id
```


What is the difference between Pause and Suspend?

A migration job "pause" is an event you schedule when you start the migration job, and "suspend" is an unscheduled event that you can employ while the migration job is running.

Resuming a Suspended Migration Job

A suspended job can be resumed any time by running the `ZDMCLI` `resume job` command, specifying the respective job ID. 
```
zdmuser> $ZDM_HOME/bin/zdmcli resume job -jobid Job_ID
[-pauseafter valid-phase]
```


You can optionally schedule a pause, by specifying the `–pauseafter` option in the `resume` command with a valid phase to be paused after. Choose a valid phase later than phase currently paused at, that is listed in the `migrate database ... -listphases` command output. 

When will the suspend interrupt a job?

Some migration job phases are interruptable and some are not.

A logical migration, when suspended, either interrupts the ongoing job phase, if the phase is interruptible, or it suspends the job after completion of the ongoing phase before the job is suspended, if the phase is not interruptible.

Following phases are interruptible for Oracle Data Pump: 

* `ZDM_DATAPUMP_EXPORT_SRC`
* `ZDM_TRANSFER_DUMPS_SRC`
* `ZDM_DATAPUMP_IMPORT_TGT`



The following phases are interruptible for Oracle GoldenGate:

* `ZDM_CREATE_GG_EXTRACT_SRC`
* `ZDM_CREATE_GG_REPLICAT_TGT`
* `ZDM_MONITOR_GG_LAG`
* `ZDM_SWITCHOVER_APP`



Once the suspend operation is completed, the job status is updated to `SUSPENDED`

### Rerun a Migration Job {#GUID-AE11CFF1-651E-4001-B3E4-6F91D44BF0A8}

If there are any unexpected errors in the migration workflow, you can correct them and rerun the migration job.

The errors are recorded in the job output, which can be queried using the ZDMCLI `query job` command. Upon resolving the error, the failed job can be continued from the point of failure. 

Rerun the migration job by running the ZDMCLI `resume job` command, specifying the job ID of the job to be rerun, as shown here. 
```
zdmuser> $ZDM_HOME/bin/zdmcli resume job -jobid Job_ID
```


### Terminate a Running Migration Job {#GUID-25B62358-6B9C-406C-84D2-89AE069DBF55}

If you want to resubmit a database migration job for a specified database, you must first terminate the running migration job.

Zero Downtime Migration blocks attempts to rerun the `MIGRATE DATABASE` command for a specified database if that database is already part of an ongoing migration job. 

If you want to resubmit a database migration job for a specified database, you must first terminate the running migration job in either `EXECUTING` or `PAUSED` state using the `ZDMCLI ABORT JOB` command. 
```
zdmuser> $ZDM_HOME/bin/zdmcli abort job -jobid job-id
```


### Modify Oracle GoldenGate Processes During a Migration Job {#GUID-B9AFB40F-3E8C-449E-9977-FAB72F3AA5D5}

You can modify the Oracle GoldenGate Replicat and Extract process parameters during a running (in-flight) migration job.

To modify a running migration job run the following command.
```
zdmuser> $ZDM_HOME/bin/zdmcli modify job -jobid job_id -rsp response_file_path
```


This modify command will affect the migration job only if the specified response file contains at least one of the following parameters related to Oracle GoldenGate configuration:

* `GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE`
* `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE`



> **note:** Starting with Oracle Zero Downtime Migration 21c (21.4) release, the following properties are deprecated and will be desupported in a future release: 
    * `GOLDENGATESETTINGS_REPLICAT_MAPPARALLELISM`
    * `GOLDENGATESETTINGS_REPLICAT_APPLYPARALLELISM`
    * `GOLDENGATESETTINGS_REPLICAT_MAXAPPLYPARALLELISM`
    * `GOLDENGATESETTINGS_REPLICAT_MINAPPLYPARALLELISM`<br>
    These parameters are replaced by the `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE` parameter. If you configure the `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE` parameter, then you do not have to configure these deprecated parameters. However, if you do configure these deprecated parameters, then the `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE` parameter will not be valid. 

**Extract** parameters can only be updated between the migration job phases `ZDM_CREATE_GG_EXTRACT_SRC` (exclusive) and `ZDM_MONITOR_GG_LAG` (inclusive). 

**Replicat** parameters can only be updated between the migration job phases `ZDM_CREATE_GG_REPLICAT_TGT` (exclusive) and `ZDM_MONITOR_GG_LAG` (inclusive) 

After Zero Downtime Migration updates the parameters for a GoldenGate process, it restarts the process, unless the migration job is suspended, in which case the GoldenGate process will remain stopped.

### Handling Application Switchover in a Logical Migration {#GUID-1455CCE0-A583-452B-8FA0-5B5F343EFE17}

The following procedure ensures zero data loss during a read-write application switchover.

Both the source and target databases are opened read-write during the logical migration work flow. The following conditions apply:

* For **read-only** applications, switchover can happen immediately after GoldenGate Replicat has applied all outstanding source transactions, allowing for zero application downtime for those services. 

* **Read-write** applications require assurance that all transactions have been applied on the target before switching the application over to ensure zero data loss. 




If your application is read-write, you must do the following to ensure zero data loss:

1. Pause the Zero Downtime Migration job after phase `ZDM_MONITOR_GG_LAG`. 

This phase monitors Oracle GoldenGate Extract and Replicat operations until Replicat has caught up on the target database--end to end (E2E) replication lag should be less than 30 seconds.

2. After phase `ZDM_MONITOR_GG_LAG` completes and the migration job pauses, stop the workload on the source database (start of downtime). 

If applications are configured to use database services, then you can use `srvctl` stop service command to stop services using `-drain_timeout`. See [Ensuring Application Continuity](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/ensuring-application-continuity.md#GUID-C1EF6BDA-5F90-448F-A1E2-DC15AD5CFE75) for more information. 

> **note:** The application must be aware that upon the expiry of drain timeout, the database services will be stopped and sessions using those services will be terminated. 

3. Resume the migration job, scheduling another pause after phase `ZDM_PREPARE_SWITCHOVER_APP`. 

    This phase does the following:
    
    1. Ensures replication E2E lag is still less than 30 seconds
    
    2. Ensures that Extract has captured outstanding transaction on the source database
    
    3. Stops Extract
    
    4. Ensures Replicat has applied all remaining trail file data
    
    5. Stops Replicat

4. After phase `ZDM_PREPARE_SWITCHOVER_APP` completes and the migration job pauses, resume the migration job as follows: 
    1. (Optional) If one or more objects were excluded from replication, then the objects need to be reloaded in the target database at this time. Resume the migration job, scheduling another pause after phase `ZDM_RELOAD_PARALLEL_EXPORT_IMPORT`. This phase will execute Data Pump Export and Import to reload the necessary objects. 
    2. Resume the migration job, scheduling another pause after phase `ZDM_SWITCHOVER_APP`. 
5. ZDM now automatically advances the sequence values in the target database during switchover. For more details refer to the `ZDM_ADVANCE_SEQUENCES` phase in [Zero Downtime Migration Process Phases](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-DA18E66B-2943-4C67-91B7-0CF3E0B0DC6B). 
6. After phase `ZDM_SWITCHOVER_APP` completes and the migration job pauses, you can verify the consistency of data between the source and target databases. You must ensure that the sequences in the target database have the correct value. If required, increment the sequences in the target database until the next value is high enough. You can run the following query in the source database to generate a SQL script.
```
select 'alter sequence '||SEQUENCE_OWNER||'."'||SEQUENCE_NAME||'" restart
start with '|| LAST_NUMBER||';'
from dba_sequences
where SEQUENCE_OWNER in (
select username
from dba_users
where nvl(oracle_maintained, 'N') = 'N');
```


Run the above SQL script in target database to advance the sequences. Resume the migration job, scheduling another pause after the `ZDM_POST_SWITCHOVER_TGT` phase. 
7. After the `ZDM_POST_SWITCHOVER_TGT` phase completes and the migration job pauses, you can start the workload on the target database (end of downtime). 

> **note:** Refer to the `GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS` and `RELOADOBJECTS-n` parameters for enabling the reload feature. 




### Zero Downtime Migration Centralized Fleet Migration Management {#GUID-AF7AEEE1-2DF5-4C3A-BE9F-FBDCD50FC38F}

Zero Downtime Migration allows you to centrally monitor fleet level migration using the Object Storage Service PAR URL.

Zero Downtime centralized fleet migration management gives you the following capabilities.

* Fleet level migration monitoring
* Fleet level log aggregation for easy troubleshooting and data mining
* Centralized migration per job metrics collection, which can be leveraged for executive-level migration status dash-boards and future migration forecasting



Centralized fleet migration management also lets you to prohibit the operation team from accessing the source or target database server for migration failures. The operation team can be allowed to troubleshoot the failure with the logs available in the specified `ZDM_LOG_OSS_PAR_URL` or logs further staged to a different Oracle Cloud storage bucket for operation troubleshooting. 

Centralized fleet migration management is only supported for physical migration jobs, and is enabled by setting the parameter` ZDM_LOG_OSS_PAR_URL` to a pre-authenticated URL, for example: 

https://objectstorage.us-*`region`*.oraclecloud.com/ … /DEV_ZDM_LOGS_*`RGN`*/o/ 

Zero Downtime Migration uploads the job specific data, shown in the table below, to the specified OSS PAR URL at regular interval while a migration job is in progress.

**Table: Centralized Fleet Migration Job Data**

Category | URL Name space Suffix (Appended to value of ZDM_LOG_OSS_PAR_URL) | Content  
---|---|---  
Job Metrics |  `*`PAR_URL`*/1/*`POD_NAME`*/*`ZDM_HOST`*/*`JOB_ID`*/METRICS.txt`<br>For example,<br>`/1/PRD_TEST_MIGRATION/s16izp/1/METRICS.txt` |  Job Metrics details the source and target database vitals and backup and restore statistics per phase involved in the work flow and error. This data can be used for fleet level migration statistics for executive dashboards.  
Job Status | *`PAR_URL`*/1*`POD_NAME`*/*`ZDM_HOST`*/*`JOB_ID`*/CURRENT_PHASE.txt`<br>For example,<br>` /1/PRD_TEST_MIGRATION/s16izp/1/CURRENT_PHASE.txt` |  Status entries are listed in the format<br>`* Phase Name`*:*`Phase Status`*`<br>For example, ``ZDM_BACKUP_FULL_SRC:EXECUTING``<br>Phase Status values are `EXECUTING`, `PAUSED`, `FAILED`, or `COMPLETED`  
Phase Log |  *`PAR_URL`*/2/*`TIMESTAMP`*/*`POD_NAME`*/*`ZDM_HOST`*/*`JOB_ID`*/*`ACTION HOST`*/*`PHASE_NAME`*.log <br>For example,<br>2/2011-11-07T17:58:34.049000/PRD_TEST_MIGRATION/2-zdm-01/1/cldx01/ZDM_BACKUP_FULL_SRC.log |  Zero Downtime Migration uploads the phase-specific logs at the end of each phase with a UTC time-stamped name space. On job rerun, the time stamp would change and the rerun-specific log would be identified by latest time stamp.  
Phase Status | *`PAR_URL`*/1/*`POD_NAME`*/*`ZDM_HOST`*/*`JOB_ID`*/*`PHASE_NAME`*.txt`<br>For example,<br>` /1/PRD_TEST_MIGRATION/s16izp/1/ZDM_VALIDATE_TGT.txt` |  The file contents will be `COMPLETED` if the phase is completed or `PENDING` if it has yet to start. <br>At the start of a migration job, Zero Downtime Migration creates all of the phase-named files with `PENDING` status, then updates them to `COMPLETED` as each phase is completed. <br>Use the creation time of `* PHASE_NAME`*.txt` to measure the phase elapsed time.   
Progress Message | *`PAR_URL`*/1/*`POD_NAME`*/*`ZDM_HOST`*/*`JOB_ID`*/CONSOLE.out`<br>For example,<br>`1/PRD_TEST_MIGRATION/s16izp/1/CONSOLE.out` |  Zero Downtime Migration updates `CONSOLE.out` for each *`n`* chunk of console messages received (for example, every 10 lines) normally, or instantly in case of errors. <br>Progress messages are formatted like this:<br>`* node name`* : *`UTC Time`* : *`Progress message`*`<br>For example, `hxvdbfz03: 2021-02-10T09:51:11.851Z : Retrieving information from source node "hfzb06" ...`
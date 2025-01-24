## GZero Downtime Migration Hybrid Migration Response File Parameters Reference {#GUID-1D38C5DA-89B4-4786-BE33-BD94A8ADDCF2}

The following topics describe the Zero Downtime Migration response file parameters used in hybrid migrations.

### MIGRATION_METHOD {#GUID-D2F5A8B6-5323-4A97-AA03-834BD717AFB7}

Specifies the method of migration.

Property | Description  
---|---  
**Syntax** |  `MIGRATION_METHOD = {OFFLINE_XTTS}`  
**Default value** |  `OFFLINE_XTTS`  
**Range of values** |  `OFFLINE_XTTS`  
**Required** |  Yes  
**Modifiable on Resume** |  No  

### DATA_TRANSFER_MEDIUM {#GUID-4DD5FBEC-B568-4818-93A2-0D54615E6070}

Specifies how data will be transferred from the source database system to the target database system.

See also [Configuring the Transfer Medium and Specifying Transfer Nodes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-3580F7B3-03A5-4ADA-9C2F-FEADF9E125A7)

Property | Description  
---|---  
**Syntax** |  `DATA_TRANSFER_MEDIUM = { NFS }`  
**Default value** |  `NFS`  
**Range of values** |  `NFS` \- Network File System   
**Required** |  Yes  
**Modifiable on Resume** |  No  

### TARGETDATABASE_ADMINUSERNAME {#GUID-BF445346-B836-44FA-9159-933D57AD0D50}

Specifies the target database administrator user name.

Parameter Relationships

For example, for a co-managed database, the migration user name can be `system` and for an Autonomous Database, the migration user name can be `admin`. The `TARGETDATABASE_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_ADMINUSERNAME = *`username`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_ADMINUSERNAME {#GUID-D7960033-C048-4737-AA9B-5A8D0625D141}

Specifies the source database administrator user name.

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_ADMINUSERNAME = *`source administrator username`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_IMPORTPARALLELISMDEGREE {#GUID-86E31642-3E53-48D2-B023-BA2627DF9775}

Specifies the maximum number of worker processes that can be used for a Data Pump Import job.

For migration to an Autonomous Database target, Zero Downtime Migration automatically queries its CPU core count and sets this parameter.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_IMPORTPARALLELISMDEGREE = *`integer`*`  
**Default value** |  There is no default value  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXPORTPARALLELISMDEGREE {#GUID-68E4D233-52CC-4CD8-9FC6-C17836CD6F41}

Specifies the maximum number of worker processes that can be used for a Data Pump Import job.

For migration to an Autonomous Database target, Zero Downtime Migration automatically queries its CPU core count and sets this parameter.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXPORTPARALLELISMDEGREE = *`integer`*`  
**Default value** |  There is no default value  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_NOCLUSTER {#GUID-3787B8EC-A881-48ED-A731-6BD42B28B80D}

Specifies whether all Data Pump workers are started on the current instance or on instances usable by the job.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_NOCLUSTER = [TRUE | FALSE]`  
**Default value** |  `FALSE`  
**Valid values** |  `TRUE` all Data Pump workers are started on the current instance <br>`FALSE` Data Pump workers are started on instances usable by the job   
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_EXPORTVERSION {#GUID-52855ED7-2E8A-4AE8-B0BA-8556FBA85F1D}

Specifies the Data Pump job export version.

See Oracle Data Pump documentation for Data Pump Export Version.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_EXPORTVERSION = ["COMPATIBLE","12","LATEST"]`  
**Default value** |  `COMPATIBLE`, if the target database is of same or higher version than that of source database, and specific version, if the target database is a lower version than that of source database.   
**Required** |  No  

### DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_NAME {#GUID-0A0F601F-30C7-42BF-B917-14BD3FCFA514}

Specifies the object name of a directory on server file system of an on-premises database.

In lieu of a network database link, a directory on the server file system of an on-premises database, specified with the `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_*` properties is used to store Data Pump Export dump files. 

Zero Downtime Migration creates this object if it does not already exist

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_NAME = *`Name`*`  
**Default value** |  There is no default value  
**Range of values** |  Name of directory object in the database.  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_PATH {#GUID-2EA3B02C-DD18-4A3A-BE9B-9DA6E84D1D74}

Specifies the absolute object path of a directory on server file system of an on-premises database.

In lieu of a network database link, a directory on the server file system of an on-premises database, specified with the `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_*` properties is used to store Data Pump Export dump files. 

Zero Downtime Migration creates this object if it does not already exist

> **note:** For Oracle Database 19c and later releases, the `UTL_FILE_DIR` initialization parameter is desupported, which means symbolic links for Data Pump directories are not supported. 
If you attempt to use an affected feature configured with symbolic links, then you encounter ORA-29283: invalid file operation: path traverses a symlink. Oracle recommends that you instead use directory objects in place of symbolic links.
See [How Does Oracle Data Pump Move Data?](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=SUTIL-GUID-3F418F02-5FE2-455A-B5AD-C1910DB3B5E0) for more information. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_PATH = *`Path`*`  
**Default value** |  There is no default value  
**Range of values** |  Absolute path of directory on database server  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME {#GUID-1F3181DD-0502-45AC-8D15-C4EBA3B68090}

Specifies the name of an import directory object.

In lieu of a network database link, a directory on the server file system of OCI database is used to store Data Pump dump files.

Zero Downtime Migration creates this object if it does not already exist.

For Autonomous Database migration targets, the `DATA_PUMP_DIR` object will already exist. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME = *`Name of the directory object`*`  
**Default value** |  There is no default value  
**Range of values** |  Name of directory object in database  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_PATH {#GUID-631A8378-51E8-4833-B6EF-3C3A87E285BE}

Specifies the absolute path of an import directory object.

In lieu of a network database link, a directory on the server file system of OCI database is used to store Data Pump dump files.

Zero Downtime Migration creates this object if it does not already exist.

For Autonomous Database migration targets, the `DATA_PUMP_DIR` object will already exist. 

> **note:** For Oracle Database 19c and later releases, the `UTL_FILE_DIR` initialization parameter is desupported, which means symbolic links for Data Pump directories are not supported. 
If you attempt to use an affected feature configured with symbolic links, then you encounter ORA-29283: invalid file operation: path traverses a symlink. Oracle recommends that you instead use directory objects in place of symbolic links.
See [How Does Oracle Data Pump Move Data?](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=SUTIL-GUID-3F418F02-5FE2-455A-B5AD-C1910DB3B5E0) for more information. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_PATH = *`Path of the directory object`*`  
**Default value** |  There is no default value  
**Range of values** |  Absolute path of directory on database server  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT` phase is `COMPLETED`.   

### DUMPTRANSFERDETAILS_PUBLICREAD {#GUID-4E2EA178-AACC-45A0-B080-26605D6A8FB1}

Specifies if the dump files on NFS are readable for others.

Parameter Relationships

This option allows target database user to read the dumps on the shared path when the dump files are allowed to be read by public. 

This helps when the user accessing source database server is not allowed to change the group ownership on dumps on the shared path.

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_PUBLICREAD = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE`: Dump files on NFS are readable by public. <br>`FALSE`: Dump files on NFS are not readable by public.   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### IGNOREEXPORTERRORS {#GUID-B524426A-F827-4B43-B4E2-0C29DECDEBB0}

Specifies comma separated list of ORA errors to be ignored for Oracle Data Pump export. For any new error to be ignored, suffix the new error to the list of default errors to be ignored. 

Property | Description  
---|---  
**Syntax** |  `IGNOREEXPORTERRORS = ORA_error_list`  
**Default value** |  `ORA-31684,ORA-39111,ORA-39082`  
**Range of values** |  `ORA-31684,ORA-39111,ORA-39082`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`.   

### IGNOREIMPORTERRORS {#GUID-A8DD6CB7-771E-4486-8E26-8C89BA45378B}

Specifies comma separated list of ORA errors to be ignored for Oracle Data Pump import. For any new error to be ignored, suffix the new error to the list of default errors to be ignored. 

Property | Description  
---|---  
**Syntax** |  `IGNOREIMPORTERRORS = ORA_error_list`  
**Default value** |  `ORA-31684,ORA-39111,ORA-39082`  
**Range of values** |  `ORA-31684,ORA-39111,ORA-39082`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`.   

### RMANSETTINGS_SRCCHANNELS {#GUID-EECB54D9-F038-40CC-B70D-028C6F1F1116}

Specifies the number of RMAN channels to be allocated at the source for performing RMAN backups.

Property | Description  
---|---  
**Syntax** |  `RMANSETTINGS_SRCCHANNELS = integer`  
**Default value** |  ` 10`  
**Range of values** |  `0` to <br>`10`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_XTTS_BACKUP_INCR_RO_SRC` phase is COMPLETED.   

### RMANSETTINGS_TGTCHANNELS {#GUID-1D6EAC13-B45F-4E2F-8807-DB2B84356DD1}

Specifies the number of RMAN channels to be allocated at the source for performing RMAN backups.

Property | Description  
---|---  
**Syntax** |  `RMANSETTINGS_TGTCHANNELS = integer`  
**Default value** |  ` 10`  
**Range of values** |  `0` to <br>`10`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_XTTS_RESTORE_INCR_TGT` phase is COMPLETED.   

### RMANSETTINGS_RETENTIONWINDOW {#GUID-9324EE53-501A-4813-8876-F2D0E1658F5A}

Specifies the number of days after which backups created by ZDM become obsolete.

Property | Description  
---|---  
**Syntax** |  `RMANSETTINGS_RETENTIONWINDOW = integer`  
**Default value** |  ` `  
**Range of values** |   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_XTTS_BACKUP_INCR_RO_SRC` phase is COMPLETED.   

### RMANSETTINGS_BACKUPTAG {#GUID-EA3C63C5-9EBE-4D85-BE33-D6243F83942E}

Specifies the backup tag to create or use backup. By default ZDM will generate a job# specific backup tag.

Property | Description  
---|---  
**Syntax** |  `RMANSETTINGS_BACKUPTAG = integer`  
**Default value** |  ` `  
**Range of values** |   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_XTTS_BACKUP_FULL_SRC` phase is COMPLETED.   

### RMANSETTINGS_SECTIONSIZE {#GUID-385B3961-5E4B-4DC7-BBE4-AC39A7DD5D66}

Specifies the backup tag RMAN section size to use for backups. Specify the value as 0 to skip using section size.

Property | Description  
---|---  
**Syntax** |  `RMANSETTINGS_SECTIONSIZE = {K\|M\|G\|T}>`  
**Default value** |  ` 64G`  
**Range of values** |   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_XTTS_BACKUP_INCR_RO_SRC` phase is COMPLETED.   

### RMANSETTINGS_COMPRESSIONALGORITHM {#GUID-AB1D8B22-4CB2-439B-BC04-7A2F4A63E194}

Specifies the RMAN Compression algorithm to use for backups.

Property | Description  
---|---  
**Syntax** |  ` RMANSETTINGS_COMPRESSIONALGORITHM = BASIC| LOW | HIGH | MEDIUM | NONE`  
**Default value** |  `MEDIUM`  
**Range of values** | * BASIC: Basic Compression Algorithm<br>LOW: Least impact on backup throughput and suited for environments where CPU resources are the limiting factor.<br>HIGH: Best suited for backups over slower networks where the limiting factor is network speed.<br>MEDIUM: Recommended for most environments. Good combination of compression ratios and speed. <br>NONE: Do not use compression.
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_XTTS_BACKUP_INCR_RO_SRC` phase is COMPLETED.   

### RMANSETTINGS_PUBLICREAD {#GUID-BB31E5B2-DE1E-47E8-9B97-126F27461F0E}

Specifies the backup files on shared storage to be made readable for others. This option allows target database user to read backups on shared path as the backup file directory is allowed to be read by public. This helps if the user accessing source database server is not allowed to change the group ownership on backups on shared path.

Property | Description  
---|---  
**Syntax** |  ` RMANSETTINGS_PUBLICREAD = TRUE| FALSE`  
**Default value** |  `FALSE`  
**Range of values** |  TRUE or FALSE  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_XTTS_BACKUP_INCR_RO_SRC` phase is COMPLETED.   

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE {#GUID-7BB86AE0-DD60-4ED9-813B-073F1AB2ED0D}

Specifies the identity file to access the bastion for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE = *`identity file`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP {#GUID-8715F8CE-E599-4BAD-AA13-3E0E1EF33784}

Specifies the IP address of the bastion host for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP = *`ip address`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT {#GUID-7649FC3E-3E97-4E06-BB22-CCB65C4B8613}

Specifies the port number of the bastion host for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT = *`port_number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP {#GUID-B54114EC-FA5B-495D-9387-7F0561A42A8F}

Specifies the remote host IP address to access from the bastion for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP = *`ip_address`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME {#GUID-ECB3122E-FED4-408B-A0BD-F647EFF4D962}

Specifies the user name to access the bastion for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME = *`username`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME {#GUID-E98D3DB0-7504-4B48-AE1B-D81F8A987957}

Specifies the proxy host name to connect to the source database through an HTTPS proxy.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME = *`proxy host name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT {#GUID-D8A5717F-5753-4567-88BC-849325586E75}

Specifies the HTTP proxy port number to connect to the source database through an HTTPS proxy.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT = *`proxy port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL {#GUID-C8AC476A-58F2-4773-8381-D8546E4E6F33}

Specifies the proxy protocol to connect to the source database through a proxy.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL = HTTP or HTTPS`  
**Default value** |  `HTTP`  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_ENVIRONMENT_DBTYPE {#GUID-636D33E1-A385-47A8-8046-D68C1EC5083C}

Indicates the type of database to migrate from the specified environment.

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_ENVIRONMENT_DBTYPE = *`dbtypeValue`*`  
**Default value** |  `ORACLE`  
**Required** |  No  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_ENVIRONMENT_NAME {#GUID-02FC755D-3FB9-4237-92F6-66589F4AF7A3}

Specifies the environment of the source database.

You can specify whether the source database environment is Oracle (`ORACLE`). 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_ENVIRONMENT_NAME = *`nameValue`*`  
**Default value** |  `ORACLE`  
**Required** |  No  
**Modifiable on Resume** |  No  

### TARGETDATABASE_CONNECTIONDETAILS_HOST {#GUID-D7C37ADB-4D6F-4FD9-95BD-7966E533DB97}

Specifies the listener host name or IP address.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS` parameters specify connection details for the target OCI database. 

These properties are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_HOST = *`hostname_or_ip`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_PORT {#GUID-75823FAA-355C-4463-B9D3-C1D7D7BA663F}

Specifies the listener port number.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_PORT = *`listener port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_SERVICENAME {#GUID-E9F0F9BA-FDD5-4043-A0F3-B619232C0F20}

Specifies the fully qualified service name.

This parameter is optional for Oracle Autonomous Database targets; however if an HTTP proxy is required to connect, specify it.

In addition, for Oracle Autonomous Database on Dedicated Exadata Infrastructure and Oracle Autonomous Database on Exadata Cloud@Customer with **fractional OCPU** service you must specify the appropriate service alias in the parameter. 

You can specify any predefined fractional service alias available; however, for Autonomous Transaction Processing workloads TP* services are preferred over LOW* services because LOW* is meant for low priority batch jobs.

* `TP_TLS`, `TP`, `LOW_TLS` or `LOW` (for Autonomous Transaction Processing workloads) 
* `LOW_TLS` or `LOW` (for Autonomous Data Warehouse workloads) 



See also [Connecting to a DB System](https://docs.cloud.oracle.com/en-us/iaas/Content/Database/Tasks/connectingDB.htm) and [About Connecting to a Dedicated Autonomous Database](https://docs.oracle.com/en-us/iaas/autonomous-database/doc/connecting.md)

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_SERVICENAME = *`service_name_or_alias`*`  
**Default value** |  There is no default value  
**Valid values** |  For **non-Autonomous**, specify the fully qualified service name. <br>For **Autonomous**, you can specify the service alias for fractional OCPU service as: <br>* `TP_TLS`, `TP`, `LOW_TLS` or `LOW` (for Autonomous Transaction Processing workloads) <br>`LOW_TLS` or `LOW` (for Autonomous Data Warehouse workloads) 
**Required** |  No  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_CONNECTIONDETAILS_HOST {#GUID-971F9A6B-E51E-4818-ABE4-ED79952DAEAC}

Specifies the source database listener host name or IP address. Required for all except `environment.name` = `ORACLE` and `environment.dbType` = `ADBS|ADBD`. In case of Oracle RAC database, the scan name can be specified. 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_HOST = *`hostname_or_ip`*`  
**Default value** |  There is no default value  
**Required** |  Yes*<br>*Not required for Autonomous Database.  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PORT {#GUID-73394D81-1868-4FDC-B7C1-C8D678C0C7D1}

Specifies the source database listener port number. Required for all except `environment.name` = `ORACLE` and `environment.dbType` = `ADBS|ADBD`. 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PORT = *`listener port number`*`  
**Default value** |  There is no default value  
**Required** |  Yes*<br>*Not required for Autonomous Database.  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME {#GUID-6738A990-C271-4252-87F2-30781D7CF007}

Specifies the source database fully qualified service name. Required for all except `environment.name` = `ORACLE` and `environment.dbType` = `ADBS|ADBD`. 

Parameter Relationships

Example, `. for all Environment.DbType except ORACLE.ADBS and ORACLE.ADBD  for Environment.DbType = ORACLE.ADBS OR ORACLE.ADBD`. See [Overview of Connecting to a DB System](https://docs.oracle.com/en-us/iaas/base-database/doc/overview-connecting-db-system.md) for more information. 

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME = *`service name`*`  
**Default value** |  There is no default value  
**Required** |  Yes*<br>*Not required for Autonomous Database  
**Modifiable on Resume** |  No  

### TABLESPACEDETAILS_EXCLUDE {#GUID-DC688D66-FA56-40A2-827A-49E0A791303F}

Specifies tablespaces to be excluded for automatic remap or automatic creation of tablespaces at target database during import of user schemas.

See [Automatic Tablespace Creation](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C23B404A-9C3A-48A3-A265-D01E6774914F) for more information. 

Example

`TABLESPACEDETAILS_EXCLUDE=B2B_LOB_TS,B2B_HR_TS`

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_EXCLUDE=*`tablespace_names`*`  
**Default value** |  By default SYSTEM, SYSAUX, and USERS tablespaces are excluded.  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE {#GUID-8CC0B207-C361-45E0-B594-97383136F4B2}

Specifies the identity file to access the bastion for bastion-based access to the target database.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE = *`bastion_id_file`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP {#GUID-1DBFB20D-3762-4B7C-9F78-EF8317D16339}

Specifies the IP address of the bastion host for bastion-based access to database.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP = *`ip address`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT {#GUID-6C65F601-A7EC-4087-825B-8BFF62424803}

Specifies the port number of the bastion host for bastion-based access to database.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT = *`port_number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP {#GUID-DE80B630-CB0C-4228-B62F-09793609A63E}

Specifies the remote host IP address to access from the bastion for bastion-based access to database.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP = *`ip_address`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME {#GUID-4FE56B96-C885-4DBC-9910-3ABB6032C305}

Specifies the user name to access the bastion for bastion-based access to database.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME = *`username`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME {#GUID-A4C8506F-8C1C-4D10-987D-4883B1BDD387}

Specifies the proxy host name for connecting to the target database through an HTTPS proxy.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME = *`proxy host name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT {#GUID-DF5B6B56-C0B8-4060-B228-C3A8B627C945}

Specifies the HTTP proxy port number for connecting to the source database through an HTTPS proxy.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT = *`proxy port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL {#GUID-547069C6-AF5F-479D-A7C5-3E41340D59DE}

Specifies the proxy protocol to connect to the target database through a proxy.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL = HTTP or HTTPS`  
**Default value** |  `HTTP`  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_DBTYPE {#GUID-4527893F-80FA-4CEC-BCD5-C23F1C51C737}

Specifies the target database type if OCID of the target database is not provided.

Parameter Relationships

The `TARGETDATABASE_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_DBTYPE = *`dbtypeValue`*`  
**Default value** |  There is no default value The following values are supported: 

* `ORACLE`<br>`EXADATA`<br>`ODA`


**Required** |  No  
**Modifiable on Resume** |  No  

### WALLET_DATAPUMPENCRYPTION {#GUID-352EA28A-0042-4AF8-9BA4-EBCB5D5022DD}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the Data Pump encryption password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_DATAPUMPENCRYPTION = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_SOURCEADMIN {#GUID-28BDF355-B647-4A8B-A82F-131730DEA197}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the source database administrative admin password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_SOURCEADMIN = *`wallet_path`*`  
**Default value** |  There is no default value  
**Range of value** |  Full path to the wallet.  
**Required** |  No  
**Modifiable on Resume** |  No  

### WALLET_TARGETADMIN {#GUID-9E1BDB81-5DB0-4826-8319-0C7FBF475B97}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the target database administrative admin password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_TARGETADMIN = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_TDEKEYSTORE {#GUID-36D5138C-0A48-4218-95B4-2BFB225419F4}

Specifies the absolute path location for wallet to be used for TDE keystore that contains the auto login wallet file cwallet.sso for specifying the source database TDE keystore password.

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_TDEKEYSTORE = *`wallet_path`*`  
**Default value** |  There is no default value  
**Range of value** |  Full path to the wallet.  
**Required** |  No  
**Modifiable on Resume** |  No  

### WALLET_TGTTDEKEYSTORE {#GUID-A00CF768-337E-4CBC-B552-3B10DB58D303}

Specifies the absolute path location for wallet to be used for the target TDE keystore that contains the auto login wallet file cwallet.sso for specifying the target database TDE keystore password.

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_TGTTDEKEYSTORE = *`wallet_path`*`  
**Default value** |  There is no default value  
**Range of value** |  Full path to the wallet.  
**Required** |  No  
**Modifiable on Resume** |  No  

### WALLET_USERACTION {#GUID-9500B33A-E1B1-4349-BFAA-1D4CEB60511D}

Allows you to provide the auto login wallet path, which contains the username and its password which should run the useraction.

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_USERACTION = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`. 
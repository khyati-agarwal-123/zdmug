## EZero Downtime Migration Physical Migration Response File Parameters Reference {#GUID-9DA94B7A-04C7-40D9-B3CA-8F680F618C3F}

The following topics describe the Zero Downtime Migration response file parameters used in physical migrations.

### BACKUP_PATH {#GUID-5C919828-772D-4772-AB90-2020BF02A3B5}

`BACKUP_PATH` specifies a valid path accessible at the source and target for migration backup type. 

Property | Description  
---|---  
**Syntax** |  `BACKUP_PATH = {STORAGEPATH \| EXTBACKUP}`  
**Default value** |  There is no default value.  
**Range of values** |  `STORAGEPATH` \- NFS backup location <br>`EXTBACKUP` \- external backup location <br>Leave this parameter value blank for other migration backup types  

### DATA_TRANSFER_MEDIUM {#GUID-1025DE55-99A0-474E-A6C4-FF41264C5114}

`DATA_TRANSFER_MEDIUM` specifies the media used for the source database backup, or you can specify a direct data transfer method. 

Property | Description  
---|---  
**Syntax** |  `DATA_TRANSFER_MEDIUM = {OSS \| EXTBACKUP \| ZDLRA \| NFS \| DIRECT}`  
**Default value** |  `OSS`  
**Range of values** | * `OSS` \- When `MIGRATION_METHOD=ONLINE_PHYSICAL`, Oracle Data Guard using OSS for standby initialization, supported for Oracle Cloud Infrastructure(OCI) virtual machine or bare metal, Exadata Cloud Service (EXACS) and Exadata Cloud at Customer (EXACC) When `MIGRATION_METHOD=OFFLINE_PHYSICAL`, specifying `OSS` will perform the migration using backup and restore through OSS, supported for Oracle Cloud Infrastructure(OCI) virtual machine or bare metal, Exadata Cloud Service (EXACS) and Exadata Cloud at Customer (EXACC) and no SQL*Net connectivity needed between source and target  <br>`EXTBACKUP` \- Oracle Data Guard with existing backup in external location, supported for Exadata Cloud at Customer (EXACC) <br>`ZDLRA` \- Oracle Data Guard using ZDLRA for standby initialization, supported for Exadata Cloud at Customer (EXACC) <br>`NFS` \- When `MIGRATION_METHOD=ONLINE_PHYSICAL`, Oracle Data Guard using backup location such as NFS, supported for Exadata Cloud at Customer (EXACC) When `MIGRATION_METHOD=OFFLINE_PHYSICAL`, specifying `NFS` performs the migration using backup and restore through NFS, supported for Exadata Cloud at Customer (EXACC) and no SQL*Net connectivity needed between source and target  <br>`DIRECT` \- RMAN active database duplication or restore from service to transfer data directly from the source to the target, This transfer method also uses the `ZDM_RMAN_DIRECT_METHOD` and `ZDM_SRC_DB_RESTORE_SERVICE_NAME` parameters. See [Direct Data Transfer Support](introduction-to-zero-downtime-migration.md#GUID-00701E56-0B91-42F3-AEA6-84A386B53B05) and [Setting Physical Migration Parameters](preparing-for-database-migration.md#GUID-892533DD-94BA-405F-8C27-47F02BA9E06C) for more information about using direct data transfer in a physical migration 



### DATAPATCH_WITH_ONE_INSTANCE_RUNNING {#GUID-602FCDEE-6D80-4594-A623-35789D2099C7}

`DATAPATCH_WITH_ONE_INSTANCE_RUNNING` specifies whether or not to stop all instances except one running on the target database server when the datapatch utility is run. When datapatch completes all of the stopped instances are started. 

Property | Description  
---|---  
**Syntax** |  `DATAPATCH_WITH_ONE_INSTANCE_RUNNING ={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Stops all instances except one running on the target database server when running the datapatch utility. <br>`FALSE` \- Does not stop all instances except one running on the target database server when running the datapatch utility.   

### HOST {#GUID-72FF1002-E495-4C51-A791-FB9E0A359D44}

`HOST` specifies the cloud storage REST endpoint URL to access Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `HOST = *`rest_endpoint_url`*`  
**Default value** |  There is no default value.  
**Range of values** |  For Oracle Cloud Infrastructure storage the typical value format is <br>`https://swiftobjectstorage.us-phoenix-1.oraclecloud.com/v1/*`ObjectStorageNamespace *`<br>For Oracle Cloud Infrastructure Classic storage the typical value format is <br>`https://acme.storage.oraclecloud.com/v1/Storage-*`tenancy name`*   

Usage Notes

To access Oracle Cloud Object Storage, you must set both the `HOST` and `OPC_CONTAINER` parameters. 

### MAX_DATAPATCH_DURATION_MINS {#GUID-D24E68FB-9C5E-426C-8EBE-5F96D41E402A}

`MAX_DATAPATCH_DURATION_MINS` specifies a timeout value, in minutes, after which if the datapatch utility has failed to complete then the operation is stopped. 

Property | Description  
---|---  
**Syntax** |  `MAX_DATAPATCH_DURATION_MINS = *`minutes`*`  
**Default value** |  There is no default value. Zero Downtime Migration waits until datapatch completes by default.  

### MIGRATION_METHOD {#GUID-59842D4B-618E-4371-BEB2-51834565FA37}

`MIGRATION_METHOD` specifies whether the migration uses Oracle Data Guard (online) or backup and restore (offline). 

Property | Description  
---|---  
**Syntax** |  `MIGRATION_METHOD = {ONLINE_PHYSICAL \| OFFLINE_PHYSICAL}`  
**Default value** |  This is no default value  
**Range of values** |  `ONLINE_PHYSICAL` uses Data Guard switchover migration method <br>`OFFLINE_PHYSICAL` uses backup and restore database migration method   

### NONCDBTOPDB_CONVERSION {#GUID-49307F5A-49EB-45F9-8556-2E2A42FA3D10}

`NONCDBTOPDB_CONVERSION` indicates whether to convert a source database from non-CDB to PDB or skip the conversion. 

Property | Description  
---|---  
**Syntax** |  `NONCDBTOPDB_CONVERSION = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` performs the conversion <br>`FALSE` does not perform the conversion   

### NONCDBTOPDB_SWITCHOVER {#GUID-453119BD-F5C7-480F-9008-4B5E6068C95C}

`NONCDBTOPDB_SWITCHOVER`, for a physical migration using Data Guard switchover, indicates whether the switchover operations will be executed during a migration job with non-CDB to PDB conversion enabled. 

Property | Description  
---|---  
**Syntax** |  `NONCDBTOPDB_SWITCHOVER = {TRUE \| FALSE}`  
**Default value** |  `TRUE`  
**Range of values** |  `TRUE` performs the switchover <br>`FALSE` does not perform the switchover   

### OPC_CONTAINER {#GUID-4F54BBA6-83C1-4D49-9329-F37D43E68872}

`OPC_CONTAINER` specifies the Object Storage bucket (called the container on Oracle Cloud Infrastructure Classic), and is set to access Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `OPC_CONTAINER = *`bucket`*`  
**Default value** |  There is no default value.  

Usage Notes

To access Oracle Cloud Object Storage, you must set both the `HOST` and `OPC_CONTAINER` parameters. 

The bucket is also referred to as a container for Oracle Cloud Infrastructure Classic storage.

### FLASHBACK_ON {#GUID-36E61844-6010-4FF9-98F6-329CEFA9E3CF}

`FLASHBACK_ON` specifies to enable or disable flashback on the target database during post-migration phase. 

Property | Description  
---|---  
**Syntax** |  `FLASHBACK_ON = {TRUE \| FALSE}`  
**Default value** |  There is no default value.  
**Range of values** | * `TRUE` = Enables `FLASHBACK_ON` <br>`FALSE` = Disables `FLASHBACK_ON`. 
**Required** |  No  
**Modifiable on Resume** |  Yes  

If you do not set the value for the `FLASHBACK_ON` parameter, then the source database's flashback status will remain on the target database after migration. 

### PLATFORM_TYPE {#GUID-1F035E46-1AAF-46AF-B190-FA8E085323AB}

`PLATFORM_TYPE` specifies the target database platform. 

Property | Description  
---|---  
**Syntax** |  `PLATFORM_TYPE = {VMDB \| EXACS \| EXACC \| NON_CLOUD}`  
**Default value** |  `VMDB`  
**Range of values** |  `VMDB` \- indicates target platform is Oracle Cloud Infrastructure(OCI) virtual machine or bare metal <br>`EXACS` \- indicates target platform is Exadata Cloud Service <br>`EXACC` \- indicates target platform is Exadata Cloud at Customer <br>`NON_CLOUD` \- indicates the target is an on-premises environment   

### SHUTDOWN_SRC {#GUID-A09ACA50-35AF-43C8-A7CA-4E7D830F79A2}

`SHUTDOWN_SRC` specifies whether or not to shut down the source database after migration completes. 

Property | Description  
---|---  
**Syntax** |  `SHUTDOWN_SRC ={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Shut down the source database after migration completes. <br>`FALSE` \- Dos not shut down the source database after migration completes.   

### SKIP_FALLBACK {#GUID-0A3A1D21-F7A4-48D3-842A-E3E19946052F}

`SKIP_FALLBACK` specifies whether or not to ship redo logs from the primary (target) database to the standby (source) database, either voluntarily or because there is no connectivity between the target and source database servers. 

Property | Description  
---|---  
**Syntax** |  `SKIP_FALLBACK = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- do not ship redo logs from the primary (target) database to the standby (source) database. <br>`FALSE` \- ship redo logs from the primary (target) database to the standby (source) database.   

### SKIP_SRC_SERVICE_RETENTION {#GUID-2FF64115-E105-4F4C-A53D-41CAE0900E85}

`SKIP_SRC_SERVICE_RETENTION` specifies whether or not to retain the source database services and run them on the target database. This parameter is only valid for `MIGRATION_METHOD=OFFLINE_PHYSICAL` . . 

Property | Description  
---|---  
**Syntax** |  `SKIP_SRC_SERVICE_RETENTION ={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Do not retain the source database services. <br>`FALSE` \- Retain the source database services.   

### SRC_BASTION_HOST_IP {#GUID-F7D2494E-2FE9-45D6-B9C9-C19BD4206B9E}

`SRC_BASTION_HOST_IP` specifies the bastion host IP address, if you want to connect to the source database server using a bastion host. 

Property | Description  
---|---  
**Syntax** |  `SRC_BASTION_HOST_IP = *`IP_address`*`  
**Default value** |  There is no default value.  

Usage Notes

If you want to connect to the source database server using a bastion host, values for the bastion host IP address parameter, `SRC_BASTION_HOST_IP`, and the source database host IP address parameter, `SRC_HOST_IP`, are required in the Zero Downtime Migration response file. 

If you do not want to use the default value, set the following parameters for bastion host connection.

`SRC_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`SRC_BASTION_USER` \- The bastion host source user is only required if the user specified for the source `zdmauth` plug-in is different from the user of the source bastion host. The bastion user defaults to the user specified for the source `zdmauth` plug-in if the parameter is not specified. 

`SRC_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the source `zdmauth` plug-in. 

### SRC_BASTION_IDENTITY_FILE {#GUID-12BCFBD1-ED5A-4762-B5A3-35F44C3B913B}

`SRC_BASTION_IDENTITY_FILE` specifies the identity file to access the bastion host as the indicated user. 

Property | Description  
---|---  
**Syntax** |  `SRC_BASTION_IDENTITY_FILE = *`identity_file`*`  
**Default value** |  If not specified, the value defaults to the value specified for the `identity_file` argument of the source `zdmauth` plug-in.   

Usage Notes

If you want to connect to the source database server using a bastion host, values for the bastion host IP address parameter, `SRC_BASTION_HOST_IP`, and the source database server IP address parameter, `SRC_HOST_IP`, are required in the Zero Downtime Migration response file. 

If you do not want to use the default value, set the following parameters for bastion host connection.

`SRC_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`SRC_BASTION_USER` \- The bastion host source user is only required if the user specified for the source `zdmauth` plug-in is different from the user of the source bastion host. The bastion user defaults to the user specified for the source `zdmauth` plug-in if the parameter is not specified. 

`SRC_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the source `zdmauth` plug-in. 

### SRC_BASTION_PORT {#GUID-9DE077E6-A3A8-4C91-93F6-0DD4ED6D225F}

`SRC_BASTION_PORT` specifies the bastion host port number, if you want to connect to the source database server using a bastion host. 

Property | Description  
---|---  
**Syntax** |  `SRC_BASTION_PORT = *`port_number`*`  
**Default value** |  `22`  

Usage Notes

If you want to connect to the source database server using a bastion host, values for the bastion host IP address parameter, `SRC_BASTION_HOST_IP`, and the source database server IP address parameter, `SRC_HOST_IP`, are required in the Zero Downtime Migration response file. 

If you do not want to use the default value, set the following parameters for bastion host connection.

`SRC_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`SRC_BASTION_USER` \- The bastion host source user is only required if the user specified for the source `zdmauth` plug-in is different from the user of the source bastion host. The bastion user defaults to the user specified for the source `zdmauth` plug-in if the parameter is not specified. 

`SRC_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the source `zdmauth` plug-in. 

### SRC_BASTION_USER {#GUID-3819F53B-08E7-42EE-88DD-B65CBCAD1604}

`SRC_BASTION_USER` specifies the bastion user, if you want to connect to the source database server using a bastion host. 

Property | Description  
---|---  
**Syntax** |  `SRC_BASTION_USER = *`bastion_user`*`  
**Default value** |  The bastion user defaults to the user specified for the source `zdmauth` plug-in if the parameter is not specified.   

Usage Notes

If you want to connect to the source database server using a bastion host, values for the bastion host IP address parameter, `SRC_BASTION_HOST_IP`, and the source database server IP address parameter, `SRC_HOST_IP`, are required in the Zero Downtime Migration response file. 

If you do not want to use the default value, set the following parameters for bastion host connection.

`SRC_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`SRC_BASTION_USER` \- The bastion host source user is only required if the user specified for the source `zdmauth` plug-in is different from the user of the source bastion host. The bastion user defaults to the user specified for the source `zdmauth` plug-in if the parameter is not specified. 

`SRC_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the source `zdmauth` plug-in. 

### SRC_CONFIG_LOCATION {#GUID-D3CB5156-7968-4888-9921-80FB910D1637}

`SRC_CONFIG_LOCATION` specifies the SSH configuration file location on the Zero Downtime Migration service host (host where Zero Downtime Migration service is running). 

Property | Description  
---|---  
**Syntax** |  `SRC_CONFIG_LOCATION = *`SSH_config_file_path`*`  
**Default value** |  `*`User_home`*/.ssh/config`  

Usage Notes

Set `SRC_CONFIG_LOCATION` to the full path of the SSH configuration file location on the Zero Downtime Migration service host, for example, `/home/crsuser/.ssh/config`. 

### SRC_DB_LISTENER_PORT {#GUID-04572B12-AFB1-4A58-A66A-33BEE4FDE79F}

`SRC_DB_LISTENER_PORT` indicates the source database listener port. Set this property when the source database is a standalone database (no Grid Infrastructure) configured with non-default SCAN listener port other than 1521. 

Property | Description  
---|---  
**Syntax** |  `SRC_DB_LISTENER_PORT = *`listener_port_number`*`  
**Default value** |  1521  

### SRC_HOST_IP {#GUID-6D02959B-99FA-44FA-B203-B5BCA71FA973}

`SRC_HOST_IP` specifies the IP to be used to connect to the source database host from the bastion host. This IP would not be reachable from the ZDM host. It is expected to be reachable from the bastion host. 

Property | Description  
---|---  
**Syntax** |  `SRC_HOST_IP = *`IP_address`*`  
**Default value** |  There is no default value.  

Usage Notes

If you want to connect to the source database server using a bastion host, values for the bastion host IP address parameter, `SRC_BASTION_HOST_IP`, and the source database server IP address parameter, `SRC_HOST_IP`, are required in the Zero Downtime Migration response file. 

If you do not want to use the default value, set the following parameters for bastion host connection.

`SRC_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`SRC_BASTION_USER` \- The bastion host source user is only required if the user specified for the source `zdmauth` plug-in is different from the user of the source bastion host. The bastion user defaults to the user specified for the source `zdmauth` plug-in if the parameter is not specified. 

`SRC_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the source `zdmauth` plug-in. 

### SRC_HTTP_PROXY_PORT {#GUID-EA7F4AB4-F244-47B8-8D88-6A64A74ECFEC}

`SRC_HTTP_PROXY_PORT` specifies the HTTPS proxy port number on the source database server if an SSH connection needs to connect using a proxy. 

Property | Description  
---|---  
**Syntax** |  `SRC_HTTP_PROXY_PORT = *`https_proxy_port_number`*`  
**Default value** |  There is no default value.  

### SRC_HTTP_PROXY_URL {#GUID-994E151B-EB00-42DF-8012-ECB7EB8BD909}

`SRC_HTTP_PROXY_URL` specifies the HTTPS proxy URL on the source database server if an SSH connection needs to connect using a proxy. 

Property | Description  
---|---  
**Syntax** |  `SRC_HTTP_PROXY_URL = *`https_proxy_url`*`  
**Default value** |  There is no default value.  

### SRC_OSS_PROXY_HOST {#GUID-91B32440-26E0-4ABE-B3F2-D8199D66EA06}

`SRC_OSS_PROXY_HOST` specifies the Object Storage Service proxy host on the source database server if a proxy is needed for connecting to the Object Store. 

Property | Description  
---|---  
**Syntax** |  `SRC_OSS_PROXY_HOST = *`oss_proxy_host`*`  
**Default value** |  There is no default value.  

Usage Notes

Set both the `SRC_OSS_PROXY_HOST` and `SRC_OSS_PROXY_PORT` parameters if a proxy is needed for connecting to the Object Store. 

### SRC_OSS_PROXY_PORT {#GUID-061FEFFA-1A38-4928-AD28-4914CA524444}

`SRC_OSS_PROXY_PORT` specifies the Object Storage Service proxy port number on the source database server if a proxy is needed for connecting to the Object Store. 

Property | Description  
---|---  
**Syntax** |  `SRC_OSS_PROXY_PORT = *`oss_proxy_port_number`*`  
**Default value** |  There is no default value.  

Usage Notes

Set both the `SRC_OSS_PROXY_HOST` and `SRC_OSS_PROXY_PORT` parameters if a proxy is needed for connecting to the Object Store. 

### SRC_RMAN_CHANNELS {#GUID-F42FBAFE-557C-4A70-9702-4E4E55A6EBD7}

`SRC_RMAN_CHANNELS` specifies the number of RMAN channels to be allocated at the source database server for performing RMAN backups. 

Property | Description  
---|---  
**Syntax** |  `SRC_RMAN_CHANNELS = *`number_of_channels`*`  
**Default value** |  `10`  

### SRC_SSH_RETRY_TIMEOUT {#GUID-330CD510-ACA3-463A-A7E3-13EC124D1B8E}

`SRC_SSH_RETRY_TIMEOUT` specifies a timeout value, in minutes, after which Zero Downtime Migration stops attempting SSH connections after an initial failure to connect. 

Property | Description  
---|---  
**Syntax** |  `SRC_SSH_RETRY_TIMEOUT = *`number_of_minutes`*`  
**Default value** |  There is no default value.  

### SRC_TIMEZONE {#GUID-B6C380A9-6466-4AAB-B1AE-A313B3EC7ED4}

`SRC_TIMEZONE` specifies the source database server time zone, which is needed for Single Instance Source Databases where there is no Grid Infrastructure configured. 

Property | Description  
---|---  
**Syntax** |  `SRC_TIMEZONE = *`source_db_time_zone`*`  
**Default value** |  There is no default value.  

### SRC_ZDLRA_WALLET_LOC {#GUID-C86F4767-FE1B-4254-81AA-7E788F8394B2}

`SRC_ZDLRA_WALLET_LOC` specifies the path of the Zero Data Loss Recovery Appliance wallet on the source database server. 

Property | Description  
---|---  
**Syntax** |  `SRC_ZDLRA_WALLET_LOC = *`source_zdlra_wallet_location`*`<br>The expected format for the location is `/u02/app/oracle/product/12.1.0/dbhome_3/dbs/zdlra`  
**Default value** |  There is no default value.  

Usage Notes

When using Zero Data Loss Recovery Appliance as the migration backup medium, you must set the following parameters.

`SRC_ZDLRA_WALLET_LOC`

`TGT_ZDLRA_WALLET_LOC`

`ZDLRA_CRED_ALIAS`

### TGT_BASTION_HOST_IP {#GUID-5962442C-5C81-49CB-A9FF-49349BAC30DE}

`TGT_BASTION_HOST_IP` specifies the bastion host IP address, if you want to connect to the target database server using a bastion host. 

Property | Description  
---|---  
**Syntax** |  `TGT_BASTION_HOST_IP = *`bastion_ip_address`*`  
**Default value** |  There is no default value.  

Usage Notes

If you want to connect to the target database server using a bastion host, you are required to configure values for the bastion host IP address parameter, `TGT_BASTION_HOST_IP`, and the target database server IP address parameter, `TGT_HOST_IP`, in the Zero Downtime Migration response file. 

If you do not want to use the default values for the remaining bastion connection parameters, set the following parameters to configure the bastion host connection.

`TGT_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`TGT_BASTION_USER` \- The bastion host target user is only required if the user specified for the target `zdmauth` plug-in is different from the user of the target bastion host. The bastion user defaults to the user specified for the target `zdmauth` plug-in if the parameter is not specified. 

`TGT_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the target `zdmauth` plug-in. 

### TGT_BASTION_IDENTITY_FILE {#GUID-26E25F0E-F42A-4015-9834-78B214D52851}

`TGT_BASTION_IDENTITY_FILE` specifies the identity file to access the bastion host as the indicated user. 

Property | Description  
---|---  
**Syntax** |  `TGT_BASTION_IDENTITY_FILE = *`identity_file`*`  
**Default value** |  If not specified, the value defaults to the value specified for the `identity_file` argument of the target `zdmauth` plug-in.   

Usage Notes

If you want to connect to the target database server using a bastion host, you are required to configure values for the bastion host IP address parameter, `TGT_BASTION_HOST_IP`, and the target database server IP address parameter, `TGT_HOST_IP`, in the Zero Downtime Migration response file. 

If you do not want to use the default values for the remaining bastion connection parameters, set the following parameters to configure the bastion host connection.

`TGT_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`TGT_BASTION_USER` \- The bastion host target user is only required if the user specified for the target `zdmauth` plug-in is different from the user of the target bastion host. The bastion user defaults to the user specified for the target `zdmauth` plug-in if the parameter is not specified. 

`TGT_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the target `zdmauth` plug-in. 

### TGT_BASTION_PORT {#GUID-93DF0C89-AB0B-4D84-8A5C-71A8C24BB437}

`TGT_BASTION_PORT` specifies the bastion host port number, if you want to connect to the target database server using a bastion host. 

Property | Description  
---|---  
**Syntax** |  `TGT_BASTION_PORT = *`port_number`*`  
**Default value** |  `22`  

Usage Notes

If you want to connect to the target database server using a bastion host, you are required to configure values for the bastion host IP address parameter, `TGT_BASTION_HOST_IP`, and the target database server IP address parameter, `TGT_HOST_IP`, in the Zero Downtime Migration response file. 

If you do not want to use the default values for the remaining bastion connection parameters, set the following parameters to configure the bastion host connection.

`TGT_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`TGT_BASTION_USER` \- The bastion host target user is only required if the user specified for the target `zdmauth` plug-in is different from the user of the target bastion host. The bastion user defaults to the user specified for the target `zdmauth` plug-in if the parameter is not specified. 

`TGT_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the target `zdmauth` plug-in. 

### TGT_BASTION_USER {#GUID-1DFD95C7-5847-4687-8CA4-641374C4A039}

`TGT_BASTION_USER` specifies the bastion user, if you want to connect to the target database server using a bastion host. 

Property | Description  
---|---  
**Syntax** |  `TGT_BASTION_USER = *`bastion_user`*`  
**Default value** |  The bastion user defaults to the user specified for the target `zdmauth` plug-in if the parameter is not specified.   

Usage Notes

If you want to connect to the target database server using a bastion host, you are required to configure values for the bastion host IP address parameter, `TGT_BASTION_HOST_IP`, and the target database server IP address parameter, `TGT_HOST_IP`, in the Zero Downtime Migration response file. 

If you do not want to use the default values for the remaining bastion connection parameters, set the following parameters to configure the bastion host connection.

`TGT_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`TGT_BASTION_USER` \- The bastion host target user is only required if the user specified for the target `zdmauth` plug-in is different from the user of the target bastion host. The bastion user defaults to the user specified for the target `zdmauth` plug-in if the parameter is not specified. 

`TGT_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the target `zdmauth` plug-in. 

### TGT_CONFIG_LOCATION {#GUID-FAEBF468-344F-4B9F-8AE9-1F1203420CC7}

`TGT_CONFIG_LOCATION` specifies the SSH configuration file location on the Zero Downtime Migration service host (host where Zero Downtime Migration service is running). 

Property | Description  
---|---  
**Syntax** |  `TGT_CONFIG_LOCATION = *`SSH_config_file_path`*`  
**Default value** |  `*`User_home`*/.ssh/config`  

Usage Notes

Set `TGT_CONFIG_LOCATION` to the full path of the SSH configuration file location on the Zero Downtime Migration service host, for example, `/home/crsuser/.ssh/config`. 

### TGT_DATAACFS {#GUID-07D1DBBC-8022-4694-95D9-F0032A07FB1A}

`TGT_DATAACFS` specifies the location for the data files ACFS volume (`data`) on the target database. Use only if required to override the values discovered automatically by Zero Downtime Migration. 

Property | Description  
---|---  
**Syntax** |  `TGT_DATAACFS = *`data_location`*`  
**Default value** |  There is no default value.  

Usage Notes

Zero Downtime Migration discovers the location for ASM and ACFS `data`, `redo`, and `reco` storage volumes from the specified target database, making these target database storage properties optional. 

If you need to override the values automatically discovered by Zero Downtime Migration, then you can set the following parameters. For example, `TGT_DATADG=+DATAC3`

For ASM use these parameters

`TGT_DATADG`

`TGT_REDODG`

`TGT_RECODG`

For ACFS use these parameters

`TGT_DATAACFS`

`TGT_REDOACFS`

`TGT_RECOACFS`

### TGT_DATADG {#GUID-28BFC27C-DEE1-4CEA-9584-3232340521F9}

`TGT_DATADG` specifies the location for the data files ASM disk group (`data`) on the target database. Use only if required to override the values discovered automatically by Zero Downtime Migration. 

Property | Description  
---|---  
**Syntax** |  `TGT_DATADG = *`data_location`*`  
**Default value** |  There is no default value.  

Usage Notes

Zero Downtime Migration discovers the location for ASM and ACFS `data`, `redo`, and `reco` storage volumes from the specified target database, making these target database storage properties optional. 

If you need to override the values automatically discovered by Zero Downtime Migration, then you can set the following parameters. For example, `TGT_DATADG=+DATAC3`

For ASM use these parameters

`TGT_DATADG`

`TGT_REDODG`

`TGT_RECODG`

For ACFS use these parameters

`TGT_DATAACFS`

`TGT_REDOACFS`

`TGT_RECOACFS`

### TGT_DB_UNIQUE_NAME {#GUID-9DC479C3-DA62-411E-9348-731F78FE047A}

`TGT_DB_UNIQUE_NAME` is used by Zero Downtime Migration to identify the target database. 

Property | Description  
---|---  
**Syntax** |  `TGT_DB_UNIQUE_NAME = *`value of target database DB_UNIQUE_NAME`*`  
**Default value** |   

Usage Notes

Set `TGT_DB_UNIQUE_NAME` to the target database `DB_UNIQUE_NAME` value. 

If the target database is Oracle Cloud Infrastructure, Oracle Exadata Database Service on Dedicated Infrastructure, or Oracle Exadata Database Service on Cloud@Customer, the target database `DB_UNIQUE_NAME` parameter value must be unique to ensure that Oracle Data Guard can identify the target as a different database from the source database. 

### TGT_HOST_IP {#GUID-51588119-429B-4BAE-98BC-EF676CEF96B7}

`TGT_HOST_IP` specifies the IP to be used to connect to the target database host from bastion host. This IP would not be reachable from ZDM host. It is expected to be reachable from bastion host. 

Property | Description  
---|---  
**Syntax** |  `TGT_HOST_IP = *`IP_address`*`  
**Default value** |  There is no default value.  

Usage Notes

If you want to connect to the target database server using a bastion host, you are required to configure values for the bastion host IP address parameter, `TGT_BASTION_HOST_IP`, and the target database server IP address parameter, `TGT_HOST_IP`, in the Zero Downtime Migration response file. 

If you do not want to use the default values for the remaining bastion connection parameters, set the following parameters to configure the bastion host connection.

`TGT_BASTION_PORT` \- The port number defaults to 22 if not specified. 

`TGT_BASTION_USER` \- The bastion host target user is only required if the user specified for the target `zdmauth` plug-in is different from the user of the target bastion host. The bastion user defaults to the user specified for the target `zdmauth` plug-in if the parameter is not specified. 

`TGT_BASTION_IDENTITY_FILE` \- If not specified, the value defaults to the value specified for the `identity_file` argument of the target `zdmauth` plug-in. 

### TGT_HTTP_PROXY_PORT {#GUID-98A676BA-75AE-4FAC-B29D-DE49B116033F}

`TGT_HTTP_PROXY_PORT` specifies the HTTPS proxy port if an SSH connection needs to use a proxy to connect to the target database server. 

Property | Description  
---|---  
**Syntax** |  `TGT_HTTP_PROXY_PORT = *`https_proxy_port_number`*`  
**Default value** |  There is no default value.  

Usage Notes

Set both the `TGT_HTTP_PROXY_URL` and `TGT_HTTP_PROXY_PORT` parameters if the SSH connection needs to use an HTTPS proxy to connect to the target database server. 

### TGT_HTTP_PROXY_URL {#GUID-A2E06923-5688-48CC-A747-9478C7EE6509}

`TGT_HTTP_PROXY_URL` specifies the HTTPS proxy URL if an SSH connection needs to use a proxy to connect to the target database server. 

Property | Description  
---|---  
**Syntax** |  `TGT_HTTP_PROXY_URL = *`https_proxy_url`*`  
**Default value** |  There is no default value.  

Usage Notes

Set both the `TGT_HTTP_PROXY_URL` and `TGT_HTTP_PROXY_PORT` parameters if the SSH connection needs to use an HTTPS proxy to connect to the target database server. 

### TGT_OSS_PROXY_HOST {#GUID-D515D816-9592-4AA8-9D20-8342C9C6EF86}

`TGT_OSS_PROXY_HOST` specifies the Object Storage Service proxy host on the target database server if a proxy is needed for connecting to the Object Store. 

Property | Description  
---|---  
**Syntax** |  `TGT_OSS_PROXY_HOST = *`oss_proxy_host`*`  
**Default value** |  There is no default value.  

Usage Notes

Set both the `TGT_OSS_PROXY_HOST` and `TGT_OSS_PROXY_PORT` parameters if a proxy is needed for connecting to the Object Store. 

### TGT_OSS_PROXY_PORT {#GUID-91A2D991-2850-43FA-85BF-5934511F985E}

`TGT_OSS_PROXY_PORT` specifies the Object Storage Service proxy port number on the target database server if a proxy is needed for connecting to the Object Store. 

Property | Description  
---|---  
**Syntax** |  `TGT_OSS_PROXY_PORT = *`oss_proxy_port_number`*`  
**Default value** |  There is no default value.  

Usage Notes

Set both the `TGT_OSS_PROXY_HOST` and `TGT_OSS_PROXY_PORT` parameters if a proxy is needed for connecting to the Object Store. 

### TGT_RECOACFS {#GUID-A0322406-9023-4D61-997E-8CF43FE1AABA}

`TGT_RECOACFS` specifies the location for the fast recovery area ACFS volume (`reco`) on the target database. Use only if required to override the values discovered automatically by Zero Downtime Migration. 

Property | Description  
---|---  
**Syntax** |  `TGT_RECOACFS = *`reco_location`*`  
**Default value** |  There is no default value.  

Usage Notes

Zero Downtime Migration discovers the location for ASM and ACFS `data`, `redo`, and `reco` storage volumes from the specified target database, making these target database storage properties optional. 

If you need to override the values automatically discovered by Zero Downtime Migration, then you can set the following parameters. For example, `TGT_DATADG=+DATAC3`

For ASM use these parameters

`TGT_DATADG`

`TGT_REDODG`

`TGT_RECODG`

For ACFS use these parameters

`TGT_DATAACFS`

`TGT_REDOACFS`

`TGT_RECOACFS`

### TGT_RECODG {#GUID-54723644-1D1F-4604-BC7D-32E3BB1BCEC9}

`TGT_RECODG` specifies the location for the fast recovery area ASM disk group (`reco`) on the target database. Use only if required to override the values discovered automatically by Zero Downtime Migration. 

Property | Description  
---|---  
**Syntax** |  `TGT_RECODG = *`reco_location`*`  
**Default value** |  There is no default value.  

Usage Notes

Zero Downtime Migration discovers the location for ASM and ACFS `data`, `redo`, and `reco` storage volumes from the specified target database, making these target database storage properties optional. 

If you need to override the values automatically discovered by Zero Downtime Migration, then you can set the following parameters. For example, `TGT_DATADG=+DATAC3`

For ASM use these parameters

`TGT_DATADG`

`TGT_REDODG`

`TGT_RECODG`

For ACFS use these parameters

`TGT_DATAACFS`

`TGT_REDOACFS`

`TGT_RECOACFS`

### TGT_REDOACFS {#GUID-64C51AA8-1DCC-4A0C-B3CD-65DE7070E196}

`TGT_REDOACFS` specifies the location for redo log files ACFS volume (`redo`) on the target database. Use only if required to override the values discovered automatically by Zero Downtime Migration. 

Property | Description  
---|---  
**Syntax** |  `TGT_REDOACFS = *`redo_location`*`  
**Default value** |  There is no default value.  

Usage Notes

Zero Downtime Migration discovers the location for ASM and ACFS `data`, `redo`, and `reco` storage volumes from the specified target database, making these target database storage properties optional. 

If you need to override the values automatically discovered by Zero Downtime Migration, then you can set the following parameters. For example, `TGT_DATADG=+DATAC3`

For ASM use these parameters

`TGT_DATADG`

`TGT_REDODG`

`TGT_RECODG`

For ACFS use these parameters

`TGT_DATAACFS`

`TGT_REDOACFS`

`TGT_RECOACFS`

### TGT_REDODG {#GUID-6F106CA3-0D91-4808-8C95-788ED7408AF0}

`TGT_REDODG` specifies the location for redo log files ASM disk group (`redo`) on the target database. Use only if required to override the values discovered automatically by Zero Downtime Migration. 

Property | Description  
---|---  
**Syntax** |  `TGT_REDODG = *`redo_location`*`  
**Default value** |  There is no default value.  

Usage Notes

Zero Downtime Migration discovers the location for ASM and ACFS `data`, `redo`, and `reco` storage volumes from the specified target database, making these target database storage properties optional. 

If you need to override the values automatically discovered by Zero Downtime Migration, then you can set the following parameters. For example, `TGT_DATADG=+DATAC3`

For ASM use these parameters

`TGT_DATADG`

`TGT_REDODG`

`TGT_RECODG`

For ACFS use these parameters

`TGT_DATAACFS`

`TGT_REDOACFS`

`TGT_RECOACFS`

### TGT_RETAIN_DB_UNIQUE_NAME {#GUID-7AE0D9B4-DF3D-4139-883F-D1EB07F7357A}

`TGT_RETAIN_DB_UNIQUE_NAME` specifies whether to ship redo logs from Oracle Cloud to the on-premises standby, observe the environment for some time, and remove the fallback later. 

When `TGT_RETAIN_DB_UNIQUE_NAME=TRUE` then the workflow phase `ZDM_RETAIN_DBUNIQUENAME_TGT` is present as the final phase. You must pause the migration job at a prior phase and resume the job when the fallback has to be removed. 

Property | Description  
---|---  
**Syntax** |  `TGT_RETAIN_DB_UNIQUE_NAME = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- enables this feature <br>`FALSE` \- disables this feature   

### TGT_RMAN_CHANNELS {#GUID-27412866-DFDA-4365-B7AB-9FACD39E02CC}

`TGT_RMAN_CHANNELS` specifies the number of RMAN channels to be allocated at the target database server for performing RMAN restore. 

Property | Description  
---|---  
**Syntax** |  `TGT_RMAN_CHANNELS = *`number_of_channels`*`  
**Default value** |  `10`  

### TGT_SKIP_DATAPATCH {#GUID-5F0DAEFA-9951-4112-8E04-8EFE0EA15A61}

`TGT_SKIP_DATAPATCH` specifies whether or not Zero Downtime Migration runs the datapatch utility on the target database as part of the post-migration tasks. 

Property | Description  
---|---  
**Syntax** |  `TGT_SKIP_DATAPATCH = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- do not allow Zero Downtime Migration to run datapatch <br>`FALSE` \- allow Zero Downtime Migration to run datapatch   

Usage Notes

If the target database environment is at a higher patch level than the source database (for example, if the source database is at Jan 2020 PSU/BP and the target database is at April 2020 PSU/BP), then set the `TGT_SKIP_DATAPATCH` parameter to `FALSE` to allow Zero Downtime Migration to run the datapatch utility on the target database as part of the post-migration tasks. 

Otherwise, set the parameter to `TRUE`, and if the target database environment is at a higher patch level than the source database, you will need to run the datapatch utility manually after the migration. 

### TGT_SSH_RETRY_TIMEOUT {#GUID-311F2629-EDB8-49C6-83E7-53502A6C2849}

`TGT_SSH_RETRY_TIMEOUT` specifies the number of minutes during which retries are attempted after SSH connection failures. Retries stop when the timeout value has elapsed. 

Property | Description  
---|---  
**Syntax** |  `TGT_SSH_RETRY_TIMEOUT = *`number_of_minutes`*`  
**Default value** |  There is no default value.  

### TGT_SSH_TUNNEL_PORT {#GUID-2D731844-BA13-4A9A-9C45-DCEC21752774}

`TGT_SSH_TUNNEL_PORT` specifies the forwarding port on the source database server where the SSH tunnel to the target database server for SQL*Net connection is set up. 

Property | Description  
---|---  
**Syntax** |  `TGT_SSH_TUNNEL_PORT = *`ssh_tunnel_port_number`*`  
**Default value** |  There is no default value.  

### TGT_ZDLRA_WALLET_LOC {#GUID-BD860D43-CB21-4B6B-B4C3-58A6D34AF402}

`TGT_ZDLRA_WALLET_LOC` specifies the path of the Zero Data Loss Recovery Appliance wallet on the target database server. 

Property | Description  
---|---  
**Syntax** |  `TGT_ZDLRA_WALLET_LOC = *`target_zdlra_wallet_location`*`<br>The expected format for the location is `/u02/app/oracle/product/12.1.0/dbhome_3/dbs/zdlra`  
**Default value** |  There is no default value.  

Usage Notes

When using Zero Data Loss Recovery Appliance as the migration backup medium, you must set the following parameters.

`SRC_ZDLRA_WALLET_LOC`

`TGT_ZDLRA_WALLET_LOC`

`ZDLRA_CRED_ALIAS`

### TGT_STBY_NODE {#GUID-BD71B58C-AA9D-4D47-B810-0FDD185F907B}

`TGT_STBY_NODE` specifies the IP address of the target standby. 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  `TGT_STBY_NODE=target standby ip address`  
**Default value** |  There is no default value.  

### TGT_STBY_DB_UNIQUE_NAME {#GUID-23182A17-3059-4264-9AB4-D4CEDF4A7F66}

`TGT_STBY_DB_UNIQUE_NAME` specifies the value of target standby database. 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  `TGT_STBY_DB_UNIQUE_NAME=*`value of target standby database DB_UNIQUE_NAME`*`  
**Default value** |  There is no default value.  

### TGT_STBY_AUTH {#GUID-0B760864-DB66-4727-8CBA-74A4DF9D5827}

``TGT_STBY_AUTH`` specifies the authentication plugin name for connecting to the target standby database. 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  `TGT_STBY_AUTH=*`plugin_name`*`  
**Default value** |  There is no default value.  
**Range of values** |  `zdmauth`  

### TGT_STBY_SUDO_USER {#GUID-4292C0E7-1E0A-4521-B6A4-E81B914E6585}

``TGT_STBY_SUDO_USER`` specifies the user name to login on the target standby database server. 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  `TGT_STBY_SUDO_USER=*`user:target_standby_database_server_login_user_name`*`  
**Default value** |  There is no default value.  

### TGT_STBY_IDENTITY_FILE {#GUID-A2881F91-4CD3-4E22-A750-3F8B16B5A979}

``TGT_STBY_IDENTITY_FILE`` specifies the location of the identity file. 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  `TGT_STBY_IDENTITY_FILE=*`identity_file:ZDM_installed_user_private_key_file_location`*`  
**Default value** |  There is no default value.  

### TGT_STBY_SUDO_PATH {#GUID-909FB9F4-BCC5-48EB-8788-25531C8AE978}

``TGT_STBY_SUDO_PATH`` specifies the sudo location on the target standby database. 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  `TGT_STBY_SUDO_PATH=*`sudo_location:sudo_location`*`  
**Default value** |  There is no default value.  

### TGT_STBY_ZDLRA_WALLET_LOC {#GUID-FB5F8FC2-2604-48AF-A1E9-637D8783B925}

``TGT_STBY_ZDLRA_WALLET_LOC`` specifies the location of the Zero Data Loss Recovery Appliance wallet on the target standby database. 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  The expected format for the location is:<br>`TGT_STBY_ZDLRA_WALLET_LOC=/u02/app/oracle/product/12.1.0/dbhome_3/dbs/zdlra`  
**Default value** |  There is no default value.  

### ZDM_TGT_STBY_CASCADING {#GUID-71897C41-6439-4A10-94BF-F28FBF6C87DE}

Specifies how to keep the target standby database in sync during DR physical online migrations. Set `ZDM_TGT_STBY_CASCADING` to `TRUE` (default value) to allow redo log shipping from the target database or set to `FALSE` to allow the redo log shipping from the source database (primary). 

> **note:** This parameter is valid only for DR Physical Online migrations. 

Property | Description  
---|---  
**Syntax** |  `ZDM_TGT_STBY_CASCADING={TRUE \| FALSE}`  
**Default value** |  `TRUE`  
**Required** |  No, only required for DR physical online migrations.   

### ZDM_UPGRADE_TARGET_HOME {#GUID-EB1E84A0-111A-4FF4-A755-C554E55D8100}

Specify the location of `ZDM_UPGRADE_TARGET_HOME` when you want to upgrade your target database after migration to an upgraded version. 

> **note:** Ensure that the TGT_SKIP_DATAPATCH should be FALSE. 

Ensure that the version of `ORACLE_HOME` is an upgraded version. 

> **note:** This parameter is valid only for DR Physical migrations. 

Property | Description  
---|---  
**Syntax** |  `ZDM_UPGRADE_TARGET_HOME=`  
**Default value** |  `<>`  
**Range of values** |  NA   

### ZDM_PRE_UPGRADE_TARGET_HOME {#GUID-132B49D5-1DCB-4736-9918-744C942E7AF6}

Specify the location of ZDM_PRE_UPGRADE_TARGET_HOME when you want to upgrade and plug your non-cdb source database to an upgraded version of the CDB target database. ZDM_PRE_UPGRADE_TARGET_HOME accepts ORACLE_HOME path in the target instance. The version of ZDM_PRE_UPGRADE_TARGET_HOME home should be same as the non-cdb source database.

> **note:** Value of the` NONCDBTOPDB_CONVERSION` should be `TRUE`. `TGT_SKIP_DATAPATCH` should be `FALSE`. 

Property | Description  
---|---  
**Syntax** |  `ZDM_PRE_UPGRADE_TARGET_HOME=`  
**Default value** |  `<>`  
**Range of values** |  NA   

### ZDLRA_CRED_ALIAS {#GUID-AD182AF8-6A50-4927-87CD-DF7DDA7D3093}

`ZDLRA_CRED_ALIAS` specifies the Zero Data Loss Recovery Appliance wallet credential alias. 

Property | Description  
---|---  
**Syntax** |  `ZDLRA_CRED_ALIAS = *`zdlra_wallet_alias *`<br>The expected format for the alias is `* zdlra scan`*:*`listener port`*/zdlra9:dedicated`  
**Default value** |  There is no default value.  

Usage Notes

When using Zero Data Loss Recovery Appliance as the migration backup medium, you must set the following parameters.

`SRC_ZDLRA_WALLET_LOC`

`TGT_ZDLRA_WALLET_LOC`

`ZDLRA_CRED_ALIAS`

### ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL {#GUID-BE851AC8-D10C-4006-869E-784A5666B5E3}

`ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL` specifies the time interval, in minutes, at which to monitor and report the progress of the `ZDM_BACKUP_DIFFERENTIAL_SRC` migration job phase. 

Property | Description  
---|---  
**Syntax** |  `ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL = *`minutes`*`  
**Default value** |  `10`  

Usage Notes

The migration job phase monitoring interval parameters, listed below, monitor and report the backup and restore operations progress at the set time interval, specified in minutes. Note that the migration job phase for which the monitoring interval applies is prefixed to `_MONITORING_INTERVAL` in each parameter listed above. 

* ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL
* ZDM_CLONE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL



To disable a monitoring interval parameter, set it to `0` (zero). 

Note that for parameters that are interval based, if the results are not in the job logs, check the specific phase logs for the monitoring information.

### ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL {#GUID-269B244C-05FA-49FD-8FB0-A822C1D832FD}

`ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL` specifies the time interval, in minutes, at which to monitor and report the progress of the `ZDM_BACKUP_FULL_SRC` migration job phase. 

Property | Description  
---|---  
**Syntax** |  `ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL = *`minutes`*`  
**Default value** |  `10`  

Usage Notes

The migration job phase monitoring interval parameters, listed below, monitor and report the backup and restore operations progress at the set time interval, specified in minutes. Note that the migration job phase for which the monitoring interval applies is prefixed to `_MONITORING_INTERVAL` in each parameter listed above. 

* ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL
* ZDM_CLONE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL



To disable a monitoring interval parameter, set it to `0` (zero). 

Note that for parameters that are interval based, if the results are not in the job logs, check the specific phase logs for the monitoring information.

### ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL {#GUID-BE9735D6-73E5-4113-9097-F23C808DD8CC}

`ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL` specifies the time interval, in minutes, at which to monitor and report the progress of the `ZDM_BACKUP_INCREMENTAL_SRC` migration job phase. 

Property | Description  
---|---  
**Syntax** |  `ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL = *`minutes`*`  
**Default value** |  `10`  

Usage Notes

The migration job phase monitoring interval parameters, listed below, monitor and report the backup and restore operations progress at the set time interval, specified in minutes. Note that the migration job phase for which the monitoring interval applies is prefixed to `_MONITORING_INTERVAL` in each parameter listed above. 

* ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL
* ZDM_CLONE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL



To disable a monitoring interval parameter, set it to `0` (zero). 

Note that for parameters that are interval based, if the results are not in the job logs, check the specific phase logs for the monitoring information.

### ZDM_BACKUP_RETENTION_WINDOW {#GUID-26585E8B-DB5B-41F5-ABB9-C514ECDDBDA6}

`ZDM_BACKUP_RETENTION_WINDOW` specifies the number of days after which backups created by Zero Downtime Migration become obsolete. 

Property | Description  
---|---  
**Syntax** |  `ZDM_BACKUP_RETENTION_WINDOW = *`days`*`  
**Default value** |  `60`  

### ZDM_BACKUP_TAG {#GUID-7EDF73B8-331D-4BD2-AB25-7348116DBE47}

`ZDM_BACKUP_TAG` specifies an RMAN backup tag that can be used to perform a database migration or create a backup. 

Use cases:

Set `ZDM_USE_EXISTING_BACKUP=TRUE` to use the specified RMAN backup in `ZDM_BACKUP_TAG` as the full backup, to skip the full backup phase in a migration job. An error is thrown if the backup associated with the specified tag is not valid. 

Set `ZDM_USE_EXISTING_BACKUP=FALSE` if you wish to create a backup with the specified tag in `ZDM_BACKUP_TAG`

Property | Description  
---|---  
**Syntax** |  `ZDM_BACKUP_TAG=*`RMAN backup tag`*`  
**Default value** |  There is no default value  
**Range of values** |  Specify a valid RMAN backup tag to create or use an existing backup in the migration.  

### ZDM_CLONE_TGT_MONITORING_INTERVAL {#GUID-79E0D034-4622-4B2D-8C79-04E985CAD1E7}

`ZDM_CLONE_TGT_MONITORING_INTERVAL` specifies the time interval, in minutes, at which to monitor and report the progress of the `ZDM_CLONE_TGT` migration job phase. 

Property | Description  
---|---  
**Syntax** |  `ZDM_CLONE_TGT_MONITORING_INTERVAL = *`minutes`*`  
**Default value** |  `10`  

Usage Notes

The migration job phase monitoring interval parameters, listed below, monitor and report the backup and restore operations progress at the set time interval, specified in minutes. Note that the migration job phase for which the monitoring interval applies is prefixed to `_MONITORING_INTERVAL` in each parameter listed above. 

* ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL
* ZDM_CLONE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL



To disable a monitoring interval parameter, set it to `0` (zero). 

Note that for parameters that are interval based, if the results are not in the job logs, check the specific phase logs for the monitoring information.

### ZDM_CURL_LOCATION {#GUID-B94384A4-7287-4905-B8FE-1E9302D8D01C}

`ZDM_CURL_LOCATION` specifies a custom location for the CURL binary on the source. 

Property | Description  
---|---  
**Syntax** |  `ZDM_CURL_LOCATION = *`curl_location`*`  
**Default value** |  `/usr/bin/curl`  

### ZDM_LOG_OSS_PAR_URL {#GUID-B3E91A10-818B-49A5-930E-B78E5A03FBCC}

`ZDM_LOG_OSS_PAR_URL` specifies the pre-authenticated URL to use when uploading logs to Object Storage Service. The logs capture the current migration job phase and the execution status of the phase. 

Property | Description  
---|---  
**Syntax** |  `ZDM_LOG_OSS_PAR_URL = *`url`*`  
**Default value** |  There is no default value. By default this parameter is disabled.  

### ZDM_OPC_RETRY_COUNT {#GUID-03BDF5E4-D71A-466F-8068-8360BC423F8C}

`ZDM_OPC_RETRY_COUNT` specifies the number of retry attempts tat will be made after an initial Object Store connection failure. 

Property | Description  
---|---  
**Syntax** |  `ZDM_OPC_RETRY_COUNT = *`number`*`  
**Default value** |  `0` (zero) <br>The default behavior is to attempt no retries.  

### ZDM_OPC_RETRY_WAIT_TIME {#GUID-D1D9FBA2-49A9-4A01-9ED0-B74B9EC1BFE9}

`ZDM_OPC_RETRY_WAIT_TIME` specifies the number of seconds to wait after an Object Store connection failure before attempting to retry the connection. 

Property | Description  
---|---  
**Syntax** |  `ZDM_OPC_RETRY_WAIT_TIME = *`seconds`*`  
**Default value** |  `529` (seconds)   

### ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL {#GUID-32752876-FE60-4F64-8B12-97EA011C6735}

`ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL` specifies the time interval, in minutes, at which to monitor and report the progress of the `ZDM_OSS_RECOVER_TGT` migration job phase. 

Property | Description  
---|---  
**Syntax** |  `ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL = *`minutes`*`  
**Default value** |  `10`  

Usage Notes

The migration job phase monitoring interval parameters, listed below, monitor and report the backup and restore operations progress at the set time interval, specified in minutes. Note that the migration job phase for which the monitoring interval applies is prefixed to `_MONITORING_INTERVAL` in each parameter listed above. 

* ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL
* ZDM_CLONE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL



To disable a monitoring interval parameter, set it to `0` (zero). 

Note that for parameters that are interval based, if the results are not in the job logs, check the specific phase logs for the monitoring information.

### ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL {#GUID-AAB15D11-969C-450A-B7E8-BDC5F0B7CE9D}

`ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL` specifies the time interval, in minutes, at which to monitor and report the progress of the `ZDM_OSS_RESTORE_TGT` migration job phase. 

Property | Description  
---|---  
**Syntax** |  `ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL = *`minutes`*`  
**Default value** |  `10`  

Usage Notes

The migration job phase monitoring interval parameters, listed below, monitor and report the backup and restore operations progress at the set time interval, specified in minutes. Note that the migration job phase for which the monitoring interval applies is prefixed to `_MONITORING_INTERVAL` in each parameter listed above. 

* ZDM_BACKUP_FULL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_INCREMENTAL_SRC_MONITORING_INTERVAL
* ZDM_BACKUP_DIFFERENTIAL_SRC_MONITORING_INTERVAL
* ZDM_CLONE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RESTORE_TGT_MONITORING_INTERVAL
* ZDM_OSS_RECOVER_TGT_MONITORING_INTERVAL



To disable a monitoring interval parameter, set it to `0` (zero). 

Note that for parameters that are interval based, if the results are not in the job logs, check the specific phase logs for the monitoring information.

### ZDM_RMAN_COMPRESSION_ALGORITHM {#GUID-DA965F9C-2B1C-420E-BEC3-06841423F768}

`ZDM_RMAN_COMPRESSION_ALGORITHM` specifies which RMAN compression algorithm to use for backups. 

Property | Description  
---|---  
**Syntax** |  `ZDM_RMAN_COMPRESSION_ALGORITHM ={BASIC \| LOW \| MEDIUM \| HIGH \| NONE}`  
**Default value** |  `MEDIUM`  
**Range of values** |  `BASIC` \- Basic Compression Algorithm. <br>`LOW` \- Least impact on backup throughput and suited for environments where CPU resources are the limiting factor. <br>`MEDIUM` \- Recommended for most environments. Good combination of compression ratios and speed <br>`HIGH` \- Best suited for backups over slower networks where the limiting factor is network speed <br>`NONE` \- Allows you to request uncompressed backups. <br>### ZDM_RMAN_DIRECT_METHOD {#GUID-1C24322D-C900-40D9-9C0B-6542AB190437}

`ZDM_RMAN_DIRECT_METHOD` specifies the RMAN method (restore from service or active duplicate) to use when `DATA_TRANSFER_MEDIUM=DIRECT` data transfer method is specified. 

You must also set the `ZDM_SRC_DB_RESTORE_SERVICE_NAME` parameter if you configure a physical migration using direct data transfer (`DATA_TRANSFER_MEDIUM=DIRECT`). 

See [Direct Data Transfer Support](introduction-to-zero-downtime-migration.md#GUID-00701E56-0B91-42F3-AEA6-84A386B53B05) for more information about using direct data transfer in a physical migration 

Property | Description  
---|---  
**Syntax** |  `ZDM_RMAN_DIRECT_METHOD = {RESTORE_FROM_SERVICE \| ACTIVE_DUPLICATE}`  
**Default value** |  `RESTORE_FROM_SERVICE`  
**Range of values** | 

* `RESTORE_FROM_SERVICE` \- Instantiates the standby target using RMAN restore from service. Restore from service is supported in Oracle Database 12.1 and later. <br>`ACTIVE_DUPLICATE` \- Instantiates the standby target using RMAN active duplicate. Active duplicate is supported in Oracle Database 11gR2 and later. 



### ZDM_RMAN_ENCRYPT_BACKUP {#GUID-933E0659-62A3-4693-BBD6-49708576EFCB}

`ZDM_RMAN_ENCRYPT_BACKUP` specifies if you want to disable the backup encryption for `NON_CLOUD`. 

Property | Description  
---|---  
**Syntax** |  `ZDM_RMAN_ENCRYPT_BACKUP ={TRUE \| FALSE}`  
**Default value** |  `TRUE`  
**Range of values** |  `TRUE` \- Enable the encryption. <br>`FALSE` \- Disable the encryption.  > **note:** This parameter is applicable only for `NON_CLOUD` targets as the encryption is mandatory in cloud. You can disable this parameter only for non-cloud migrations. Backup encryption is mandatory for cloud .   

### ZDM_RMAN_SECTION_SIZE {#GUID-FE0DE57E-EACE-479F-9B88-253134522EAF}

`ZDM_RMAN_SECTION_SIZE` specifies the size for backups or for direct migration restore operation. 

Property | Description  
---|---  
**Syntax** |  `ZDM_RMAN_SECTION_SIZE ={*`{K\|M\|G\|T}`*}`<br>Example, `ZDM_RMAN_SECTION_SIZE=4G`  
**Default value** |  `64G` > **note:** Specify `0` to skip using the section size.   
**Modifiable on resume** | Yes, the value will be used for any new backups.  
**Required** |  No  

### ZDM_SHARD_ID {#GUID-D54C37E2-C9D4-40C7-A715-E1878FF30D93}

`ZDM_SHARD_ID` is used to ensure that a migration job is running or is being resumed in the correct intended pod. 

In a scenario where Zero Downtime Migration is scaled out to service multiple simultaneous migrations, each ZDM server has its own metadata store, which means that the same migration job ID values could be repeated across multiple ZDM servers.

To avoid job ID conflicts which could cause the incorrect job to be resumed on the incorrect ZDM server, the `ZDM_SHARD_ID` for each job will be configured contain the ZDM host name to which the migration job is being sent. This value will be seeded by E2E during RSP creation. 

The value of `ZDM_SHARD_ID` and the other RSP tokens will be used by ZDM to confirm that the job metadata matches the RSP file values for the source and target database properties to ensure the correct job ID and ZDM server are resumed. 

If the value from `ZDM_SHARD_ID` in the response file is set, and it does not match the current host name, the following exception is thrown: 

`PRGZ-#### : Specified shard ID *`zdm_host_name_a`* does not match with current ZDM host *`zdm_host_name_b`*.`

in which den01gl is the value read from the response file, and den01glt is the current pod's host name

Property | Description  
---|---  
**Syntax** |  `ZDM_SHARD_ID = *`zdm_host_name`*`  
**Default value** |  No default value<br>By default, ZDM will not consider the host name before starting or resuming a job.   
**Range of values** |  `ZDM_SHARD_ID` accepts the host name of the pod in which the current job will be run or resumed. It accepts host name or fully qualified domain name. <br>The `ZDM_SHARD_ID` verification is case insensitive   

### ZDM_SKIP_DG_CONFIG_CLEANUP {#GUID-DBAF0EB4-CC0B-44CE-8358-45E4BB0E202A}

`ZDM_SKIP_DG_CONFIG_CLEANUP` indicates whether Zero Downtime Migration should clean up the Oracle Data Guard configuration from the source and target databases at the end of the migration when using online physical migration. 

Property | Description  
---|---  
**Syntax** |  `ZDM_SKIP_DG_CONFIG_CLEANUP ={TRUE \| FALSE}`  
**Default value** |  `FALSE`<br>By default, ZDM will deconfigure Data Guard parameters configured for migration.  
**Range of values** |  `TRUE` \- Do not clean up the Oracle Data Guard configuration. <br>`FALSE` \- Clean up the Oracle Data Guard configuration.   

### ZDM_SKIP_TDE_WALLET_MIGRATION {#GUID-FAFFC327-F0DF-420A-9A55-9136F882D716}

`ZDM_SKIP_TDE_WALLET_MIGRATION` indicates whether Zero Downtime Migration should skip export and import of TDE wallet from the source and target database. 

Property | Description  
---|---  
**Syntax** |  `ZDM_SKIP_TDE_WALLET_MIGRATIONP ={TRUE \| FALSE}`  
**Default value** |  `FALSE`<br>By default, ZDM will export TDE wallet from the source and copy and stage the wallet on the target database.  
**Range of values** |  `TRUE` \- Do not export the TDE wallet from the source and copy and stage the wallet on the target database. <br>`FALSE` \- Export TDE wallet from the source and copy and stage the wallet on the target database.   

### ZDM_SRC_DB_RESTORE_SERVICE_NAME {#GUID-4E23C77A-A39A-483C-B887-015C590BD41E}

`ZDM_SRC_DB_RESTORE_SERVICE_NAME` specifies the fully qualified name of the service on the source database to be used for an online physical migration (`MIGRATION_METHOD=ONLINE_PHYSICAL`) using direct data transfer (`DATA_TRANSFER_MEDIUM=DIRECT`). 

You must also set the `ZDM_RMAN_DIRECT_METHOD` parameter if you configure a physical migration using direct data transfer (`DATA_TRANSFER_MEDIUM=DIRECT`). 

See [Direct Data Transfer Support](introduction-to-zero-downtime-migration.md#GUID-00701E56-0B91-42F3-AEA6-84A386B53B05) for more information about using direct data transfer in a physical migration 

Property | Description  
---|---  
**Syntax** |  `ZDM_SRC_DB_RESTORE_SERVICE_NAME = *`source_database_service`*`  
**Default value** |  There is no default value.<br>If not specified, the default database service is used.  

### ZDM_SRC_TNS_ADMIN {#GUID-33110D3A-05FB-4777-984A-BF31E04E9B10}

`ZDM_SRC_TNS_ADMIN` specifies the custom location for `TNS_ADMIN` on the source database server when there is no Oracle Grid Infrastructure. If a Grid Infrastructure exists, then the `TNS_ADMIN` property must be set in the CRS resource attribute environment of the database resource. 

Property | Description  
---|---  
**Syntax** |  `ZDM_SRC_TNS_ADMIN = *`tns_admin_location`*`  
**Default value** |  There is no default value.  

### ZDM_STANDBY_DB_CONNECT_STRING {#GUID-149B0B86-48B0-4CA2-A9B1-F766809C79F5}

Specifies the connect string of the standby database when `ZDM_USE_EXISTING_STANDBY` is enabled. 

Optional parameter to set when `ZDM_USE_EXISTING_STANDBY` is enabled: 

`ZDM_STANDBY_DB_CONNECT_STRING`

See [Using an Existing Standby to Instantiate the Target Database](preparing-for-database-migration.md#GUID-71F345C5-5ED1-4C5D-AD54-76784D4AE221). 

Specify the connect string either in `EZconnect` format or in long form connect descriptor format as shown: 

`EZConnect Format:ZDM_STANDBY_DB_CONNECT_STRING=standby-host.example.com:1521/standbydb`

Syntax (Long service name): `ZDM_STANDBY_DB_CONNECT_STRING=(DESCRIPTION=(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=standby-host.example.com)(PORT=1521)))(CONNECT_DATA=(SERVICE_NAME=standbydb)))`

Property | Description  
---|---  
**Syntax** |  `ZDM_STANDBY_DB_CONNECT_STRING = *`connect_string`*`  
**Default value** |  None  
**Range of values** |  Enter the connect string for an existing standby for the source primary database  

### ZDM_USE_DG_BROKER {#GUID-F9E0DC74-5D5F-45ED-ACCB-7AE1BB16526F}

Indicates whether Zero Downtime Migration can use an Oracle Data Guard broker configuration to manage database role switchover.

See [Using Oracle Data Guard Broker Role Switchover](preparing-for-database-migration.md#GUID-000723DE-532F-44DC-A0B5-2B04890228F5). 

Note that broker configuration is not supported for Oracle Database 11.2.0.4.

Property | Description  
---|---  
**Syntax** |  `ZDM_USE_DG_BROKER ={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Use broker for the migration <br>`FALSE` \- Do not use broker for the migration   

### ZDM_USE_EXISTING_BACKUP {#GUID-E2ABC922-91B6-42D3-A68C-21EB83819B0C}

`ZDM_USE_EXISTING_BACKUP` indicates whether Zero Downtime Migration can use an existing RMAN backup to perform a database migration. 

If this parameter is set to `TRUE`, you must also set parameter `ZDM_BACKUP_TAG`. If no value is provided for `ZDM_BACKUP_TAG`, and `ZDM_USE_EXISTING_BACKUP=TRUE` an error is thrown. 

Property | Description  
---|---  
**Syntax** |  `ZDM_USE_EXISTING_BACKUP ={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Use an existing RMAN backup for the migration <br>`FALSE` \- Do not use an existing RMAN backup for the migration   

### ZDM_USE_EXISTING_STANDBY {#GUID-FAEEF281-7D81-4B20-B35D-80AF27A3B87F}

Indicates whether Zero Downtime Migration can use an existing standby database to instantiate the standby in the target environment in a physical migration.

Optional parameter to set when `ZDM_USE_EXISTING_STANDBY` is enabled: 

`ZDM_STANDBY_DB_CONNECT_STRING`

See [Using an Existing Standby to Instantiate the Target Database](preparing-for-database-migration.md#GUID-71F345C5-5ED1-4C5D-AD54-76784D4AE221). 

> **note:** This migration option is only available when you are using direct data transfer with RMAN restore from service. Because Oracle Database 11.2 doesn't support RMAN restore from service, it is therefore not supported for migration from an existing standby. 

Property | Description  
---|---  
**Syntax** |  `ZDM_USE_EXISTING_STANDBY = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Use an existing standby for the migration <br>`FALSE` \- Do not use a standby for the migration   

### ZDM_USE_EXISTING_UNDO_SIZE {#GUID-9DABFF1F-5E54-49A2-A709-AE8194E2CA1E}

`ZDM_USE_EXISTING_UNDO_SIZE` specifies whether Zero Downtime Migration should use the existing undo tablespace size when creating a new undo tablespace, if required. 

Property | Description  
---|---  
**Syntax** |  `ZDM_USE_EXISTING_UNDO_SIZE ={TRUE \| FALSE}`  
**Default value** |  `TRUE`<br>By default, Zero Downtime Migration uses the largest size of the existing undo tablespaces.  
**Range of values** |  `TRUE` \- Use the existing undo tablespace size. <br>`FALSE` \- Do not use the existing undo tablespace size.   

### ZDM_TGT_UPGRADE_TIMEZONE {#GUID-A407807B-161B-4A45-A871-193154F95410}

`ZDM_TGT_UPGRADE_TIMEZONE` specifies whether Zero Downtime Migration performs timezone upgrade operation on the target database as part of the post-migration tasks. 

Property | Description  
---|---  
**Syntax** |  `ZDM_TGT_UPGRADE_TIMEZONE = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Specify `TRUE` if you want to upgrade target database time zone file version to the latest available time zone file version present in target database. <br>`FALSE` \- Specify `FALSE` if you do not want to upgrade target database time zone file version to the latest available time zone file version present in target database.   

Usage Notes

This property is applicable for physical migration where source database time zone version is less than target time zone version. After completion of physical migration, target database timezone version is downgraded to source time zone version. 

Enable this property to upgrade time zone version of target database to pre-migration time zone version as part of post- migration job performed in this phase. Also upgrade the values of `TIMESTAMP WITH TIME ZONE` data type to the upgraded time zone version, based on the `DBMS_DST` package. 

The following phases are applicable if value of the ZDM_TGT_UPGRADE_TIMEZONE parameter is TRUE:

* TIMEZONE_UPGRADE_PREPARE_TGT
* TIMEZONE_UPGRADE_TGT



**Related Topics**

* [Upgrading the Time Zone File and Timestamp with Time Zone Data](https://docs.oracle.com/en/database/oracle/oracle-database/21/nlspg/datetime-data-types-and-time-zone-support.md)



### ZDM_APPLY_LAG_MONITORING_INTERVAL {#GUID-F7C34BFC-5C42-41F7-AFF6-511533C9A284}

`ZDM_APPLY_LAG_MONITORING_INTERVAL` specifies the lag monitoring interval. 

Property | Description  
---|---  
**Syntax** |  `ZDM_APPLY_LAG_MONITORING_INTERVAL = {NONE \| DAILY \| WEEKLY}`  
**Default value** |  `NONE`  
**Range of values** |  `NONE` \- No interval is specified. <br>`DAILY` -Determines next MIDNIGHT and would be +24hrs for next schedule. <br>`WEEKLY` \- Determines next MIDNIGHT and would be +7days for next schedule.   

### ZDM_NONCDBTOPDB_PDB_NAME {#GUID-BB1C3815-F401-45F5-A2F0-90E098A900BA}

`ZDM_NONCDBTOPDB_PDB_NAME` is used to specify the database name if you want to convert a source database from nonCDB to PDB. 

Property | Description  
---|---  
**Syntax** |  `ZDM_NONCDBTOPDB_PDB_NAME = *``*`  
**Default value** |  `ZDM_AUX_`  
**Range of values** |  When migrating non-CDB source to CDB target as a PDB, the PDB name to be used.<br>If empty, ZDM uses `ZDM_AUX_` by default.   

### SRC_DB_PASSWORDFILE_LOC {#GUID-9DE9D334-CF74-45B8-8FAE-52F7CFB3D916}

`SRC_DB_PASSWORDFILE_LOC` specifies the location of the password file on the source database server. 

If the database user is specified for source database host access and if the database password file is in ASM, then copy it to the `sourcenode` filesystem path for the specified `dbuser` to access the Syntax Local File System path. For example, `/u02/app/oracle/network/admin/orapw`

Property | Description  
---|---  
**Syntax** |  `SRC_DB_PASSWORDFILE_LOC = *`source_db_passwordfile_location`*`  
**Default value** |  There is no default value.  
**Range of values** |  Not applicable  
**Required** | No  
**Modifiable on Resume** | Yes  

### SRC_DB_TDE_WALLET_LOC {#GUID-79FA4FC4-A0E4-4154-9781-1AA041A40FBD}

`SRC_DB_TDE_WALLET_LOC` specifies the path of the TDE wallet on the source database server. 

This parameter allows multiple TDE wallet locations (separated by ||). You can specify multiple TDE wallet locations when using PDBs in isolated mode. For example, `SRC_DB_TDE_WALLET_LOC=/u02/app/oracle/network/admin/tde||/u02/app/oracle/network/admin/pdb1/tde||/u02/app/oracle/network/admin/pdb2/tde`

The allowed characters include:` a-z, A-Z, 0-9, /, +, ., _ `and `-`. 

If `NoSudo DBUSER` auth is specified for source database host access and if the database TDE wallet is in ASM, then copy it to the `sourcenode` filesystem path for the specified `dbuser` to allow access. For example, `/u02/app/oracle/network/admin/tde`. 

> **note:** The TDE wallet paths must be similar to the output of this query: `SQL> SELECT * FROM v$encryption_wallet`. 

Property | Description  
---|---  
**Syntax** |  `SRC_DB_TDE_WALLET_LOC = *`source_db_tde_wallet_location`*`  
**Default value** |  There is no default value.  
**Range of values** |  Not applicable  
**Required** | No  
**Modifiable on Resume** | Yes  

### WALLET_SOURCEADMIN {#GUID-63A82F48-E61E-4A49-B3B2-204B83BC56E7}

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

### WALLET_OSS {#GUID-0289BF78-8634-4FA6-B0E8-AF9C515B647A}

Specifies the absolute path location for wallet to be used for OSS.

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_OSS = *`wallet_path`*`  
**Default value** |  There is no default value  
**Range of value** |  Full path to the wallet.  
**Required** |  No  
**Modifiable on Resume** |  No  

### WALLET_DV {#GUID-2C683471-D968-46F8-87AE-71611874F9F9}

Specifies the absolute path location for wallet to be used for Oracle Database Vault.

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_DV = *`wallet_path`*`  
**Default value** |  There is no default value  
**Range of value** |  Full path to the wallet.  
**Required** |  No  
**Modifiable on Resume** |  No  

### WALLET_TDEKEYSTORE {#GUID-A6CC13E2-8843-4E32-9402-6283BEFE28B6}

Specifies the absolute path location for wallet to be used for TDE keystore.

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

### WALLET_TGTTDEKEYSTORE {#GUID-BC8830EC-F3D3-433B-84BE-227555025DE1}

Specifies the absolute path location for wallet to be used for the target TDE keystore.

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

### WALLET_BACKUP {#GUID-8B883ACC-BE18-446B-A50B-A06838837DB8}

Specifies the absolute path location for wallet to be used for backup.

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_BACKUP = *`wallet_path`*`  
**Default value** |  There is no default value  
**Range of value** |  Full path to the wallet.  
**Required** |  No  
**Modifiable on Resume** |  No
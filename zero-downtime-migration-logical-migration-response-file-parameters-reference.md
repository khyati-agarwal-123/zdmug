## FZero Downtime Migration Logical Migration Response File Parameters Reference {#GUID-D580AD1C-C209-4F0F-A630-863D206FF0E5}

Zero Downtime Migration response file parameters supported for logical migrations.

### DATA_TRANSFER_MEDIUM {#GUID-9C0196CE-6E21-421A-8E7D-D53E021E3D52}

Specifies how data will be transferred from the source database system to the target database system.

See also [Configuring the Transfer Medium and Specifying Transfer Nodes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-3580F7B3-03A5-4ADA-9C2F-FEADF9E125A7)

Property | Description  
---|---  
**Syntax** |  `DATA_TRANSFER_MEDIUM = {OSS \| NFS \| DBLINK \| COPY \| AMAZON3}`  
**Default value** |  `OSS`  
**Range of values** |  `OSS` \- Object Storage Service <br>`NFS` \- Network File System <br>`DBLINK` \- Direct transfer of data over a database link <br>`COPY` \- secure copy (for co-managed and user managed targets) <br>`AMAZON3` \- Amazon Simple Storage Service (Amazon S3) bucket (only supported when `[SOURCEDATABASE_ENVIRONMENT_NAME](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-A8504F26-2CBA-47A2-84AC-94C6A91ECB7A)=AMAZON`  
**Required** |  Yes  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_CREATEAUTHTOKEN {#GUID-61742426-7921-49F7-8FDC-489EC4F586D5}

Indicates whether to create a new OCI Auth Token.

> **note:** This parameter is applicable only when the source database is Oracle Autonomous Database on Shared Exadata Infrastructure or Oracle Autonomous Database on Dedicated Exadata Infrastructure. 

If you are not using a network database link for Data Pump Import, an OCI Auth Token is created for the specified OCI user to import Data Pump dump files from the Object Storage into an Autonomous Database. 

To reuse an existing Auth Token, set this property to `FALSE`. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_CREATEAUTHTOKEN ={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Creates an OCI Auth Token for the specified OCI user <br>`FALSE` \- Does not create an OCI Auth Token.   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATABASELINKDETAILS_NAME {#GUID-5976DC07-E4F3-4793-8B56-E50DAF250600}

Specifies the name of the database link from the OCI database to the on-premise database.

Zero Downtime Migration creates the database link if the link does not already exist.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

The `DATAPUMPSETTINGS_DATABASELINKDETAILS_*` parameters are optional details for creating a network database link from OCI database to the on-premise database. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATABASELINKDETAILS_NAME = *`db_link_name`*`  
**Default value** |  There is no default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATABASELINKDETAILS_WALLETBUCKET_BUCKETNAME {#GUID-92E146E9-4D1C-4524-B1C9-FCD6082AE815}

Specifies the OCI Object Storage bucket.

The `DATAPUMPSETTINGS_DATABASELINKDETAILS_WALLETBUCKET_*` parameters are used with Autonomous Database migration targets. These parameters settings specify the OCI Object Storage details used to store the wallet containing the certificate for on-premise database to create a database link from the Autonomous Database to the on-premise database using TLS. 

Not required for a TCP connection from Autonomous Database to the on-premise database.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATABASELINKDETAILS_WALLETBUCKET_BUCKETNAME = *`Object Storage bucket name`*`  
**Default value** |  There is no default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATABASELINKDETAILS_WALLETBUCKET_NAMESPACENAME {#GUID-D00B2A24-4B75-496D-BB85-34B614CF39B4}

Specifies the Object Storage namespace.

The `DATAPUMPSETTINGS_DATABASELINKDETAILS_WALLETBUCKET_*` parameters are used with Autonomous Database migration targets. These parameters settings specify the OCI Object Storage details used to store the wallet containing the certificate for on-premise database to create a database link from the Autonomous Database to the on-premise database using TLS. 

Not required for a TCP connection from Autonomous Database to the on-premise database.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATABASELINKDETAILS_WALLETBUCKET_NAMESPACENAME = *`Object Storage bucket namespace`*`  
**Default value** |  There is no default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATABUCKET_BUCKETNAME {#GUID-A9C42CC1-B206-499A-B5A6-36D33D0BBA3E}

In lieu of a network database link, the OCI Object Storage bucket specified in `DATAPUMPSETTINGS_DATABUCKET_BUCKETNAME` is used to store Data Pump dump files for migrating to an Autonomous Database. 

`DATAPUMPSETTINGS_DATABUCKET_BUCKETNAME` is one of the optional settings for logical migrations using Data Pump. 

Use with `DATAPUMPSETTINGS_DATABUCKET_NAMESPACENAME`

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATABUCKET_BUCKETNAME = *`Name of the Object Storage bucket`*`  
**Default value** |  There is no default value  
**Range of values** |  Enter the storage bucket name  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATABUCKET_NAMESPACENAME {#GUID-95F00CD4-E6E9-4C2B-8E07-D553F314F2D9}

In lieu of a network database link, the OCI Object Storage bucket specified with `DATAPUMPSETTINGS_DATABUCKET_NAMESPACENAME` is used to store Data Pump dump files for migrating to an Autonomous Database. 

`DATAPUMPSETTINGS_DATABUCKET_NAMESPACENAME` is one of the optional settings for logical migrations using Data Pump. 

Use with `DATAPUMPSETTINGS_DATABUCKET_BUCKETNAME`

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATABUCKET_NAMESPACENAME = *`Object Storage namespace`*`  
**Default value** |  There is no default value  
**Range of values** |  Enter the storage bucket namespace  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_DATAPUMP_SETTINGS_SRC` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ESTIMATEBYSTATISTICS {#GUID-71A1EFA0-BB9B-4962-A785-7B3EBE0FE99E}

Specifies the STATISTICS method for Data Pump dump size estimation.

Zero Downtime Migration estimates the Data Pump dump size using the BLOCKS or STATISTICS methods. You are expected to apply discretion in arriving at the FILESYSTEM space required for the dump path, considering other factors that affect the actual dump size expected. The estimated dump size and actual size varies based on the data compression applied on the data in the database.

* If data is compressed, then it would be the same as that of the reported ESTIMATE
* If data is uncompressed, then Zero Downtime Migration exports with COMPRESSION set as MEDIUM and the resulting dump would be ~50% of estimate
* There is deviation in dump size expected between data having HCC vs. Advanced compression modes 



Note that by default, Zero Downtime Migration performs estimation using the BLOCKS method as part of the precheck and as part of the actual migration part of phase `ZDM_DATAPUMP_ESTIMATE_SRC`. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ESTIMATEBYSTATISTICS = [TRUE | FALSE]`  
**Default value** |  `FALSE`  
**Valid values** |  `TRUE` enables this parameter <br>`FALSE` disables this parameter   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_ESTIMATE_SRC` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_TABLEEXISTSACTION {#GUID-EF826AD1-65A7-4A63-9C2E-0A557763A427}

Specifies the action to be performed when data is loaded into a preexisting table.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_TABLEEXISTSACTION = [SKIP | TRUNCATE | REPLACE | APPEND]`  
**Default value** |  `SKIP`  
**Range of values** |  `SKIP` \- the preexisting table is left unchanged. <br>`TRUNCATE` \- rows are removed from a preexisting table before inserting rows from the Import. Note that if `TRUNCATE` is specified on tables referenced by foreign key constraints, the `TRUNCATE` will be modified into a `REPLACE`. <br>`REPLACE` \- preexisting tables are replaced with new definitions. Before creating the new table, the old table is dropped. <br>`APPEND` \- new rows are added to the existing rows in the table <br>See `TABLE_EXISTS_ACTION` entry in Table 48-25 "Valid Options for the name Parameter in the SET_PARAMETER Procedure" in [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D)  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_USERMETADATA {#GUID-F23EC9CC-4FE6-434E-A145-513BB253C69B}

For EXPORT and Network IMPORT, if set to a nonzero value for schema-mode operations, specifies that the metadata to re-create the user's schemas should also be part of the operation.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETER_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_USERMETADATA = *`integer`*`  
**Default value** |  There is no default value  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_IMPORTPARALLELISMDEGREE {#GUID-3558D915-2C04-40BA-83C3-E12539F2D8E5}

Specifies the maximum number of worker processes that can be used for a Data Pump Import job.

For migration to an Autonomous Database target, Zero Downtime Migration automatically queries its CPU core count and sets this parameter. 

> **note:** While the value is calculated automatically, if you have already set this parameter, then the value is not calculated and ZDM will consider the value that you set. 

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

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXPORTPARALLELISMDEGREE {#GUID-91741406-B08B-4B64-8FE1-6AD20A5B8772}

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

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ENCRYPTION {#GUID-4D96D5ED-24F5-46EE-8F41-10FE36427347}

Specifies what should be encrypted in the dump file set. 

Parameter Relationships

> **note:** Oracle Database Standard Edition 2 does not have dump encryption support and set DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ENCRYPTION=NONE for migration to proceed with data being exported using Data Pump without encryption. 

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  ` DATAPUMPSETTINGS_DATAPUMPPARAMETERS_ENCRYPTION = *``*`  
**Default value** |  `ALL`  
**Valid values** |  Following are the supported values: <br>* `DATA_ONLY`<br>`METADATA_ONLY`<br>`ENCRYPTED_COLUMNS_ONLY`<br>`ALL`<br>`NONE`
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXCLUDETYPELIST {#GUID-7539C488-5707-433A-B825-EE98CA5E811B}

Specifies a comma separated list of object types to exclude.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXCLUDETYPELIST = *`object_type_list`*`  
**Default value** |  There is no default  
**Valid values** |  A comma separated list of object types  
**Required** |  No  
**Modifiable on Resume** |  No  

Example
```
DATAPUMPSETTINGS_DATAPUMPPARAMETERS_EXCLUDETYPELIST=cluster,dblink,comment
```


### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_SKIPCURRENT {#GUID-4646386A-1306-4AD0-A358-7ACE979C2856}

Specifies whether actions that were "in progress" on a previous execution of the job are skipped when the job restarts.

This mechanism allows you to skip actions that trigger fatal bugs and cause the premature termination of a job. Multiple actions can be skipped on a restart. The log file identifies which actions are skipped.

The skip is only honored for Import jobs.

If a domain index was being processed, all pieces of the domain index are skipped, even if the error only occurred in a sub-component of the domain index.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_SKIPCURRENT = [TRUE | FALSE]`  
**Default value** |  `FALSE`  
**Valid values** |  `TRUE` enables this parameter <br>`FALSE` disables this parameter   
**Required** |  No  
**Modifiable in Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_NOCLUSTER {#GUID-68E385C1-C108-486B-ADC1-DA88ACA3532D}

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

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_RETAININDEX {#GUID-5D3AA59A-7AAD-4E7F-8EC1-8570DFD970A7}

Specifies whether to retain the index. 

Parameter Relationships

While migrating to the Oracle Autonomous Database on Shared Exadata Infrastructure target database, the INDEXTYPE objects are excluded by default. Set the DATAPUMPSETTINGS_DATAPUMPPARAMETERS_RETAININDEX parameter to retain the INDEXTYPE objects from source to target Oracle Autonomous Database on Shared Exadata Infrastructure database. 

> **note:** This is not applicable for the other supported target databases as INDEXTYPE objects are always migrated. 

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_*` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_RETAININDEX = [TRUE | FALSE]`  
**Default value** |  `FALSE`  
**Valid values** |  `TRUE` retains the index <br>`FALSE` does not retain the index   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`.   

### DATAPUMPSETTINGS_DATAPUMPPARAMETERS_PARTITIONOPTION {#GUID-B9559EEC-4A3C-46DA-9385-C6CF95261990}

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS_PARTITIONOPTION` specifies how partitioned tables should be handled during an import operation when the target database type is Oracle Autonomous Data Warehouse. 

`DATAPUMPSETTINGS_DATAPUMPPARAMETERS` are optional parameters for Data Pump Export and Import. See [SET_PARAMETER Procedures](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-62324358-2F26-4A94-B69F-1075D53FA96D) for more information. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DATAPUMPPARAMETERS_PARTITIONOPTION = [DEPARTITION | MERGE | NONE]`  
**Default value** |  `MERGE` if the target is ADW-S <br>Otherwise, default value is `NONE`  
**Valid values** |  `DEPARTITION`, `MERGE`, `NONE`  

### DATAPUMPSETTINGS_DELETEDUMPSINOSS {#GUID-8BFD1E37-A046-4D3E-BBFA-E34DCBBD87F0}

Indicates whether to retain dump files which are uploaded to Object Storage as part of the migration.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_DELETEDUMPSINOSS = {TRUE\|FALSE}`  
**Default value** |  `TRUE`  
**Range of values** |  `TRUE` = delete dumps <br>`FALSE` = retain dumps   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_POST_DATAPUMP_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_NAME {#GUID-4CB8C566-2DDC-4EE9-B23F-079B658979BA}

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

### DATAPUMPSETTINGS_EXPORTDIRECTORYOBJECT_PATH {#GUID-1B68BA6F-6254-4AF5-BDDD-369973CDE252}

Specifies the object path of a directory on server file system of an on-premises database.

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

### DATAPUMPSETTINGS_EXPORTVERSION {#GUID-FF32FE8D-99A1-4535-ABD2-FDFBDB0934A4}

Specifies the Data Pump job export version.

See Oracle Data Pump documentation for Data Pump Export Version.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_EXPORTVERSION = ["COMPATIBLE","12","LATEST"]`  
**Default value** |  `COMPATIBLE`, if the target database is of same or higher version than that of source database, and specific version, if the target database is a lower version than that of source database.   
**Required** |  No  

### DATAPUMPSETTINGS_FIXINVALIDOBJECTS {#GUID-60A71EC6-33C0-44CB-AF98-3B542A39D0B2}

Specifies whether invalid objects are recompiled in the database as part of the migration job.

Migration can result in invalid schema objects. Typically, invalid objects fix themselves as they are accessed or run. However, Oracle recommends that invalid objects are recompiled in the database as part of the Zero Downtime Migration migration job so that issues with invalid objects, and any required dependencies, are resolved before users encounter these invalid objects.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_FIXINVALIDOBJECTS = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` = fix invalid objects <br>`FALSE` = do not fix invalid objects   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_POST_DATAPUMP_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_PATH {#GUID-ED883B34-ABAD-479C-AC44-9E0392513547}

Specifies the path of an import directory object.

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

### DATAPUMPSETTINGS_IMPORTDIRECTORYOBJECT_NAME {#GUID-3134C0CB-EE4B-4DBC-BB69-02D1F043FF31}

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

### DATAPUMPSETTINGS_JOBMODE {#GUID-F44F9CBE-5719-4C87-B58F-04EC8D70C819}

Specifies the Data Pump export mode.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Usage

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_JOBMODE = *`jobModeValue`*`  
**Default value** |  `SCHEMA`  
**Range of values** |  `FULL` performs a full database export. <br>`SCHEMA` (default) lets you specify a set of schemas to export. <br>`TABLE` lets you specify a set of tables to export. In this mode, Zero Downtime Migration precreates the target schema before Data Pump import. <br>`TABLESPACE` lets you specify a set of tablespaces to export. In this mode, Zero Downtime Migration precreates the target schema before Data Pump import. <br>`TRANSPORTABLE` is not supported by Zero Downtime Migration. <br>See [Oracle Data Pump Export Modes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=SUTIL-GUID-8E497131-6B9B-4CC8-AA50-35F480CAC2C4) for more information.   
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_METADATAFILTERS-LIST_ELEMENT_NUMBER {#GUID-DF9119C6-D922-44D2-9CD9-9683E55D9C3F}

Defines the name, the object type, and the value of the filter for the Data Pump `METADATA_FILTER` property. 

To add multiple filters, increment the integer appended to the parameter name, as shown in the examples below.
```
DATAPUMPSETTINGS_METADATAFILTERS-1=name:nameValue1st, objectType:objectTypeValue1st, value:valueValue1st
DATAPUMPSETTINGS_METADATAFILTERS-2=name:nameValue2nd, objectType:objectTypeValue2nd, value:valueValue2nd
```


To exclude select `SCHEMA` Objects for `FULL` mode: 
```
DATAPUMPSETTINGS_METADATAFILTERS-1=name:NAME_EXPR,value:'NOT IN(**' 'SYSMAN' '**)',objectType:SCHEMA
DATAPUMPSETTINGS_METADATAFILTERS-2=name:NAME_EXPR,value:'NOT IN(' 'SH' ')',objectType:SCHEMA
```


Note that the `SCHEMA` name `SYSMAN` is surrounded by two single quotes and not a double quote. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_METADATAFILTERS-*`LIST_ELEMENT_NUMBER`* = name:*`nameValue`*, objectType:*`objectTypeValue`*, value:*`valueValue`*`  
**Default value** |  There is no default value  
**Range of values** |  An entry specifying the name, type, and value is expected, as shown in the examples above.  
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_METADATAREMAPS-LIST_ELEMENT_NUMBER {#GUID-1DF8799E-495D-44EB-8C73-90012A1235B1}

Defines remapping to be applied to objects as they are processed.

To add multiple remappings, increment the integer appended to the parameter name, as shown in the examples below.
```
DATAPUMPSETTINGS_METADATAREMAPS-1=type:typeValue1st, oldValue:oldValueValue1st, newValue:newValueValue1st
DATAPUMPSETTINGS_METADATAREMAPS-2=type:typeValue2nd, oldValue:oldValueValue2nd, newValue:newValueValue2nd
```


See [METADATA_REMAP Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ARPLS-GUID-0FC32790-91E6-4781-87A3-229DE024CB3D) for more information. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_METADATAREMAPS-*`LIST_ELEMENT_NUMBER`* = type:*`typeValue`*, oldValue:*`oldValueValue`*, newValue:*`newValueValue`*`<br>See [Remaps Provided by the METADATA_REMAP Procedure](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DATAPUMP.md#GUID-0FC32790-91E6-4781-87A3-229DE024CB3D) for more information about the values that can be specified for `type`.   
**Default value** |  There is no default value  
**Range of values** |  An entry specifying the new value, old value and type is expected, as shown in the examples above.  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`  

### DATAPUMPSETTINGS_METADATATRANSFORMS-LIST_ELEMENT_NUMBER {#GUID-D5945FFC-7DF6-4D22-971B-A08B6329DE34}

Defines the name, the object type, and the value for the Data Pump `METADATA_TRANSFORM` property. 

To add multiple filters, increment the integer appended to the parameter name, as shown in the examples below.
```
DATAPUMPSETTINGS_METADATATRANSFORMS-1=name:nameValue1st, objectType:objectTypeValue1st, value:valueValue1st
DATAPUMPSETTINGS_METADATATRANSFORMS-2=name:nameValue2nd, objectType:objectTypeValue2nd, value:valueValue2nd
```


See [Transforms Provided by the METADATA_TRANFORM Procedure](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DATAPUMP.md#GUID-DE64EDF1-5AD1-44B6-8B3A-8A3954096F71) for more information. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_METADATATRANSFORMS-*`LIST_ELEMENT_NUMBER`* = name:*`nameValue`*, objectType:*`objectTypeValue`*, value:*`valueValue`*` > **note:** See [Transforms Provided by the METADATA_TRANFORM Procedure](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DATAPUMP.md#GUID-DE64EDF1-5AD1-44B6-8B3A-8A3954096F71) for more information about the values that can be specified for `name`.   
**Default value** |  There is no default value  
**Range of values** |  An entry specifying the name, type, and value is expected, as shown in the examples above.  
**Required** |  No  
**Modifiable on Resume** |  No  

Usage Notes

You can set `XMLTYPE_STORAGE_CLAUSE` to `'BINARY XML'`.` DATAPUMPSETTINGS_METADATATRANSFORMS-1=name:XMLTYPE_STORAGE_CLAUSE, value:'BINARY XML'` . With this enhancement all XML types can be converted as part of the migration. 

In another example, you can set `DATAPUMPSETTINGS_METADATATRANSFORMS-2=name:TABLE_COMPRESSION_CLAUSE, value: 'NONE'`. 

### DATAPUMPSETTINGS_METADATAFIRST {#GUID-FF276C20-4BD5-4613-8577-844D1283B0EC}

Specifies that Zero Downtime Migration will import METADATA and DATA as separate phases in the migration job work flow. Setting `DATAPUMPSETTINGS_METADATAFIRST` to `TRUE` will import the METADATA before DATA. 

Migrating METADATA and DATA as separate phase in the migration job workflow provides these benefits:

* Allows you to quickly identify the migration issues that arise due to lockdown profiles in ADB. 

* Allows quick completion of export/import operations of large databases and identify and rectify DDL import errors 

* Allows you to intervene in the migration workflow with target custom actions that require the database objects to exist, namely USER Schema or TABLE or other associated OBJECT definitions 

* Helps you identify potential import failure points in DDL processing 




Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Usage

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_METADATAFIRST = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` = import METADATA before DATA as a separate migration job workflow phase <br>`FALSE` = do not import METADATA and DATA in separate phases   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_SRC`  

### DATAPUMPSETTINGS_MONITORINTERVALMINUTES {#GUID-050FAA02-6503-487D-B9E2-46BD9A08EC99}

Specifies the Data Pump monitor interval in minutes. This setting is optional.

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_MONITORINTERVALMINUTES = *`number`*`  
**Default value** |  10  
**Range of values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_OMITENCRYPTIONCLAUSE {#GUID-09FC7E24-A34D-4721-AD11-EB9762097709}

Sets `TRANSFORM=OMIT_ENCRYPTION_CLAUSE`, which directs Data Pump to suppress any encryption clauses associated with objects using encrypted columns. 

This parameter is valid for targets on Oracle Database 19c and later releases.

`OMIT_ENCRYPTION_CLAUSE` applies to materialized view, table, and tablespace objects, and enables objects which were using encrypted columns in the source to get created in a target database environment where encryption attributes are not supported. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_OMITENCRYPTIONCLAUSE = [TRUE | FALSE]`  
**Default value** |  `TRUE`  
**Valid values** |  `TRUE` enables this parameter <br>`FALSE` disables this parameter   
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_RETAINDUMPS {#GUID-AF42C07A-1C02-4043-8C8C-713FF36F43ED}

Specifies if you want to clean the dumps in OSS or FSS. 

If this parameter is set to `TRUE`, then export dumps are not cleaned as part of the post actions in the selected data transfer medium; OSS, NFS, or FSS. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_RETAINDUMPS = [TRUE | FALSE]`  
**Default value** |  `FALSE`<br>For regular export/import job, the dumps are cleaned up in OSS or FSS.  
**Valid values** |  `TRUE` enables this parameter <br>`FALSE` disables this parameter   
**Required** |  No  
**Modifiable on Resume** |  Until ` ZDM_VALIDATE_DATAPUMP_SETTINGS_TGT` phase is `COMPLETED`.   

### DATAPUMPSETTINGS_REUSE_DUMPPREFIX {#GUID-20305C7C-62D1-4799-9900-227C47E03F87}

(Optional) Specifies the dump prefix of the existing dump files from a previous job.

ZDM can reuse the existing DataPump dump files from the previous ZDM jobs. This is optional and can be done by specifying in this parameter the dump file prefix as `ZDM__DP_EXPORT_<#>_dmp`. ZDM auto detects the dump files in the indicated import directory object path and includes them for import. In this case, ZDM does not include the estimate, export, and transfer phase. If the specified dumps are not present, an error is thrown. 

> **note:** To retain the dumps in the ZDM job, set `DATAPUMPSETTINGS_RETAINDUMPS` to `TRUE`. 

Specify the dump's prefix pattern for preexisting dumps that you want to re-import. Provide the prefix as `ZDM__DP_EXPORT_<#>_dmp`. 

> **note:** Setting `REUSE` and `DATAPUMPSETTINGS_METADATAFIRST` to `TRUE` is not currently supported in the Oracle Zero Downtime Migration 21c (21.5) release. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_REUSE_DUMPPREFIX = ZDM__DP_EXPORT_<#>_dmp`  
**Default value** |  None  
**Valid values** |  NA  
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_SECUREFILELOB {#GUID-E67CFE8E-2AC7-4C73-91A8-0277B6CD2384}

Sets `TRANSFORM=LOB_STORAGE:SECUREFILE`, which directs Data Pump to transform basic LOBs into securefile LOBs during the Data Pump import. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_SECUREFILELOB = [TRUE | FALSE]`  
**Default value** |  `TRUE`  
**Valid values** |  `TRUE` enables this parameter <br>`FALSE` disables this parameter   
**Required** |  No  
**Modifiable on Resume** |  No  

### DATAPUMPSETTINGS_SCHEMABATCHCOUNT {#GUID-0B319434-C8AD-40AC-BC5F-CA6EDFFE0FEA}

Specifies the number of schemas to be processed in parallel as batches.

When `DATAPUMPSETTINGS_JOBMODE=SCHEMA` you can specify the number of schemas to be migrated in parallel. If this count is specified, then Zero Downtime Migration determines the set of schemas included in each batch from the list of user schemas identified from the database. 

`DATAPUMPSETTINGS_SCHEMABATCHCOUNT` is mutually exclusive with the `DATAPUMPSETTINGS_SCHEMABATCH` parameter. 

> **note:** Database initialization parameter `MAX_DATAPUMP_JOBS_PER_PDB` determines the maximum number of concurrent Oracle Data Pump jobs for each PDB. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_SCHEMABATCHCOUNT = *`integer`*`  
**Default value** |  There is no default value  
**Range of values** |  An integer equal to or less than database initialization parameter `MAX_DATAPUMP_JOBS_PER_PDB`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_SRC` phase is `COMPLETED`  

### DATAPUMPSETTINGS_SCHEMABATCH-LIST_ELEMENT_NUMBER {#GUID-8CE757B3-E3EE-4158-B71A-3A4BCBCC9272}

Specifies which schemas are batched together to be migrated in parallel.

When `DATAPUMPSETTINGS_JOBMODE=SCHEMA` you can specify which schemas are migrated together in parallel as a batch. Increment the -*`LIST_ELEMENT_NUMBER`* value to differentiate the batches. 

In this example there are two batches, each containing two schemas that are migrated in parallel.

`DATAPUMPSETTINGS_SCHEMABATCH-1=n1,n2`

`DATAPUMPSETTINGS_SCHEMABATCH-2=n3,n4`

`DATAPUMPSETTINGS_SCHEMABATCH` is mutually exclusive with the `DATAPUMPSETTINGS_SCHEMABATCHCOUNT` parameter. 

> **note:** Database initialization parameter `MAX_DATAPUMP_JOBS_PER_PDB` determines the maximum number of concurrent Oracle Data Pump jobs for each PDB. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_SCHEMABATCH-*`LIST_ELEMENT_NUMBER`* = `list of schemas``  
**Default value** |  There is no default value.  
**Range of values** |  *`LIST_ELEMENT_NUMBER`* must be a unique integer value for each batch specified <br>`list of schemas` must be a comma-separated list of schemas   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_SRC` phase is `COMPLETED`  

### DATAPUMPSETTINGS_SKIPDEFAULTTRANSFORM {#GUID-8CBA61B0-DA06-4E4C-BBE8-01AFD7E41765}

Skips default transform parameters.

Set this property to `TRUE` to avoid all internal Zero Downtime Migration transform defaults. 

Parameter Relationships

The optional `DATAPUMPSETTINGS_*` parameters let you customize Oracle Data Pump Export and Import jobs. 

Property | Description  
---|---  
**Syntax** |  `DATAPUMPSETTINGS_SKIPDEFAULTTRANSFORM = [TRUE | FALSE]`  
**Default value** |  `FALSE`  
**Valid values** |  `TRUE` enables this parameter <br>`FALSE` disables this parameter   
**Required** |  No  
**Modifiable on Resume** |  No  

### DUMPTRANSFERDETAILS_PUBLICREAD {#GUID-53C3274C-163F-4208-B862-C8D80281D2C5}

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

### DUMPTRANSFERDETAILS_PARALLELCOUNT {#GUID-EF6D2FAC-D321-4B42-830F-8AC811DBCDAF}

Specifies the maximum number of Export dump files to transfer to Object Storage or remote node in parallel.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_PARALLELCOUNT = *`integer`*`  
**Default value** |  3  
**Range of values** |  This parameter accepts integer values  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_RETRYCOUNT {#GUID-03F40E39-8FCE-4E9D-9402-360D39135ECF}

Specifies the number of times to retry upload or transfer of dump failure.

Intermittent network failures observed during the transfer of Data Pump dumps can mitigated by setting the `DUMPTRANSFERDETAILS_RETRYCOUNT` parameter. 

Parameter Relationships

The `DUMPTRANSFERDETAILS` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_RETRYCOUNT = *`integer`*`  
**Default value** |  3  
**Range of values** |  This parameter accepts integer values  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_RSYNCAVAILABLE {#GUID-BCC3594B-1173-4CBA-97F3-4D1084CB1746}

Specifies that the transfer dump files are using the Linux rsync utility.

Ensure rsync is installed in both source and target servers. Oracle Exadata does not ship with rsync, refer to MOS Doc ID 1556257.1 for details. ZDM defaults to scp command for transfer.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_RSYNCAVAILABLE = {TRUE \| FALSE`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- transfer dump files are using the Linux rsync utility <br>`FALSE` \- not using rsync   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_S3BUCKET_ACCESSKEY {#GUID-90C92A88-41DE-4027-9F7B-CD307CBA5243}

Specifies the access key of the Amazon Simple Storage Service (Amazon S3) bucket.

When you set `DATA_TRANSFER_MEDIUM=AMAZON3` to migrate an Amazon Web Services RDS Oracle database to Oracle Autonomous Database using an S3 bucket, you must also set the `DUMPTRANSFERDETAILS_S3BUCKET_*` parameters. 

See [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA) for details. 

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Property | Description  
---|---  
**Syntax** |  ` DUMPTRANSFERDETAILS_S3BUCKET_ACCESSKEY = *`s3_bucket_access_key`* `  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_S3BUCKET_NAME {#GUID-473E6FD8-7A8D-4990-8D22-EC7E9920C068}

Specifies the name of the Amazon Simple Storage Service (Amazon S3) bucket.

When you set `DATA_TRANSFER_MEDIUM=AMAZON3` to migrate an Amazon Web Services RDS Oracle database to Oracle Autonomous Database using an S3 bucket, you must also set the `DUMPTRANSFERDETAILS_S3BUCKET_*` parameters. 

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_S3BUCKET_NAME = *`s3_bucket_name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_S3BUCKET_REGION {#GUID-21D528D4-2B00-4612-9905-8455E8F7EAB3}

Specifies the region of the Amazon Simple Storage Service (Amazon S3) bucket.

When you set `DATA_TRANSFER_MEDIUM=AMAZON3` to migrate an Amazon Web Services RDS Oracle database to Oracle Autonomous Database using an S3 bucket, you must also set the `DUMPTRANSFERDETAILS_S3BUCKET_*` parameters. 

See [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA) for details. 

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_S3BUCKET_REGION = *`s3_bucket_region`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SHAREDSTORAGE_NAME {#GUID-8638D6E8-E5F2-4584-9B3A-4F339E2808F9}

Specifies the name of the exported filesystem to be specified for mount in Autonomous Database.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SHAREDSTORAGE_NAME`<br>` = *`name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is COMPLETED   

### DUMPTRANSFERDETAILS_SHAREDSTORAGE_HOST {#GUID-4BF0256E-6BD8-4E18-9702-46FEA40A4915}

Specifies the hostname to access shared path in the Autonomous Database.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SHAREDSTORAGE_HOST`<br>` = *`name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is COMPLETED   

### DUMPTRANSFERDETAILS_SHAREDSTORAGE_PATH {#GUID-50647E02-4DE2-4A5D-9F09-B020DB19842B}

Specifies the exported path to be mounted in the Autonomous Database.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SHAREDSTORAGE_PATH`<br>` = *`name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is COMPLETED   

### DUMPTRANSFERDETAILS_SOURCE_OCIHOME {#GUID-2386CF2B-CF51-4788-BDD5-465F8D63949C}

Specifies the Oracle Cloud OCI-CLI Binary path.

Parameter Relationships

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_OCIHOME = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SOURCE_PARTSIZEMB {#GUID-E1260E58-9D1F-42E8-8FE1-ED0A8BBE23A9}

Specifies the part size in MB for chunked transfer.

Parameter Relationships

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_PARTSIZEMB = *`integer`*`  
**Default value** |  `128`  
**Range of values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SOURCE_NOSSHUPLOADMETHOD {#GUID-F25EBCFF-5E6F-4102-B8BA-B846F504655F}

Specifies the method to use no-ssh upload.

Parameter Relationships

When value of this parameter is `HTTPS`, then `ociWalletLoc` is required to be a valid path. 

For the default method, value of this parameter is `NONE`. Data is uploaded to OSS securely by using Curl or OCI CLI. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_NOSSHUPLOADMETHOD = `  
**Default value** |  `NONE`  
**Required** |  No  

### DUMPTRANSFERDETAILS_SOURCE_OCIWALLETLOC {#GUID-83F78CA2-8251-40BB-A26E-73FFFA73CD39}

Specifies the directory path of the OCI SSL wallet location on the source database server node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_SOURCE_OCIWALLETLOC` parameter specifies the OCI wallet location in the respective database servers. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_OCIWALLETLOC = < Directory path for OCI SSL wallet location on the source database server node.>`  
**Default value** |  `None`  
**Required** |  No  

### DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_DUMPDIRPATH {#GUID-35B6A45C-D3A5-4DE9-BC3C-3C91E4B894B4}

Specifies the absolute path of the directory to copy or upload dump files.

The `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using Oracle Cloud OCI-CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_DUMPDIRPATH = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_HOST {#GUID-3CED78CE-F502-47C8-8419-21D741D46E97}

Specifies the dump transfer node host name.

The `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using Oracle Cloud OCI-CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_HOST = *`host_name`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_SUDOPATH {#GUID-6F49C305-2EF5-4256-BF9D-27412722637A}

Specifies the Sudo path on the dump transfer node.

The `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using Oracle Cloud OCI-CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_SUDOPATH = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_USER {#GUID-071468D9-2BFB-4B7C-A392-9B75096AF7D3}

Specifies the user allowed to execute OCI CLI in the dump transfer node.

The `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using Oracle Cloud OCI-CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_USER = *`user`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_USERKEY {#GUID-17B05033-2894-4EE2-8A1F-37BD2C21154C}

Specifies the user's authentication key.

The `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using Oracle Cloud OCI-CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_TRANSFERNODE_USERKEY = *`user_auth_key`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SOURCE_USEOCICLI {#GUID-3548ACA4-D63F-461F-83FE-4EE18B1BD134}

Indicates that transfer dump files use Oracle Cloud Infrastructure command line interface (CLI).

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_SOURCE_*` parameters specify details to upload the Dump files from source node Data Pump dump location to Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SOURCE_USEOCICLI = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` or `FALSE`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SRCPROXY_HOSTNAME {#GUID-94829F66-9EF4-439B-847D-8FCEAA542D09}

Specifies the proxy host name to be used on the source database server node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

Property | Description  
---|---  
**Syntax** |  DUMPTRANSFERDETAILS_SRCPROXY_HOSTNAME<br>` = *`hostname`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SRCPROXY_PORT {#GUID-17B15F5E-8500-42B7-B698-9A203E7E011F}

Specifies the proxy port to be used on the source database server node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SRCPROXY_PORT`<br>` = *`port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_SRCPROXY_PROTOCOL {#GUID-674C4DAD-C4F2-4682-A008-65D3B6F70243}

Specifies whether `HTTP` or `HTTPS` proxy protocol is to be used on the source database server node. 

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_SRCPROXY_PROTOCOL = `HTTP` or `HTTPS``  
**Default value** |  ``HTTP``  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### DUMPTRANSFERDETAILS_TARGET_OCIHOME {#GUID-8B32315B-C891-44D1-BFCD-0C2A3528D9A1}

Specifies the Oracle Cloud Infrastructure command line interface (CLI) Binary path.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_OCIHOME = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TARGET_NOSSHUPLOADMETHOD {#GUID-8A51F249-6200-40F1-A504-8A77772AA004}

Specifies the method to use no-ssh upload. When HTTPS is used, `ociWalletLoc` is required to be a valid path. 

Parameter Relationships

When value of the parameter is `HTTPS`, then `ociWalletLoc` is required to be a valid path. For default method, when value of the parameter is `NONE`, data is uploaded to OSS securely using Curl or OCI CLI. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_NOSSHUPLOADMETHOD = `  
**Default value** |  `NONE`  
**Required** |  No  

### DUMPTRANSFERDETAILS_TARGET_OCIWALLETLOC {#GUID-5C181F57-8F62-4740-A2BC-D6008A1C546E}

Specifies the directory path of the OCI SSL wallet location on the target database server node.

Parameter Relationships

Specifies the OCI wallet location in the respective database server.

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_OCIWALLETLOC = < Directory path for OCI SSL wallet location on the target database server node.>`  
**Default value** |  `None`  
**Required** |  No  

### DUMPTRANSFERDETAILS_TARGET_PARTSIZEMB {#GUID-89074EF7-4CBE-4370-B168-399066F9FCB7}

Specifies the part size in MB for chunked transfer.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_PARTSIZEMB = *`integer`*`  
**Default value** |  `128`  
**Range of values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_DUMPDIRPATH {#GUID-84AA5650-5FEB-46C0-9305-76138BBCBD4D}

Specifies the absolute path of the directory to copy or upload dump files.

The `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using the OCI CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through Oracle Cloud Infrastructure command line interface (CLI) or Curl.

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_DUMPDIRPATH = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_HOST {#GUID-D302F24E-01A5-420A-915E-8803B62C0883}

Specifies the dump transfer node host name.

The `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using OCI CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through Oracle Cloud Infrastructure command line interface (CLI) or Curl.

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_HOST = *`host_name`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_SUDOPATH {#GUID-6D06F7A9-8229-4D16-8641-A9C3ABD69BCF}

Specifies the Sudo path on the dump transfer node.

The `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using OCI CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through Oracle Cloud Infrastructure command line interface (CLI) or Curl.

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_SUDOPATH = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_USER {#GUID-9CCA7AB9-DFC2-47D7-83C8-152858209912}

Specifies the user allowed to execute OCI CLI in the dump transfer node.

The `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using OCI CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through Oracle Cloud Infrastructure command line interface (CLI) or Curl. 

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_USER = *`user`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_USERKEY {#GUID-33F11CA6-88A3-4623-B3C1-308DCB1CF962}

Specifies the user's authentication key.

The `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_*` parameters are configured when you use a standalone server as transfer server. 

Transfer dump files using OCI CLI or curl, or copy to or from a node other than the database server. 

This option can be leveraged for cases where OCI CLI is installed and configured in a specific node per data center and the node has the Data Pump export or import directory path shared with it. This avoids the requirement of installing OCI CLI on the database node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through Oracle Cloud Infrastructure command line interface (CLI) or Curl.

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_TRANSFERNODE_USERKEY = *`user_auth_key`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TARGET_USEOCICLI {#GUID-9BCCB62B-6930-4D57-8318-5C684C91361F}

Indicates that transfer dump files use Oracle Cloud Infrastructure command line interface (CLI).

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TARGET_*` parameters specify details to download the Dump files to the target node Data Pump dump location from Oracle Cloud Object Storage. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TARGET_USEOCICLI = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` or `FALSE`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TRANSFERTARGET_DUMPDIRPATH {#GUID-492E964E-BD32-4149-93F5-47721AA80DB3}

Specifies the absolute path of the directory to copy or upload dump files.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TRANSFERTARGET_*` parameters specify details for the target node to copy the Dump files from source node Data Pump dump location or transfer node directory path specified. Default target database host. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TRANSFERTARGET_DUMPDIRPATH = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TRANSFERTARGET_HOST {#GUID-629A2B8A-FC85-4D6A-AC9A-DBCE3FC7BC28}

`DUMPTRANSFERDETAILS_TRANSFERTARGET_HOST` specifies the dump transfer node host name. 

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TRANSFERTARGET_*` parameters specify details for the target node to copy the Dump files from source node Data Pump dump location or transfer node directory path specified. Default target database host. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TRANSFERTARGET_HOST = *`host_name`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TRANSFERTARGET_SUDOPATH {#GUID-A887C2D6-E3E9-43BF-8511-CB00C1184BE6}

Specifies the Sudo path on the dump transfer node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TRANSFERTARGET_*` parameters specify details for the target node to copy the Dump files from source node Data Pump dump location or transfer node directory path specified. Default target database host. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TRANSFERTARGET_SUDOPATH = *`path`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TRANSFERTARGET_USER {#GUID-70D193EF-102C-4E9A-8743-1F5198BB69AF}

Specifies the host user name that has write permission to the indicated dump storage path.

The user specified in `DUMPTRANSFERDETAILS_TRANSFERTARGET_USER` should be allowed to 

* Execute OCI CLI in the specified host to download the dumps from the Object Storage bucket if `DATA_TRANSFER_MEDIUM=OSS`

* Copy the dump files from the source node to the target host specified if `DATA_TRANSFER_MEDIUM=COPY`. 




See [Configuring the Transfer Medium and Specifying Transfer Nodes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-3580F7B3-03A5-4ADA-9C2F-FEADF9E125A7) for more information about configuring the dump transfer details parameters. 

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TRANSFERTARGET_*` parameters specify details for the target node to copy the Dump files from source node Data Pump dump location or transfer node directory path specified. Default target database host. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TRANSFERTARGET_USER = *`host_username`*`  
**Default value** |  `There is no default value`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TRANSFERTARGET_USERKEY {#GUID-527914CC-03CA-4123-81C5-123C1AA8B1A6}

Specifies the user's authentication key.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. 

Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl.

The `DUMPTRANSFERDETAILS_TRANSFERTARGET_*` parameters specify details for the target node to copy the Dump files from source node Data Pump dump location or transfer node directory path specified. Default target database host. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TRANSFERTARGET_USERKEY = *`user_auth_key`*`  
**Default value** |  No default value  
**Range of values** |  A string value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is `COMPLETED`  

### DUMPTRANSFERDETAILS_TGTPROXY_HOSTNAME {#GUID-376C1B5B-B70A-435F-A370-48921770BC56}

Specifies the proxy host name to be used on the target database server node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TGTPROXY_HOSTNAME`<br>` = *`hostname`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is COMPLETED   

### DUMPTRANSFERDETAILS_TGTPROXY_PORT {#GUID-A0618C95-7369-4C9F-ADC7-59F567BE5833}

Specifies proxy port number to be used on the target database server node.

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TGTPROXY_PORT`<br>` =*`port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_TRANSFER_DUMPS_SRC` phase is COMPLETED   

### DUMPTRANSFERDETAILS_TGTPROXY_PROTOCOL {#GUID-BBA2C472-07FC-4329-A9D9-DCD025B94925}

Specifies whether `HTTP` or `HTTPS` proxy protocol is to be used on the target database server node. 

Parameter Relationships

The `DUMPTRANSFERDETAILS_*` parameters specify details to transfer dumps from the source node to the target node. Supported modes of transfer are OSS and secure copy. Upload of dumps to OSS can be either through OCI CLI or Curl. 

Property | Description  
---|---  
**Syntax** |  `DUMPTRANSFERDETAILS_TGTPROXY_PROTOCOL = HTTP or `HTTPS``  
**Default value** |  `HTTP`  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### EXCLUDEOBJECTS-LIST_ELEMENT_NUMBER {#GUID-1D3685DD-539E-4466-BF46-6CBD5F3CFD51}

Specifies database objects to exclude from migration.

To exclude multiple objects, increment the integer appended to the parameter name, as shown in the examples below.

`EXCLUDEOBJECTS-1=owner:*`ownerValue1`*, objectName:*`objectNameValue1`*, objectType:*`objectTypeValue1`*`

`EXCLUDEOBJECTS-2=owner:*`ownerValue2`*, objectName:*`objectNameValue2`*, objectType:*`objectTypeValue2`*`

See [Selecting Objects for Migration](preparing-logical-database-migration1.md#GUID-AFAC1F36-BB52-4EFC-9586-18970BC65B43) for more information about using `EXCLUDEOBJECTS`. 

Property | Description  
---|---  
**Syntax** |  `EXCLUDEOBJECTS-*`LIST_ELEMENT_NUMBER`* = owner:*`ownerValue`*, objectName:*`objectNameValue`*, objectType:*`objectTypeValue`*`  
**Default value** |  There is no default value.  
**Range of values** |  An entry specifying the owner, object name, and object type is expected, as shown in the examples above.  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`.   

### GOLDENGATEHUB_ALLOWSELFSIGNEDCERTIFICATE {#GUID-68968802-F79A-4315-BBED-66195E2D96EA}

Indicates whether Oracle GoldenGate server's self-signed HTTPS certificate should be accepted to avoid the ` sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target` error, similar to the `curl --insecure` command. 

Parameter Relationships

For online logical migration, you must set the `GOLDENGATEHUB_*` parameters to provide Zero Downtime Migration with details about Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATEHUB_ALLOWSELFSIGNEDCERTIFICATE = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** | 

* `TRUE`<br>`FALSE`


**Required** |  No  
**Modifiable on Resume** |  Yes  

### GOLDENGATEHUB_ADMINUSERNAME {#GUID-7E8EBBD5-32BE-40BA-AC94-5DAFE8EAF1E6}

Specifies the Oracle GoldenGate deployment's administrator username.

Parameter Relationships

For an Oracle GoldenGate deployment provisioned using Oracle Cloud Marketplace image, retrieve the `oggadmin` username and password credentials in the `/home/opc/ogg-credentials.json` file on the Oracle Cloud compute instance where Oracle GoldenGate is running. For Oracle GoldenGate deployment running in a docker container, obtain the `oggadmin` username and password credentials as indicated at [Administrative Account Password](https://github.com/oracle/docker-images/tree/main/OracleGoldenGate/21c#administrative-account-password). 

For online logical migration, you must set the `GOLDENGATEHUB_*` parameters to provide Zero Downtime Migration with details about Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATEHUB_ADMINUSERNAME = *`user_name`*`  
**Default value** |  There is no default value  
**Range of values** |  Oracle GoldenGate hub's administrator user name  
**Required** |  No*<br>*Required for online logical migration  
**Modifiable on Resume** |  Until `ZDM_PREPARE_GG_HUB` phase is `COMPLETED`.   

### GOLDENGATEHUB_COMPUTEID {#GUID-D09AF51C-971C-462B-ADD9-0282AD627337}

Specifies the Oracle Cloud identifier of the VM.

Parameter Relationships

For online logical migration, you must set the `GOLDENGATEHUB_*` parameters to provide Zero Downtime Migration with details about Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATEHUB_COMPUTEID = *`vm_identifier`*`  
**Default value** |  There is no default value  
**Range of values** |  Oracle Cloud identifier of the VM  
**Required** |  No*<br>*Required for online logical migration  
**Modifiable on Resume** |  Until `ZDM_PREPARE_GG_HUB` phase is `COMPLETED`.   

### GOLDENGATEHUB_SOURCEDEPLOYMENTNAME {#GUID-3AA575E5-E289-40BB-9128-BD5A8DA06BAE}

Specifies the name of the GoldenGate Microservices deployment to operate on the source database.

Parameter Relationships

For online logical migrations, you must set the `GOLDENGATEHUB` parameters to provide Zero Downtime Migration with details about Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATEHUB_SOURCEDEPLOYMENTNAME = *`GG_microservices_deployment_name`*`  
**Default value** |  There is no default value  
**Range of values** |  Name of the GoldenGate Microservices deployment to operate on the source database  
**Required** |  No*<br>*Required for online logical migration  
**Modifiable on Resume** |  Until `ZDM_PREPARE_GG_HUB` phase is `COMPLETED`.   

### GOLDENGATEHUB_TARGETDEPLOYMENTNAME {#GUID-3BCBB41E-A3B0-455F-B172-C75E615DFC5E}

Specifies the name of the GoldenGate Microservices deployment to operate on the target database.

Parameter Relationships

For online logical migrations, you must set the `GOLDENGATEHUB` parameters to provide Zero Downtime Migration with details about Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATEHUB_TARGETDEPLOYMENTNAME = *`GG_microservices_deployment_name`*`  
**Default value** |  There is no default value  
**Range of values** |  Name of the GoldenGate Microservices deployment to operate on the target database  
**Required** |  No*<br>*Required for online logical migration  
**Modifiable on Resume** |  Until `ZDM_PREPARE_GG_HUB` phase is `COMPLETED`.   

### GOLDENGATEHUB_URL {#GUID-FE937ED3-1977-46E2-A7DB-66B53AA094B5}

Specifies the Oracle GoldenGate hub's REST endpoint.

Parameter Relationships

For example, `GOLDENGATEHUB_URL=https://`. For more information, see, [Network](https://docs.oracle.com/en/middleware/goldengate/core/19.1/securing/network.md#GUID-A709DA55-111D-455E-8942-C9BDD1E38CAA%23). 

For online logical migrations, you must set the `GOLDENGATEHUB` parameters to provide Zero Downtime Migration with details about Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATEHUB_URL = *`GG_hub_rest_endpoint`*`  
**Default value** |  There is no default value  
**Range of values** |  Oracle GoldenGate hub's REST endpoint  
**Required** |  No*<br>*Required for online logical migration  
**Modifiable on Resume** |  Until `ZDM_PREPARE_GG_HUB` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_ACCEPTABLELAG {#GUID-6083CD6B-D87B-40AD-A9A7-19518E62F335}

Specifies GoldenGate end-to-end latency lag time

Zero Downtime Migration monitors GoldenGate end-to-end latency until the lag time is lower than the value (in seconds) specified in `GOLDENGATESETTINGS_ACCEPTABLELAG`. 

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_ACCEPTABLELAG = *`seconds`*`  
**Default value** |  30 seconds  
**Required** |  No  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_MONITOR_GG_LAG` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE {#GUID-92758D4E-85E6-4D3E-A6C1-337432587341}

Tunes Oracle GoldenGate Integrated Capture.

Use this setting to automatically configure relevant parameters to achieve the desired throughput and latency.

Parameter Relationships

The `GOLDENGATESETTINGS_EXTRACT_*` parameters define settings for the Integrated Extract process. 

Only one Extract can be configured.

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_EXTRACT_PERFORMANCEPROFILE = [HIGH | MEDIUM | LOW_RES]`  
**Default value** |  `HIGH`  
**Valid values** |  `HIGH`, `MEDIUM`, `LOW_RES`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_EXTRACT_SRC` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_CHECKINTERVAL {#GUID-F5ABF18A-B5E9-44A9-A831-AB643357268E}

Specifies the frequency in seconds with which Oracle GoldenGate checks for long-running transactions.

Parameter Relationships

`GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_*` parameters specify a length of time in seconds that a transaction can be open before Extract generates a warning message that the transaction is long-running. 

The `GOLDENGATESETTINGS_EXTRACT_*` parameters define settings for the Integrated Extract process. 

Only one Extract can be configured.

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_CHECKINTERVAL = *`integer`*`  
**Default value** |  There is no default value  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_EXTRACT_SRC` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_DURATION {#GUID-0393A20A-C0DE-41E1-9B97-2751B9BC9981}

Specifies a length of time in seconds that a transaction can be open before the GoldenGate Extract process generates a warning message that the transaction is long-running.

Parameter Relationships

`GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_*` parameters specify a length of time in seconds that a transaction can be open before Extract generates a warning message that the transaction is long-running. 

The `GOLDENGATESETTINGS_EXTRACT_*` parameters define settings for the Integrated Extract process. 

Only one Extract can be configured.

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_EXTRACT_WARNLONGTRANS_DURATION = *`integer`*`  
**Default value** |  There is no default value  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_EXTRACT_SRC` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_EXTRACT_PARALLELISM {#GUID-E98A3901-E973-4630-8280-56EB52BF0A8C}

Specifies the number of processes supporting the database logmining server.

Parameter Relationships

The `GOLDENGATESETTINGS_EXTRACT_*` parameters define settings for the Integrated Extract process. Only one Extract can be configured. 

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_EXTRACT_PARALLELISM = *`integer`*`  
**Default value** |  2  
**Valid values** |  A positive integer value is expected > **note:** Parallelism greater than 1 is not supported for Oracle Database Standard Edition 2.   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_EXTRACT_TGT` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS {#GUID-815ACFB2-E654-4857-909D-99AB1A4C3E01}

Specifies whether to reload objects that are not supported by Oracle GoldenGate for replication.

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Set this parameter to `TRUE` to reload objects that are not supported by Oracle GoldenGate for replication. 

For Online logical migration, ZDM evaluation phase `ZDM_VALIDATE_SRC` queries view `DBA_GOLDENGATE_SUPPORT_MODE` and reports the list of tables with Oracle GoldenGate `support_mode = NONE` or `support_mode = PLSQL` as objects to be excluded for online logical migration. 

Set the ZDM parameter GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS to: 

* automatically exclude tables with Oracle GoldenGate support_mode = NONE or support_mode = PLSQL for Oracle GoldenGate replication.
* automatically reload tables with Oracle GoldenGate support_mode = NONE or support_mode = PLSQL in target database using Oracle Data Pump during ZDM phase ZDM_RELOAD_PARALLEL_EXPORT_IMPORT during switchover window.



As part of online logical migrations, when value of the `GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS = TRUE`, then the audit trails are also imported during the reload phase `ZDM_RELOAD_PARALLEL_EXPORT_IMPORT`. 

When value of the `GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS = TRUE`, then `SOURCEDATABASE_ALLOWTEMPTABLE` is set to `TRUE` by default. If this is unexpected, then you need to set the value of `SOURCEDATABASE_ALLOWTEMPTABLE` to `FALSE`. However, setting it to `FALSE` can lead to Datapump import performance issue. 

For more details, see [Handling Application Switchover in a Logical Migration](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-1455CCE0-A583-452B-8FA0-5B5F343EFE17). 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_RELOADUNREPLICATEDOBJECTS = TRUE | FALSE`  
**Default value** |  `FALSE`  
**Required** |  No  
**Modifiable on Resume** | Until `ZDM_VALIDATE_SRC` phase is completed.   

### GOLDENGATESETTINGS_REPLICAT_MAPPARALLELISM {#GUID-3BF06855-84E0-46FC-AB8A-6CDB4290B549}

Specifies the number of threads used to read GoldenGate trail files.

Starting with Oracle Zero Downtime Migration 21c (21.4) release, this property is deprecated and will be desupported in a future release. For more information, see [GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-7D0DAA07-ABB6-4AFF-B736-70F241A1F602). 

Valid for Parallel Replicat.

Parameter Relationships

The `GOLDENGATESETTINGS_REPLICAT_*` parameters define settings for the Parallel Replicat process. 

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_REPLICAT_MAPPARALLELISM = *`integer`*`  
**Default value** |  4  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_REPLICAT_TGT` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_REPLICAT_MAXAPPLYPARALLELISM {#GUID-0EE85063-8DE2-4CCC-BE52-FBBCA135ACD5}

Defines the range in which the GoldenGate Replicat automatically adjusts its apply parallelism.

Starting with Oracle Zero Downtime Migration 21c (21.4) release, this property is deprecated and will be desupported in a future release. For more information, see [GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-7D0DAA07-ABB6-4AFF-B736-70F241A1F602). 

Valid for Parallel Replicat.

Parameter Relationships

The `GOLDENGATESETTINGS_REPLICAT_*` parameters define settings for the Parallel Replicat process. 

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_REPLICAT_MAXAPPLYPARALLELISM = *`integer`*`  
**Default value** |  50  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_REPLICAT_TGT` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_REPLICAT_MINAPPLYPARALLELISM {#GUID-BA3DA6DF-2C8E-49D3-B602-58BC9CADC97E}

Defines the range in which the GoldenGate Replicat automatically adjusts its apply parallelism.

Starting with Oracle Zero Downtime Migration 21c (21.4) release, this property is deprecated and will be desupported in a future release. For more information, see [GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-7D0DAA07-ABB6-4AFF-B736-70F241A1F602). 

Valid for Parallel Replicat.

Parameter Relationships

The `GOLDENGATESETTINGS_REPLICAT_*` parameters define settings for the Parallel Replicat process. 

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_REPLICAT_MINAPPLYPARALLELISM = *`integer`*`  
**Default value** |  4  
**Valid values** |  An integer value is expected  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_REPLICAT_TGT` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE {#GUID-7D0DAA07-ABB6-4AFF-B736-70F241A1F602}

Tunes GoldenGate Replicat.

Starting with Oracle Zero Downtime Migration 21c (21.4) release, the following properties are deprecated and will be desupported in a future release:

* `GOLDENGATESETTINGS_REPLICAT_MAPPARALLELISM`
* `GOLDENGATESETTINGS_REPLICAT_APPLYPARALLELISM`
* `GOLDENGATESETTINGS_REPLICAT_MAXAPPLYPARALLELISM`
* `GOLDENGATESETTINGS_REPLICAT_MINAPPLYPARALLELISM`



These parameters are replaced by the `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE` parameter. If you configure the `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE` parameter, then you do not have to configure these deprecated parameters. However, if you do configure these deprecated parameters, then the `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE` parameter will not be valid. 

Set the performance profile to automatically configure relevant parameters to achieve the desired throughput and latency.

Parameter Relationships

The `GOLDENGATESETTINGS_REPLICAT_*` parameters define settings for simplifying the tuning of GoldenGate Replicat. 

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_REPLICAT_PERFORMANCEPROFILE = `HIGH`|`LOW``  
**Default value** |  `HIGH`  
**Valid values** |  Use `HIGH` when there is no concurrent workload on target database. <br>Use `LOW` when there is concurrent workload on target database.  > **note:** <br> * When `HIGH` is set, `Replicat Mappers` = `5` and `Appliers` = `2 * CPU_COUNT`<br> *When `LOW` is set, `Replicat Mappers` = `4` and `Appliers` = `CPU_COUNT / 2`**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_REPLICAT_TGT` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_REPLICATEDDL {#GUID-EBF50453-536D-4A9D-B4EC-D2048F802B42}

Specifies whether to set Oracle GoldenGate Replicat parameter `GOLDENGATESETTINGS_REPLICATEDDL`. 

During the migration period it is recommended to avoid DDL operations. When DDL is replicated, Oracle GoldenGate Replicat serializes data to ensure that there are no locking issues between DML and DDL on the same objects. This can slow down the replication progress.

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_REPLICATEDDL = TRUE | FALSE`  
**Default value** |  `FALSE`  
**Required** |  No  
**Modifiable on Resume** | Until ZDM_MONITOR_GG_LAG phase is `COMPLETED`.   

### GOLDENGATESETTINGS_RELOADAQOBJECTS {#GUID-1A247A02-D531-4876-8863-E966FF22988A}

Allows users to specify if AQ objects need to be reloaded post import.

> **note:** If you intend to reload AQ, then apply the following there is RDBMS patches for source and target databases: 

* Apply the patch for bug 34174611 on source database. 
* Apply the patch for Bug 34314402 that addresses table_exists_action for AQ tables on target database. 



Ensure that you have applied the respective patches as mentioned above for the source and target databases before setting GOLDENGATESETTINGS_RELOADAQOBJECTS=TRUE.

When GOLDENGATESETTINGS_RELOADAQOBJECTS = TRUE, ZDM attempts to reload the AQ objects as part of the ZDM_RELOAD_PARALLEL_EXPORT_IMPORT phase post the initial import and replication. When GOLDENGATESETTINGS_RELOADAQOBJECTS = FALSE, ZDM assumes that the source database does not contain any AQ objects which should be reloaded. 

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_RELOADAQOBJECTS = TRUE | FALSE`  
**Default value** |  `FALSE`  
**Required** |  No  
**Modifiable on Resume** | Until ZDM_CREATE_GG_REPLICAT_TGT phase is `COMPLETED`.   

### GOLDENGATESETTINGS_REPLICATIONMODE {#GUID-4D555361-4C74-4E64-9318-1F4BEE876E87}

Allows the users to select INTEGRATED or NONINTEGRATED replication.

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

For more information on `INTEGRATED` and `NONINTEGRATED` replication , see [Configuring Oracle GoldenGate Replicat](https://docs.oracle.com/en/middleware/goldengate/core/21.3/oracle-db/configuring-oracle-goldengate-apply.md#GUID-ED4D0DAA-18B9-48A6-A3DC-D5AFE9877A93). 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_REPLICATIONMODE = PARALLEL_INTEGRATED | PARALLEL_NON_INTEGRATED`  
**Default value** |  `NON_INTEGRATED`  
**Required** |  No  
**Modifiable on Resume** | Until ZDM_CREATE_GG_REPLICAT_TGT phase is `COMPLETED`.   

### GOLDENGATESETTINGS_REPLICAT_SPLITTRANSRECS {#GUID-DAA1EDD3-F754-4C5F-A962-CDED3746EA10}

Specifies that large transactions should be divided into pieces of specified size (records) and applied in parallel.

Parameter Relationships

Valid for Parallel Replicat.

The `GOLDENGATESETTINGS_REPLICAT_*` parameters define settings for simplifying the tuning of GoldenGate Replicat. 

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_REPLICAT_SPLITTRANSRECS = integer`  
**Default value** |  `100000`  
**Valid values** | An integer value is expected.  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_CREATE_GG_REPLICAT_TGT` phase is `COMPLETED`.   

### GOLDENGATESETTINGS_FEATUREGROUP {#GUID-80E440A8-449E-4EAE-B6F1-399C3E0B0E21}

Allows the users to select specific feature groups for procedural replication. The feature group name includes` ALL_SUPPORTED`, `AQ`, `FGA`, `DBFS`, and so on. 

See [Oracle Fusion Middleware Reference for Oracle GoldenGate, 18c (18.1.0)](https://docs.oracle.com/cd/F13565_01/reference/reference-oracle-goldengate.pdf) for more information. 

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_FEATUREGROUP = `  
**Default value** |  `ALL_SUPPORTED`  
**Required** |  No  
**Modifiable on Resume** | Until ZDM_MONITOR_GG_LAG phase is `COMPLETED`.   

### GOLDENGATESETTINGS_ENABLEPROCEDURALREPLICATION {#GUID-C42CF0BB-5AB6-4DAC-90B3-EC17F66C76DE}

Allows the users to enable procedural replication.

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_ENABLEPROCEDURALREPLICATION = TRUE | FALSE`  
**Default value** |  `FALSE`  
**Required** |  No  
**Modifiable on Resume** | Until ZDM_CREATE_GG_REPLICAT_TGT phase is `COMPLETED`.   

### GENFIXUP {#GUID-13C6C8D4-1CA4-4BC5-8D56-D810FA7C6792}

Displays the path to remedial scripts for failing checks when a migration is run with Cloud Premigration Advisor Tool (CPAT) enabled. Generate the ZDM fixup scripts.

Parameter Relationships

The following values are possible: 

* `YES`: Generate the SQL fixup scripts for checks such as `gg_enabled_replication, gg_force_logging, gg_supplemental_log_data_min, has_libraries, has_low_streams_pool_size, has_columns_of_rowid_type, has_java_objects` and `has_java_source`. 
* `NO`: Does not generate SQL fixup scripts. 



Property | Description  
---|---  
**Syntax** |  `GENFIXUP = NO | YES`  
**Default value** |  `NO`  
**Required** |  No  
**Modifiable on Resume** | Until ZDM_SETUP_SRC phase is `COMPLETED`.   

### GOLDENGATESETTINGS_USEFLASHBACKQUERY {#GUID-F3D2A6B9-BF71-49F6-BDB2-63FE8635AC74}

Specifies whether to set Oracle GoldenGate Extract parameters that allow ID KEY support mode objects to be replicated.

If you set parameter `GOLDENGATESETTINGS_USEFLASHBACKQUERY = TRUE`, Zero Downtime Migration sets the following Oracle GoldenGate Extract parameters that allow ID KEY support mode objects to be replicated. 

> **note:** Zero Downtime Migration 21.3 users must apply Patch 33509650: ZDM PATCH USING MOS to use this feature. 

Parameter Relationships

For online logical migrations, you can set the optional `GOLDENGATESETTINGS_*` parameters to provide Zero Downtime Migration with details about the Oracle GoldenGate Microservices configuration. 

Property | Description  
---|---  
**Syntax** |  `GOLDENGATESETTINGS_USEFLASHBACKQUERY = TRUE | FALSE`  
**Default value** |  `FALSE`  
**Required** |  No  
**Modifiable on Resume** |   

### IGNOREEXPORTERRORS {#GUID-C7998D83-C17B-4055-A826-357264915B26}

Specifies comma separated list of ORA errors to be ignored for Oracle Data Pump export. For any new error to be ignored, suffix the new error to the list of default errors to be ignored. 

Property | Description  
---|---  
**Syntax** |  `IGNOREEXPORTERRORS = ORA_error_list`  
**Default value** |  `ORA-31684,ORA-39111,ORA-39082`  
**Range of values** |  `ORA-31684,ORA-39111,ORA-39082`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`.   

### IGNOREIMPORTERRORS {#GUID-17B3D582-E236-4AD0-95DF-C12CE896204E}

Specifies comma separated list of ORA errors to be ignored for Oracle Data Pump import. For any new error to be ignored, suffix the new error to the list of default errors to be ignored. 

Property | Description  
---|---  
**Syntax** |  `IGNOREIMPORTERRORS = ORA_error_list`  
**Default value** |  `ORA-31684,ORA-39111,ORA-39082`  
**Range of values** |  `ORA-31684,ORA-39111,ORA-39082`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`.   

### INCLUDEOBJECTS-LIST_ELEMENT_NUMBER {#GUID-899F39B5-0B3F-4406-84A4-65258986DD6B}

Specifies database objects to include for migration.

To include multiple objects, increment the integer appended to the parameter name, as shown in the examples below.

`INCLUDEOBJECTS-1=owner:*`ownerValue1`*, objectName:*`objectNameValue1`*, objectType:*`objectTypeValue1`*`

`INCLUDEOBJECTS-2=owner:*`ownerValue2`*, objectName:*`objectNameValue2`*, objectType:*`objectTypeValue2`*`

See [Selecting Objects for Migration](preparing-logical-database-migration1.md#GUID-AFAC1F36-BB52-4EFC-9586-18970BC65B43) for more information about using `INCLUDEOBJECTS`. 

Property | Description  
---|---  
**Syntax** |  `INCLUDEOBJECTS-*`LIST_ELEMENT_NUMBER`* = owner:*`ownerValue`*, objectName:*`objectNameValue`*, objectType:*`objectTypeValue`*`  
**Default value** |  There is no default value  
**Range of values** |  An entry specifying the owner, object name, and object type is expected, as shown in the examples above.  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_EXPORT_SRC` phase is `COMPLETED`, or until `ZDM_DATAPUMP_IMPORT_TGT` phase when using `DATA_TRANSFER_MEDIUM=DBLINK`.   

### MIGRATION_METHOD {#GUID-10275193-115B-47D1-A9A8-4BD3B95FE305}

In a logical migration, specifies whether the migration is online logical or offline logical

The required `MIGRATION_METHOD` parameter specifies whether the migration is online (Data Pump with Oracle GoldenGate replication) or offline (Data Pump only). 

Property | Description  
---|---  
**Syntax** |  `MIGRATION_METHOD = {ONLINE_LOGICAL \| OFFLINE_LOGICAL}`  
**Default value** |  `OFFLINE_LOGICAL`  
**Range of values** |  `ONLINE_LOGICAL` migrate the database with Data Pump, with replication using Oracle GoldenGate for minimal downtime <br>`OFFLINE_LOGICAL` migrate the database with Data Pump, with no replication   
**Required** |  Yes  
**Modifiable on Resume** |  No  

### OCIAUTHENTICATIONDETAILS_REGIONID {#GUID-66AA3CC6-6B06-4A85-92CB-9D02EB469960}

Specifies the OCI region identifier.

See the Region Identifier column in the table at [Regions and Availability Domains](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm). 

Parameter Relationships

To call REST APIs, you must configure the `OCIAUTHENTICATIONDETAILS_*` parameters. 

Property | Description  
---|---  
**Syntax** |  `OCIAUTHENTICATIONDETAILS_REGIONID = *`region_id`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  Yes  

### OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_FINGERPRINT {#GUID-F371C18E-F8FE-4825-B83B-11239421B385}

Specifies the fingerprint of the public API key.

See [Required Keys and OCIDs](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#RequiredKeysandOCIDs) for more information. 

Parameter Relationships

To call REST APIs, you must configure the `OCIAUTHENTICATIONDETAILS_*` parameters. 

Property | Description  
---|---  
**Syntax** |  `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_FINGERPRINT = *`fingerprint`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  Yes  

### OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_PRIVATEKEYFILE {#GUID-75C44383-D626-4A5D-973B-C7C475BF9C1F}

Specifies the absolute path of API private key file.

See [Required Keys and OCIDs](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#RequiredKeysandOCIDs) for more information. 

Parameter Relationships

To call REST APIs, you must configure the `OCIAUTHENTICATIONDETAILS_*` parameters. 

Property | Description  
---|---  
**Syntax** |  `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_PRIVATEKEYFILE = *`path`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  Yes  

### OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_TENANTID {#GUID-605F176C-3AD6-4841-B9C9-579F4176450B}

Specifies the OCID of the OCI tenancy.

You can find the tenant OCID on OCI at Governance and Administration, Administration, Tenancy Details. The tenancy OCID is shown under Tenancy Information.

Example: ocid1.tenancy.oc1..aaaaaaaaba3pv6wkcr4jqae5f44n2b2m2yt2j6rx32uzr4h25vqstifsfdsq

See [Managing the Tenancy](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingtenancy.htm) and [Required Keys and OCIDs](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#RequiredKeysandOCIDs) for more information. 

Parameter Relationships

To call OCI REST APIs, you must configure the `OCIAUTHENTICATIONDETAILS_*` parameters. 

Property | Description  
---|---  
**Syntax** |  `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_TENANTID = *`ocid_string`*`  
**Default value** |  There is no default value  
**Valid values** |  A string value is expected  
**Required** |  Yes  
**Modifiable on Resume** |  Yes  

### OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_USERID {#GUID-A2A2DE9E-7F8E-4F4A-BEBE-5F25608044A6}

Specifies the OCID of the IAM user.

You can find the IAM OCID on OCI at Console, Profile, User Settings.

See [Managing Users](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm) and [Required Keys and OCIDs](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#RequiredKeysandOCIDs) for more information. 

Parameter Relationships

To call OCI REST APIs, you must configure the `OCIAUTHENTICATIONDETAILS_*` parameters. 

Property | Description  
---|---  
**Syntax** |  `OCIAUTHENTICATIONDETAILS_USERPRINCIPAL_USERID = *`userid`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  Yes  

### OCIPROXY_HOSTNAME {#GUID-095DF33F-E2E5-477A-B05E-37A7999504A7}

Specifies the HTTP proxy hostname required for OCI REST endpoint access from ZDM host.

Parameter Relationships

The `OCIPROXY_*` parameters specify details about the proxy for connecting to OCI REST endpoints. 

Property | Description  
---|---  
**Syntax** |  `OCIPROXY_HOSTNAME = *`hostname`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### OCIPROXY_PORT {#GUID-4B0CE6AB-F1E0-45AA-B9CF-98E9F41AE3A9}

Specifies the HTTP proxy port required for OCI REST endpoint access from ZDM host.

Parameter Relationships

The `OCIPROXY_*` parameters specify details about the proxy for connecting to OCI REST endpoints. 

Property | Description  
---|---  
**Syntax** |  `OCIPROXY_PORT = *`port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### OCIPROXY_PROTOCOL {#GUID-E265968E-5184-4BDA-852C-142EDC3AE3C7}

Specifies the HTTP proxy protocol required for OCI REST endpoint access from ZDM host.

Parameter Relationships

The `OCIPROXY_*` parameters specify details about the proxy for connecting to OCI REST endpoints. 

Property | Description  
---|---  
**Syntax** |  `OCIPROXY_PROTOCOL = HTTP or HTTPS`  
**Default value** |  `None`  
**Required** |  Yes  
**Modifiable on Resume** |  Yes  

### OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_KEY {#GUID-C4A0F23B-3FE6-4016-ACAE-56C17156CD12}

Specify the `REALM_KEY` properties if you will register a new OCI realm required by `REGION` properties. 

Parameter Relationships

Refer to Realm Key in the table available at [About Regions and Availability Domains](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm#About). 

> **note:** ZDM is bundled with a list of the known OCI regions and any region that gets added after the release of ZDM may not get recognized and the ZDM job may report an error on an unidentified OCI region. If the given OCI region is not recognized by ZDM, specify the OCI region details in `OCISDKCONFIG_REGISTERREGIONDETAILS*` for a database in the given region. 

The `OCISDKCONFIG_*` parameters specify OCI SDK configurations. 

Property | Description  
---|---  
**Syntax** |  `OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_KEY = *``*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Can be modified in any phase, no restrictions.  

### OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_DOMAINCOMPONENT {#GUID-B49E0D49-8DFD-4FC4-9E61-1A807B46372E}

Specify the second level domain of the realm. Specify the REALM_DOMAINCOMPONENT properties if you will register a new OCI realm required by REGION properties.

Parameter Relationships

> **note:** ZDM is bundled with a list of the known OCI regions and any region that gets added after the release of ZDM may not get recognized and the ZDM job may report an error on an unidentified OCI region. If the given OCI region is not recognized by ZDM, specify the OCI region details in `OCISDKCONFIG_REGISTERREGIONDETAILS*` for a database in the given region. 

The `OCISDKCONFIG_*` parameters specify OCI SDK configurations. 

Property | Description  
---|---  
**Syntax** |  `OCISDKCONFIG_REGISTERREGIONDETAILS_REALM_DOMAINCOMPONENT = *``*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Can be modified in any phase, no restrictions.  

### OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_ID {#GUID-5B26FCB5-0D1B-4A5B-AB13-1FC579D6005F}

Specify the `REGISTERREGIONDETAILS_*` properties when working with a new OCI region. 

Parameter Relationships

Refer to Region Identifier in the table available at [About Regions and Availability Domains](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm#About). 

> **note:** ZDM is bundled with a list of the known OCI regions and any region that gets added after the release of ZDM may not get recognized and the ZDM job may report an error on an unidentified OCI region. If the given OCI region is not recognized by ZDM, specify the OCI region details in `OCISDKCONFIG_REGISTERREGIONDETAILS*` for a database in the given region. 

The `OCISDKCONFIG_*` parameters specify OCI SDK configurations. 

Property | Description  
---|---  
**Syntax** |  `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_ID = *``*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Can be modified in any phase, no restrictions.  

### OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_KEY {#GUID-44BE88C8-E42E-4938-B4AF-38F09BE61AA7}

Specify the `REGISTERREGIONDETAILS_*` properties when working with a new OCI region. 

Parameter Relationships

Refer to Region Key in the table available at [About Regions and Availability Domains](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm#About). 

> **note:** ZDM is bundled with a list of the known OCI regions and any region that gets added after the release of ZDM may not get recognized and the ZDM job may report an error on an unidentified OCI region. If the given OCI region is not recognized by ZDM, specify the OCI region details in `OCISDKCONFIG_REGISTERREGIONDETAILS*` for a database in the given region. 

The `OCISDKCONFIG_*` parameters specify OCI SDK configurations. 

Property | Description  
---|---  
**Syntax** |  `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_KEY = *``*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Can be modified in any phase, no restrictions.  

### OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_REALMKEY {#GUID-A8B42EFD-5D10-482B-8758-CB3673C970C4}

Specify the `REGISTERREGIONDETAILS_*` properties when working with a new OCI region. 

Parameter Relationships

Refer to Realm Key in the table available at [About Regions and Availability Domains](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm#About). 

> **note:** ZDM is bundled with a list of the known OCI regions and any region that gets added after the release of ZDM may not get recognized and the ZDM job may report an error on an unidentified OCI region. If the given OCI region is not recognized by ZDM, specify the OCI region details in `OCISDKCONFIG_REGISTERREGIONDETAILS*` for a database in the given region. 

The `OCISDKCONFIG_*` parameters specify OCI SDK configurations. 

Property | Description  
---|---  
**Syntax** |  `OCISDKCONFIG_REGISTERREGIONDETAILS_REGION_REALMKEY = *``*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Can be modified in any phase, no restrictions.  

### PROFILE {#GUID-D5717422-94E6-48DB-849B-81499D2C91A8}

Specifies the full path to the source database profile file. ZDM maintains a key-value template for each of the supported database PROFILE. ZDM defines custom defaults with every PROFILE supported parameter.

Property | Description  
---|---  
**Syntax** |  `PROFILE = {EBS.profile, FA.profile}`  
**Default value** |  `NONE`  
**Range of values** |   
**Required** |  No  
**Modifiable on Resume** |  Yes  

### RUNCPATREMOTELY {#GUID-85FB677B-4BD0-4EBE-BE53-10DD48742D57}

Specifies whether Cloud Premigration Advisor Tool (CPAT) should run on the Zero Downtime Migration service host with a remote connection to the source database.

See [Running CPAT with a Remote Connection](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-7973872B-ED7C-433C-BCB6-DDEAF4EC55BB) for details. 

Property | Description  
---|---  
**Syntax** |  `RUNCPATREMOTELY = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` = run CPAT remotely <br>`FALSE` = do not run CPAT remotely   
**Required** |  No  
**Modifiable on Resume** |  No  

### RUNFIXUPS {#GUID-0162CA9C-0826-4ACD-A69F-7D26F16C959C}

Specifies if you want to run automated fixups on the target database host.

Property | Description  
---|---  
**Syntax** |  `RUNFIXUPS = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` = Automated fixup scripts are processed for the target database. <br>`FALSE` = Automated Fixup scripts are not processed for the target database.   
**Required** |  No  
**Modifiable on Resume** |  No  

### REFRESHMVIEWS {#GUID-5F74127D-24DF-42FC-B31A-8469F1EEA8BF}

Specifies whether you want to add the `ZDM_REFRESH_MVIEW` phase, for refreshing the materialized views post migration. 

If `REFRESHMVIEW = FALSE` , then the `ZDM_REFRESH_MVIEW` phase is not added and materialized views are not refreshed post migration. 

> **note:** If you do not set this property, by default the `ZDM_REFRESH_MVIEW` phase will be part of the migration for CLI based migration. However, for the Database Migration Service, the `ZDM_REFRESH_MVIEW` phase will not be a part of the migration. 

See [Logical Migration Phase Descriptions](https://docs.oracle.com/en/database/oracle/zero-downtime-migration/21.4/zdmug/zero-downtime-migration-process-phases.md#GUID-DA18E66B-2943-4C67-91B7-0CF3E0B0DC6B) for details. 

Property | Description  
---|---  
**Syntax** |  `REFRESHMVIEWS = {TRUE \| FALSE}`  
**Default value** |  `NULL`  
**Range of values** |  `TRUE` = enables refresh of materialized views <br>`FALSE` = does not enable refresh of materialized views <br>`NULL`  
**Required** |  No  
**Modifiable on Resume** |  No  

### RELOADOBJECTS-LIST_ELEMENT_NUMBER {#GUID-07CBC6C7-07E4-41C8-8D11-01D71ABC25C4}

Specifies if the database objects have to be migrated in offline mode in an online job during target database instantiation.

Objects are migrated to the target database part of Data Pump for the target database instantiation. However, the objects are not considered for online syncing from source to target using Oracle GoldenGate. This can cater to any objects that are not to be replicated or not supported for replication. If objects are to be excluded from migration, then use the `EXCLUDEOBJECTS` parameter. 

For objects specified in `RELOADOBJECTS-*`, objects with prefix `AQ$_` will be ignored. Basically, AQ objects are ignored if specified in the RELOADOBJECTS- property. To include AQ objects for reload, you must use `GOLDENGATESETTINGS_RELOADAQOBJECTS=TRUE`. ZDM reloads all the AQ objects in such case. 

The specified objects are not replicated using Oracle GoldenGate. Instead, the specified objects are reloaded to the target database using Oracle Data Pump during phase `ZDM_RELOAD_PARALLEL_EXPORT_IMPORT` when there is no active workload on the source database. 

> **note:** 

* This parameter is only supported for online logical migration. 
* Use of wildcard expressions are allowed. 



To reload multiple objects, increment the integer appended to the parameter name, as shown in the examples below.

`RELOADOBJECTS-1=owner:*`ownerValue1`*, objectName:*`objectNameValue1`*, objectType:*`objectTypeValue1`*`

`RELOADOBJECTS-2=owner:*`ownerValue2`*, objectName:*`objectNameValue2`*, objectType:*`objectTypeValue2`*`

See [Selecting Objects for Migration](preparing-logical-database-migration1.md#GUID-AFAC1F36-BB52-4EFC-9586-18970BC65B43) for more information about using `RELOADOBJECTS`. 

> **note:** You must note the following restrictions: 

* Ensure to not reload `AQ$` objects or any `ORACLE_MAINTAINED` objects. 
* Using `DBLINK` method is not supported for the reload feature. 
* The base `AQ` tables can be reloaded, which will reload all the dependent objects. 
* To reload the base AQ tables, the Oracle Database 19c source database must include the latest Datapump Bundle patch that includes patch for bug 34174611. 
* Reload of base AQ should not be performed for database versions earlier than Oracle Database 19c. 
* Reloading the base AQ tables with support mode as `internal` or `PLSQL` without the patch for bug 34174611 results in missing AQ$ table. 



Property | Description  
---|---  
**Syntax** |  `RELOADOBJECTS-*`LIST_ELEMENT_NUMBER`* = owner:*`ownerValue`*, objectName:*`objectNameValue`*, objectType:*`objectTypeValue`*`  
**Default value** |  `False`  
**Range of values** | `True``False`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_VALIDATE_SRC` phase is `COMPLETED`.   

### COPYCPATREPORTTOZDMHOST {#GUID-D6C7DD16-CECF-4ED9-8E2A-F57C3B6D4225}

Specifies whether to copy the CPAT report log from the source database server to the ZDM server host job log directory for easier access to logs and more efficient troubleshooting.

Use this parameter when value of `RUNCPATREMOTELY=FALSE` and CPAT reports are required to be located in ZDM host. The `COPYCPATREPORTTOZDMHOST` parameter is skipped and the value is always `FALSE` when `RUNCPATREMOTELY=TRUE`. 

Property | Description  
---|---  
**Syntax** |  `COPYCPATREPORTTOZDMHOST = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` = copy the CPAT report log <br>`FALSE` = do not copy the CPAT report log   
**Required** |  No  
**Modifiable on Resume** |  No  

### FORCECPATENUSLOCALE {#GUID-E9F90D76-D5E8-4F06-AAD2-382F7F716ABA}

Forces CPAT to use the en_US.UTF-8 Locale.

Property | Description  
---|---  
**Syntax** |  `FORCECPATENUSLOCALE = {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` = Enable CPAT to use the en_US.UTF-8 Locale. <br>`FALSE` = Allows CPAT to use the default locale.   
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_ADMINUSERNAME {#GUID-1D610347-BA45-49B7-B746-912478AEA754}

Specifies the source CDB administrator user name.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_*` parameters specify connection details for the source database CDB root. 

> **note:** `SOURCECONTAINERDATABASE_*` is required for online migration when the source is a PDB. The Oracle GoldenGate Capture runs at `CDB$ROOT` level. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_ADMINUSERNAME = *`username`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  No  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE {#GUID-676ABC80-A2D6-4D88-B3AD-90BF43761C26}

Specifies the identity file to access the bastion, as part of the database connection details for bastion-based access to the database.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` parameters specify details for bastion based access to the database. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE = *`bastion_id_file`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP {#GUID-7D4124C1-6324-4101-B728-CC7B305D05A9}

Specifies the IP address of the source CDB bastion host, as part of the database connection details for bastion-based access to the database.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` parameters specify details for bastion based access to the database. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP = *`bastion_ip_address`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT {#GUID-09BA4FDC-D6B5-4527-AB80-AFE674C1520B}

Specifies the bastion host port, as part of the database connection details for bastion-based access to the container database (CDB).

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` parameters specify details for bastion based access to the database. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT = *`bastion_port_number`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP {#GUID-79C1A550-5A87-4C41-8260-B67A1BE93ED3}

Specifies the remote host IP address to access from the bastion, as part of the database connection details for bastion-based access to the container database (CDB).

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` parameters specify details for bastion based access to the database. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP = *`ip_address`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME {#GUID-B090F838-8684-4A9C-AD3F-0A3EB0135EF2}

Specifies the user name to access the bastion, as part of the database connection details for bastion-based access to the container database (CDB).

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_*` parameters specify details for bastion based access to the database. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME = *`username`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_HOST {#GUID-8EEEE4A5-2F30-40DA-8AF0-C3D479BEBD73}

Specifies the listener host name or IP address for the source container database (CDB).

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_HOST = *`listener_host`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs<br>Not required for migrations to Autonomous Database.  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PORT {#GUID-3F8CF986-E46E-4802-BCEB-03395F0A5666}

Specifies the listener port number for the source container database (CDB).

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PORT = *`listener_port`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs<br>Not required for migrations to Autonomous Database.  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL {#GUID-C4AB9751-039E-478E-91FC-1ECCC81008F0}

Specifies the proxy protocol for the CDB.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_*` parameters specify connection details for the source CDB root through an HTTP proxy. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL = *``*`  
**Default value** |  There is no default value. Following values are supported: <br>* `HTTP`<br>`HTTPS`**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME {#GUID-D7EFA335-E7EF-41EF-8A32-D4148947E922}

Specifies the HTTPS proxy host name for the CDB.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_*` parameters specify connection details for the source CDB root through an HTTP proxy. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME = *`proxy_hostname`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT {#GUID-CDCAD9EB-10EC-4681-9276-77EBC5B3F1ED}

Specifies the HTTPS proxy host port number for the CDB.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_*` parameters specify connection details for the source CDB root through an HTTP proxy. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT = *`proxy_port`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_SERVICENAME {#GUID-80DAFD7C-A6B7-4D94-BD3A-C752F249BA3C}

Specifies the fully qualified source CDB service name.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_*` parameters specify connection details for the source database CDB root. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_SERVICENAME = *`service_name`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs<br>Not required for Autonomous Database  
**Modifiable on Resume** |  No  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_TLSDETAILS_CREDENTIALSLOCATION {#GUID-D863EEAE-F2E1-4715-B1D0-2E0E0E372F09}

Specifies the directory containing client credentials (wallet, keystore, trustfile, etc.) for the CDB.

Parameter Relationships

> **note:** This parameter is not required for Autonomous Database. 

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_TLSDETAILS_*` parameters specify details for TLS connection to the database CDB. These settings are not required if you are using TCP. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_TLSDETAILS_CREDENTIALSLOCATION = *`directory`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs<br>Not required for Autonomous Database<br>Not required if you are using TCP  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME {#GUID-9F6078B0-BF0B-4157-A059-EA69BE4CFA35}

Specifies the distinguished name (DN) of the database server (`SSL_SERVER_CERT_DN`). 

Parameter Relationships

> **note:** This parameter is not required for Autonomous Database. 

For online logical migrations, the `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source CDB root. 

The `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_TLSDETAILS_*` parameters specify details for TLS connection to the CDB. These settings are not required if you are using TCP. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME = *`distinguished_name`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs<br>Not required for Autonomous Database<br>Not required if you are using TCP  
**Modifiable on Resume** |  Yes  

### SOURCECONTAINERDATABASE_GGADMINUSERNAME {#GUID-29AA8AF3-CF30-4E75-86F0-F84ED4818683}

Specifies the source CDB GoldenGate administrator user name.

Parameter Relationships

For online logical migrations, the `SOURCECONTAINERDATABASE_*` parameters specify connection details for the source database CDB root. 

Property | Description  
---|---  
**Syntax** |  `SOURCECONTAINERDATABASE_GGADMINUSERNAME = *`GoldenGate administrator username`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_ADMINUSERNAME {#GUID-31E82B15-5159-4204-B2DC-B86A05F7491A}

Specifies the source database administrator user name.

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_ADMINUSERNAME = *`source administrator username`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_ALLOWTEMPTABLE {#GUID-8636A6BB-CCCC-4A8B-880E-1A60120272A5}

Indicates whether a new table should be created to store the unsupported table names of Oracle GoldenGate.

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

ZDM fetches the list of unsupported table names of Oracle GoldenGate from the metadata tables and stores them separately in a temporary table. Setting this parameter to `TRUE` allows ZDM to create a new `TEMP_ZDM_` table in the source database. ZDM stores all the Oracle GoldenGate unsupported table names in this table. Once reload is completed, this table is dropped as part of the `ZDM_CLEANUP_SRC` phase. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_ALLOWTEMPTABLE= {TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Required** |  No  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE {#GUID-9C8FA825-1076-41E0-B70D-F5B7AD1BF1BA}

Specifies the identity file to access the bastion for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE = *`identity file`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP {#GUID-0153D3C8-7C81-43A9-B41F-49757F7B413A}

Specifies the IP address of the bastion host for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP = *`ip address`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT {#GUID-EDE7DE47-8D89-4F78-BFEE-B5E16D82A89A}

Specifies the port number of the bastion host for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT = *`port_number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP {#GUID-B8FFA4D6-F4F1-40A8-A8CD-98CCD912B08B}

Specifies the remote host IP address to access from the bastion for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP = *`ip_address`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME {#GUID-7CDE48AD-CF95-4EDD-A456-D46FE1F531C7}

Specifies the user name to access the bastion for bastion-based access to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME = *`username`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_HOST {#GUID-FD985443-EBEF-4529-B4E8-F9000D657948}

Specifies the source database listener host name or IP address. 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_HOST = *`hostname_or_ip`*`  
**Default value** |  There is no default value  
**Required** |  Yes*<br>*Not required for Autonomous Database.  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PORT {#GUID-871E1FB8-E0BE-4B79-8259-27C2424CF633}

Specifies the source database listener port number. 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PORT = *`listener port number`*`  
**Default value** |  There is no default value  
**Required** |  Yes*<br>*Not required for Autonomous Database.  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME {#GUID-24AF215D-5BD2-4E88-97DE-1BB9652C083D}

Specifies the proxy host name to connect to the source database through an HTTPS proxy.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME = *`proxy host name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT {#GUID-D3BED84E-8B55-43EA-9AE3-B982EFD9C1F9}

Specifies the HTTP proxy port number to connect to the source database through an HTTPS proxy.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT = *`proxy port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL {#GUID-372FE3FB-FD40-45B8-A4B6-EDCAE24051A4}

Specifies the proxy protocol to connect to the source database through a proxy.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL = HTTP or HTTPS`  
**Default value** |  `HTTP`  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME {#GUID-AA087D91-306B-484D-9EE5-8739D59D9C97}

Specifies the source database fully qualified service name.

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_SERVICENAME = *`service name`*`  
**Default value** |  There is no default value  
**Required** |  Yes*<br>*Not required for Autonomous Database  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_CREDENTIALSLOCATION {#GUID-B6138D78-1A5C-4E37-878A-FAED97E5F8F8}

Specifies the directory containing client credentials (wallet, keystore, trustfile, etc.) for a TLS connection to the database.

Parameter Relationships

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_CREDENTIALSLOCATION = *`directory`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs<br>Not required if using TCP<br>Not required for Autonomous Database  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME {#GUID-9F7EEF4D-2FF7-4B22-8A35-C13E0E9AAC7C}

Specifies the distinguished name (DN) of the database server (`SSL_SERVER_CERT_DN`) for a TLS connection to the database. 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

The `SOURCEDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME = *`distinguished_name`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migration jobs<br>Not required if using TCP<br>Not required for Autonomous Database  
**Modifiable on Resume** |  Yes  

### SOURCEDATABASE_ENVIRONMENT_DBTYPE {#GUID-9F01629D-76BF-42E4-A0F5-84BF811154FE}

Indicates the type of database to migrate from the specified environment.

If you specify that the source database environment is Amazon Web Services RDS by setting `SOURCEDATABASE_ENVIRONMENT_NAME=AMAZON`, then you must also set `SOURCEDATABASE_ENVIRONMENT_DBTYPE=RDS_ORACLE`. 

See [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA). 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_ENVIRONMENT_DBTYPE = RDS_ORACLE`  
**Default value** |  `RDS_ORACLE`  
**Required** |  No  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_ENVIRONMENT_NAME {#GUID-A8504F26-2CBA-47A2-84AC-94C6A91ECB7A}

Specifies the environment of the source database.

You can specify whether the source database environment is Oracle (`ORACLE`) or Amazon Web Services RDS (`AMAZON`). 

If you set `SOURCEDATABASE_ENVIRONMENT_NAME=AMAZON` you must also set `SOURCEDATABASE_ENVIRONMENT_DBTYPE=RDS_ORACLE`. See [Migrating from Amazon Web Services RDS to Oracle Autonomous Database](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5B3396BE-BEC6-488E-A205-192776FAC4FA). 

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_ENVIRONMENT_NAME = [ORACLE|AMAZON]`  
**Default value** |  `ORACLE`  
**Required** |  No  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_GGADMINUSERNAME {#GUID-1D5E0EF4-19A0-41E1-9A6D-23BEAC2BE5B1}

Specifies the GoldenGate administrator user name for online logical migrations.

Parameter Relationships

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_GGADMINUSERNAME = *`gg_admin_username`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  No  

### SOURCEDATABASE_OCID {#GUID-8EF79584-99E2-47B7-9B67-B8968E6021CD}

Specifies the OCID of the source Autonomous Database.

Parameter Relationships

> **note:** Using the `SOURCEDATABASE_OCID` parameter, you can migrate from an Autonomous database to an Autonomous database within same tenancy and region. However, to migrate an Autonomous database to an Autonomous database across tenancy and regions use the SOURCEDATABASE_CONNECTIONDETAILS_* parameters. Also, ensure to provide the OCIAUTHENTICATIONDETAILS_* of target region or tenancy. For logical online migration, ensure to provision GoldenGate hub in the target region or tenancy. 

The `SOURCEDATABASE_*` parameters specify connection details for the source database. 

Property | Description  
---|---  
**Syntax** |  `SOURCEDATABASE_OCID = *`ocid of source adb`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  No  

### TABLESPACEDETAILS_AUTOCREATE {#GUID-04BDA35D-4BC6-423D-A6E6-8CBDC659D531}

Specifies whether Zero Downtime Migration automatically creates DATA, TEMPORARY, and UNDO tablespaces at the target database necessary to allocate space in the database to contain schema objects.

This option is prohibited for Autonomous Database Shared targets.

See [Automatic Tablespace Creation](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C23B404A-9C3A-48A3-A265-D01E6774914F) for more information. 

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_AUTOCREATE={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Zero Downtime Migration automatically creates tablespaces <br>`FALSE` \- Zero Downtime Migration does not create tablespaces   
**Required** |  No<br>This option is prohibited for Autonomous Database Shared targets.  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  

### TABLESPACEDETAILS_AUTOREMAP {#GUID-723AAB7F-CFB3-4D5A-B95A-79549603FF53}

Specifies whether Zero Downtime Migration automatically remaps a tablespace at the target database.

See [Automatic Tablespace Remap](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-96E7666B-433F-4E83-BC0C-BE5B6E3FB9E2) for more information. 

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_AUTOREMAP={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Zero Downtime Migration automatically remaps tablespaces <br>`FALSE` \- Zero Downtime Migration does not remap tablespaces   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  

### TABLESPACEDETAILS_EXCLUDE {#GUID-65B5BCE8-536F-4B20-B045-D9D9457370F1}

Specifies tablespaces to be excluded from automatic creation at the target database.

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

### TABLESPACEDETAILS_EXTENDSIZEMB {#GUID-7E04079D-A945-48FE-95B0-1291CB87F058}

Specifies an extend size for `AUTOEXTEND` in support of automatic tablespace creation. 

Properly setting `TABLESPACEDETAILS_EXTENDSIZEMB` enables `AUTOEXTEND` to avoid extend errors when automatic tablespace creation is enabled. 

See [Automatic Tablespace Creation](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C23B404A-9C3A-48A3-A265-D01E6774914F) for more information. 

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_EXTENDSIZEMB=*`MB`*`  
**Default value** |  `512`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  

### TABLESPACEDETAILS_REMAPTARGET {#GUID-2BA9FBB7-DC92-4FA6-8D5D-32E8E04FB033}

Specifies tablespaces to be remapped.

For a tablespace to be used as REMAP target, the user performing the import operation, for example `SYSTEM`, should have some quota on the chosen tablespace. 

See [Automatic Tablespace Remap](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-96E7666B-433F-4E83-BC0C-BE5B6E3FB9E2) for more information. 

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_REMAPTARGET=*`target_tablespace_names`*`  
**Default value** |  By default the DATA tablespace is remapped.  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED`  

### TABLESPACEDETAILS_REMAPTEMPTARGET {#GUID-6F70DE8C-CCA0-4A67-8B39-72487F03B007}

Specifies the TEMP tablespace at the target database to which the temporary tablespace of the source database needs to be remapped. 

Name of `TEMP` tablespace at target to which the source database temporary tablespace need to be remapped. Setting `TABLESPACEDETAILS_AUTOREMAP` to `TRUE` and setting this parameter enables remap of temporary tablespaces. 

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_REMAPTEMPTARGET={TRUE \| FALSE}`  
**Default value** | * Remap by default to temporary tablespace TEMP for migrating to Autonomous Database.<br>No default for migrating to non Autonomous targets
**Range of values** |  `TRUE` \- Zero Downtime Migration automatically remaps TEMP tablespaces. <br>`FALSE` \- Zero Downtime Migration does not remap TEMP tablespaces.   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_DATAPUMP_IMPORT_TGT` phase is `COMPLETED` .   

### TABLESPACEDETAILS_USEBIGFILE {#GUID-AF05E79B-BA9A-47A1-BA5D-FCCF5E5FF90A}

Specifies whether to use bigfile tablespaces, if Zero Downtime Migration is configured to create tablespaces automatically.

Using bigfile tablespaces, which can be up to 128 TB, significantly reduces the number of data files for your database. Combined with Oracle Managed Files (OMF), bigfile tablespaces simplify data file management.

See [Automatic Tablespace Creation](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-C23B404A-9C3A-48A3-A265-D01E6774914F) for more information. 

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_USEBIGFILE={TRUE \| FALSE}`  
**Default value** |  `FALSE`  
**Range of values** |  `TRUE` \- Zero Downtime Migration automatically creates bigfile tablespaces <br>`FALSE` \- Zero Downtime Migration does not create bigfile tablespaces   
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  

### TABLESPACEDETAILS_BLOCKSIZE {#GUID-7CBA071F-DEAF-43E8-905C-316B311FDCC1}

Specifies the size of Oracle database blocks in kilobytes (KB).

Parameter Relationships

In a logical migration, the `TABLESPACEDETAILS_*` parameters specify details that allow Zero Downtime Migration to automatically create the required tablespaces at target database. 

> **note:** 

Ensure that the target database has `db_block_size` set to `16k` or set `db_16k_cache_size` if `db_block_size` is `8k`. See [DB_nK_CACHE_SIZE](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DB_nK_CACHE_SIZE.md) for the appropriate value to be specified for `db_16k_cache_size`. 

Property | Description  
---|---  
**Syntax** |  `TABLESPACEDETAILS_BLOCKSIZE= 12 or 32`  
**Default value** |  `32`  
**Range of values** |  `8`<br>`12`<br>`16` <br>`32`  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`  

### TARGETDATABASE_ADMINUSERNAME {#GUID-D636F9F6-4015-4FEE-BAC9-A61BC627A4DC}

Specifies the target database administrator user name.

Parameter Relationships

The `TARGETDATABASE_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_ADMINUSERNAME = *`username`*`  
**Default value** |  There is no default value  
**Required** |  Yes  
**Modifiable on Resume** |  No  

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE {#GUID-770A7460-6D20-43B4-B996-D754C5412C42}

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

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP {#GUID-12519682-186C-4B2D-9098-37B3BCABD49A}

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

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT {#GUID-EDCB2DD0-BF02-446C-9C91-F0F5D358EC4F}

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

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP {#GUID-3EF1EF47-8594-4972-9A33-783FB85EA2BE}

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

### TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME {#GUID-8227F624-BB6D-44B8-80B5-D4AE3890B3A8}

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

### TARGETDATABASE_CONNECTIONDETAILS_HOST {#GUID-46961DAB-87C1-4D16-B8EB-A9F4026AED90}

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

### TARGETDATABASE_CONNECTIONDETAILS_PORT {#GUID-D56505AE-DB5C-4AF0-A0EE-8D02EFA215C0}

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

### TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME {#GUID-125C21C9-6CB8-4925-A1D4-4EB8F96B071E}

Specifies the proxy host name for connecting to the target database through an HTTPS proxy.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_HOSTNAME = *`proxy host name`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT {#GUID-32B61E07-994E-4BAE-A956-70EA6377C5A6}

Specifies the HTTP proxy port number for connecting to the source database through an HTTPS proxy.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PORT = *`proxy port number`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL {#GUID-BEA27804-A392-441D-8DF1-2344A5FB4373}

Specifies the proxy protocol to connect to the target database through a proxy.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_PROXYDETAILS_PROTOCOL = HTTP or HTTPS`  
**Default value** |  `HTTP`  
**Required** |  No  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_SERVICENAME {#GUID-0AB8D4F0-3DA6-4382-8C77-9027E5A5C947}

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
**Valid values** |  For **non-Autonomous**, specify the fully qualified service name. <br>For **Autonomous**, you can specify the service alias for fractional OCPU service as: 

* `TP_TLS`, `TP`, `LOW_TLS` or `LOW` (for Autonomous Transaction Processing workloads) <br>`LOW_TLS` or `LOW` (for Autonomous Data Warehouse workloads) 


**Required** |  No  
**Modifiable on Resume** |  No  

### TARGETDATABASE_CONNECTIONDETAILS_TLSDETAILS_CREDENTIALSLOCATION {#GUID-8E519CD9-853B-40B2-AD87-AE7746AA3EEF}

Specifies the directory containing client credentials (wallet, keystore, trustfile, etc.) for a TLS connection.

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_TLSDETAILS_CREDENTIALSLOCATION = *`directory`*`  
**Default value** |  There is no default value  
**Required** |  Not mandatory for migrations<br>Not required if using TCP  
**Modifiable on Resume** |  Yes  

### TARGETDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME {#GUID-0C0FADD5-550F-4F4F-86F4-7771F5F6D9A5}

Specifies the distinguished name (DN) of the database server (`SSL_SERVER_CERT_DN`) for a TLS connection. 

Parameter Relationships

The `TARGETDATABASE_CONNECTIONDETAILS_*` parameters specify connection details for the target OCI database. 

These are optional for Autonomous Database; however if an HTTP proxy is required to connect, specify them.

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_CONNECTIONDETAILS_TLSDETAILS_DISTINGUISHEDNAME = *`distinguished_name`*`  
**Default value** |  There is no default value  
**Required** |  Required if the DB connection is over TLS.<br>**Modifiable on Resume** |  Yes  

### TARGETDATABASE_GGADMINUSERNAME {#GUID-0A6D291D-811A-4997-803B-51939AEEF16A}

Specifies the GoldenGate administrator user name for online logical migrations.

Parameter Relationships

The `TARGETDATABASE_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_GGADMINUSERNAME = *`gg_admin_username`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  No  

### TARGETDATABASE_OCID {#GUID-45C25BCD-438C-4A15-8326-1B22CCE38450}

Specifies the Oracle Cloud resource identifier.

Parameter Relationships

The `TARGETDATABASE_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_OCID = *`ocid`*`  
**Default value** |  There is no default value  
**Required** |  Yes<br>This is an optional parameter if `TARGETDATABASE_DBTYPE` is set to valid a value.   
**Modifiable on Resume** |  No  

### TARGETDATABASE_DBTYPE {#GUID-C97C3B81-02E0-4377-9323-E64A0B760844}

Specifies the target database type if OCID of the target database is not provided.

Parameter Relationships

You can migrate database to Oracle Autonomous Database on Exadata Cloud@Customer without needing an OCID if value of `TARGETDATABASE_DBTYPE = ADBCC`. 

The `TARGETDATABASE_*` parameters specify connection details for the target OCI database. 

Property | Description  
---|---  
**Syntax** |  `TARGETDATABASE_DBTYPE = *`dbtypeValue`*`  
**Default value** |  There is no default value The following values are supported: 

* `ORACLE`<br>`ADBD`<br>`ADBS`<br>`ADBCC`<br>`EXADATA`


**Required** |  No  
**Modifiable on Resume** |  No  

### WALLET_USERACTION {#GUID-38FD145F-B908-48A8-ADDA-2A92DADFB3E3}

Allows you to provide the auto login wallet path, which contains the username and its password.

> **note:** This parameter is supported only for logical migration. 

The path should be resolvable from the Zero Downtime Migration service host. 

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_USERACTION = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_AMAZONS3SECRET {#GUID-A3332E65-40AC-4851-96F2-D5B55A96B156}

Specifies the Amazon S3 Secret Key wallet path.

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_AMAZONS3SECRET = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_OCIAUTHTOKEN {#GUID-8098E64D-C466-4E6E-826E-5C2277D02D32}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the OCI Auth Token password. 

> **note:** This parameter is applicable only when the source database is Oracle Autonomous Database on Shared Exadata Infrastructure or Oracle Autonomous Database on Dedicated Exadata Infrastructure. 

The path should be resolvable from the Zero Downtime Migration service host. 

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_OCIAUTHTOKEN = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_DATAPUMPENCRYPTION {#GUID-C794FD29-CB88-4CF0-81E2-A698E37E8D38}

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

### WALLET_OGGADMIN {#GUID-28C5B8A0-B382-4043-BBDB-9C0F34928CA0}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the Oracle GoldenGate hub administrative password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_OGGADMIN = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_SOURCECONTAINER {#GUID-0E154BF7-2B40-48CB-9157-E90C291E2072}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the source database administrative user password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_SOURCECONTAINER = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_SOURCECGGADMIN {#GUID-464EB423-D8D4-4E28-978F-FD98B9B2F867}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the source database administrative `c##ggadmin` user password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_SOURCECGGADMIN = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_SOURCEGGADMIN {#GUID-7D3B6B7F-6B41-4D8F-80EB-82D9D2EF2EE3}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the source database administrative user `ggadmin` password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_SOURCEGGADMIN = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_TARGETADMIN {#GUID-08E166D0-56CE-4FA8-96E4-6658B7A00DE2}

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

### WALLET_TARGETGGADMIN {#GUID-93A493C5-8D35-475A-9E11-68564AE28534}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, which is used to get the target database administrative user `ggadmin` password. 

The path should be resolvable from the Zero Downtime Migration service host.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_TARGETGGADMIN = *`wallet_path`*`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`.   

### WALLET_OGGSERVICEADMIN {#GUID-CA204B83-4203-4045-A905-B3C11A5C18E6}

Specifies the absolute path to the directory that contains the auto login wallet file `cwallet.sso`, for specifying the Oracle Cloud Infrastructure GoldenGate administrative password. 

The path should be accessible from the Zero Downtime Migration node.

About the WALLET_* Parameters

The `WALLET_*` parameters specify the full path for the auto login wallet file on the Zero Downtime Migration service host. 

Property | Description  
---|---  
**Syntax** |  `WALLET_OGGSERVICEADMIN = *``*Absolute path to wallet on ZDM host`  
**Default value** |  There is no default value  
**Required** |  No  
**Modifiable on Resume** |  Until `ZDM_PREPARE_DATAPUMP_SRC` phase is `COMPLETED`. 
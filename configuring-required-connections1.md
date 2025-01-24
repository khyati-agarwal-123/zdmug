## 3Configuring Required Connections {#GUID-F15ED0C0-19C7-4E1B-9194-04144E07C1D3}

Connectivity must be set up between the Zero Downtime Migration service host and the source and target database servers.

> **note:** 
The umask setting for host level authenticating user must have umask set to 022. For example, if `zdmuser` is authenticating user for the source or target, then `zdmuser` must have umask set to 022. 

### Logical Migration without SSH Access {#GUID-EAF1DF4C-3F7D-4279-8C81-8E6177FDF096}

You can perform logical migration to the target database server host without requiring SSH credentials.

* ZDM performs the logical workflow without SSH access where it handles the upload and download of dumps.
* To have Zero Downtime Migration run Cloud Premigration Advisor Tool (CPAT) validation remotely, set the response file parameter `RUNCPATREMOTELY=TRUE`. See [RUNCPATREMOTELY](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-85FB677B-4BD0-4EBE-BE53-10DD48742D57). 
* ZDM allows CPAT to be started from the ZDM node (referred to above as the ZDM service host) remotely via a JDBC connection to the source or target database. 



For migration involving transfer of dumps to OSS, ZDM auto transfers dumps over HTTPS.

To achieve this HTTPS connectivity, you must perform the following steps in the source database host:

* Set up `SSL_WALLET` for HTTPS access: see [Create SSL Wallet with Certificates](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=280456498617551&parent=EXTERNAL_SEARCH&sourceId=HOWTO&_adf.ctr=&id=2748362.1&_afrWindowMode=0&_adf.ctrl-state=2qc622q2m_53). 
* Specify this wallet path to the ZDM job using the `DUMPTRANSFERDETAILS_SOURCE_OCIWALLETLOC` parameter. 
* Set up Network ACL: The user performing the export or import requires the necessary network ACL to be granted to access the network from the source and target database host. See [How To Set Network ACLs](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=280054546270978&parent=EXTERNAL_SEARCH&sourceId=HOWTO&id=958129.1&_afrWindowMode=0&_adf.ctrl-state=2qc622q2m_4)



For source database servers: 

The following parameters indicate the SSL wallet path on source node:

* `DUMPTRANSFERDETAILS_SOURCE_NOSSHUPLOADMETHOD`
* `DUMPTRANSFERDETAILS_SOURCE_OCIWALLETLOC`



For target database servers:

The following parameters indicate the SSL wallet path on target node: 

* `DUMPTRANSFERDETAILS_TARGET_NOSSHUPLOADMETHOD`
* `DUMPTRANSFERDETAILS_TARGET_OCIWALLETLOC`



**Related Topics**

* [DUMPTRANSFERDETAILS_SOURCE_NOSSHUPLOADMETHOD](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-F25EBCFF-5E6F-4102-B8BA-B846F504655F)
* [DUMPTRANSFERDETAILS_SOURCE_OCIWALLETLOC](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-83F78CA2-8251-40BB-A26E-73FFFA73CD39)
* [DUMPTRANSFERDETAILS_TARGET_NOSSHUPLOADMETHOD](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-8A51F249-6200-40F1-A504-8A77772AA004)
* [DUMPTRANSFERDETAILS_TARGET_OCIWALLETLOC](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-5C181F57-8F62-4740-A2BC-D6008A1C546E)



### Physical and Logical Migration with SSH Access {#GUID-A26E8B42-455D-4AD2-8E62-6294DD607C90}

You can perform physical and logical migration and migrate a database from source to target with SSH access with `database` user and `sudo` privileged user. 

**Related Topics**

* [Setting Up SSH Key Based Authentication](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-06286CA9-C5DE-4C85-9AD9-245C4C2B0F24)
* [Generate SSH Keys Without a Passphrase](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-602216AD-4B59-441B-BA8D-AC7B333038FD)



#### Migrating with Database User Privileges {#GUID-C03E5EBB-CA6D-4FDA-83D8-0591158E7068}

You can migrate database from source to target with all actions performed on the source and target database node as `oracle` user. You do not require a privileged user access to perform any action. 

**Using dbuser authentication plug-in: **

You can specify the authentication user and identity file for the specified user to connect to source database server and perform migration. 

> **note:** To perform the logical migration using database user plug-in as `dbuser`, you must set value of the `[RUNCPATREMOTELY](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-85FB677B-4BD0-4EBE-BE53-10DD48742D57)` parameter to `TRUE`. 

You require the following arguments:

* `user`: *`Oracle db user`* \- Specifies the Oracle Database user name that is allowed to perform the migration action. For example, `user:oracle` is capable of performing any Grid Infrastructure (GI) actions required as part of the physical migration workflow. 
* `identity_file: *`Oracle db user SSH private key file`*`\- Specifies the private key file for the specified Oracle Database user name that allows SSH connection for the specified source or target node. For example, `identity_file:/home/zdmnode/keys/source_ssh_private_key.file`. 



1. Choose the authentication plug-in `-srcauth` or `-tgtauth` as `dbuser` and specify the authentication user. 
2. Identity the file for the specified user to connect to source or target database server respectively and perform migration.
3. Use the following arguments in `migrate` command to support `dbuser` authentication plug-in: 
    * Source node access: Specify following parameters in ZDMCLI `migrate` command:
    ```
    -srcauth dbuser
    -srcarg1 user:username
    -srcarg2 identity_file:Oracle user private_ssh_key.file
    ```
    
    
    * Target node access: Specify following parameters in ZDMCLI `migrate` command:
    ```
    -tgtauth dbuser
    -tgtarg1 user:target_database_server_login_user_name
    -tgtarg2 identity_file:Oracle user private_ssh_key.file
    ```





**Using dbuser plug-in for performing logical migration **

ZDM supports non-privileged users for both source and target node access.

You must allow `dbuser` specified for authentication to perform all action required for migration in the source and target database server hosts. 

** Using dbuser plug-in for performing physical migration **

**Restrictions for physical migration**: Physical migration is supported for accessing the source database server and conditionally for accessing the target node, detailed as follows: 

* You must allow `dbuser` specified for authentication to perform all actions required for migration in the source database server host. 
* For OCI target database servers- `dbuser` authentication plug-in is not supported and always requires a privileged user. You must use `zdmauth`. 
* For non-cloud target database server, to use `dbuser` for target database in physical migration, the target database must be non-role separated where Oracle Grid Infrastructure (GI) and Oracle Database (DB) must be owned by the same user. 
* For source database servers, ensure that either of the following cases are applicable if Grid Infrastructure user and Database user have no role separation. The following actions are not required if `dbuser` and `grid user` are same and if Oracle Database password or TDE wallet is not in ASM: 
    * If Grid Infrastructure and Database are role separated and if the Oracle Database password or TDE wallet is on ASM, then copy the password or TDE wallet from ASM to a secure local file system path for `dbuser` access. 
    * Ensure that the copied wallet has protected and restricted access only for `dbuser` and the wallet is removed after the completion of migration action. 
    * If you copy a TDE wallet or Oracle Database from ASM to local file system, then specify the path in the `SRC_DB_PASSWORDFILE_LOC[SRC_DB_PASSWORDFILE_LOC](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-9DE9D334-CF74-45B8-8FAE-52F7CFB3D916)` and `SRC_DB_TDE_WALLET_LOC[SRC_DB_TDE_WALLET_LOC](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-79FA4FC4-A0E4-4154-9781-1AA041A40FBD)` parameters respectively. 



**Sample migrate command**: 
```
$ZDM_HOME/bin/zdmcli migrate database -rsp file_name  -sourcesid source_oracle_sid
-sourcenode source_database_server_name
-srcauth dbuser
-srcarg1 user:username
-srcarg2 identity_file:ssh_key_path
-targetnode target_database_server_name
-tgtauth dbuser
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
```


#### Migrating with SUDO User Privilege {#GUID-3DC19DF1-422C-47BD-9632-D14F82107809}

You may need to grant certain users authority to perform operations using `sudo` on the source and target database servers. 

You can migrate the database from source to target with all actions on the source and target database node as `sudo` user without the SUPERUSER privilege elevation. The indicated user in the authentication argument `user:` is used to connect to the database host over SSH. None of the actions performed expects SU (super user) access or direct SSH via database user. 

> **note:** This is an optional method and can be used as an alternative to the [Setting Up SSH Key Based Authentication](setting-ssh-key-based-authentication.md#GUID-06286CA9-C5DE-4C85-9AD9-245C4C2B0F24) method which is applicable for role-separated cases. 
These steps are applicable for physical migrations and logical migrations where either the source and/or target are accessed using SSH keys (co-managed databases). 
These steps are **not applicable** for logical migration where the target is Autonomous Database. 

> **note:** The new authentication plugin `sudoasuser` is available only for logical migration. 
It caters to your environments with SSH access to Database user being locked as well as when the Sudo user is not granted privilege to become Super User.

For source database servers:

* If the source database server is accessed with the `root` user, then there is no need to configure`sudo` privileges. 

* If the source database server is accessed through non-`root`, non-`database` user, then configure `sudo` privilege for the user to perform migration action as `database` user without prompting for a password. 




For target database servers:

* When the target database server is on the cloud, any `sudo` privileges are already configured. Otherwise, configure all `sudo` privileges to run without prompting for a password for the database installed user and the `root` user. 

If the target database server is accessed through non-`root`, non-`database` user, then configure `sudo` privilege for the user to perform migration action as `database` user without prompting for a password. 




**Using sudoasuser authentication plugin for logical migration**

You can migrate the database from source to target with all actions on the source and target database node as `sudo` user without the SUPERUSER privilege elevation. The indicated user in the authentication argument `user:` is used to connect to the database host over SSH. None of the actions performed expects SU (super user) access or direct SSH via database user. 

**Authplugin : 'sudoasuser'**

The plugin offers arguments for you to specify the authentication user, asuser and identity file for the specified user to connect to source database server and perform migrate action using sudo to become database user.
Following are the arguments required 
`user: `
Argument specifies the auth user name with which the connection is made to the remote source node.


**'asuser:' **


Argument specifies the sudo user name that is allowed to perform the migration action with performing sudo to Oracle Database user. For example, `user:zdmuser` can do `sudo oracle` (and become Grid User for Physical migration.) For physical migration, the specified user should be capable of performing any Grid Infrastructure (GI) actions required as part of the migration workflow with sudo as GI user. 

**'identity_file:' **

Argument specifies the private key file for the specified auth user name that will allow SSH connection for the specified source node. For example, `identity_file:/home/zdmnode/keys/source_ssh_private_key.file`. 

Following is the sample `migrate` command:
```
zdmcli migrate database
-sourcedb db_name
-sourcenode source_host_name
-srcauth sudoasuser
-srcarg1 user:source_database_server_login_user_name
-srcarg2 asuser:source_database_server_login_user_name
-srcarg3 identity_file:ZDM_installed_user_private_key_file_location
-srcarg4 sudo_location:/usr/bin/sudo
-targetnode target_host_name
-backupuser Object_store_login_user_name
-rsp response_file_location
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
-tgtarg3 sudo_location:/usr/bin/sudo -tdekeystorepasswd -tgttdekeystorepasswd
```


The following example contains a sample command for host access via `certs` using authentication plug-in. 

> **note:** Use `-srcauth zdmauth` for user with sudo access and `-tgtauth dbuser` for non-sudo user or vice versa. 
```
$ZDM_HOME/bin/zdmcli migrate database
-sourcedb source_db_unique_name_value
-rsp response_file_location
-sourcenode
-srcauth zdmauth
-srcarg1  user:username
-srcarg2 identity_file:
-srcarg3 sudo_location:/usr/bin/sudo
-targetnode target_database_server_name
-tgtauth dbuser
-tgtarg1  user:target_database_server_login_user_name
-tgtarg2 identity_file:ZDM_installed_user_private_key_file_location
```


### Zero Downtime Migration Port Requirements {#GUID-E6F8EF13-03A5-43DD-8F98-182632C83CB3}

The ports required for communication between the Zero Downtime Migration service host, the source and target database servers, and Oracle Cloud Object Store Service are described in the following table.

**Table: Zero Downtime Migration Communication Ports**

**Initiator** | **Target** | **Protocol** | **Port** | **Purpose** | **Description**  
---|---|---|---|---|---  
Zero Downtime Migration service host | Source and target database servers | TCP | 22 | SSH | Authentication-based operations to run Zero Downtime Migration operational phases Source and target database servers should accept incoming connections from the Zero Downtime Migration service host.<br>Not applicable to Autonomous Database targets
Zero Downtime Migration service host | Source and target database servers | TCP | 1521, 2484, or a database SCAN Listener port  | SQL*Net | For logical migrations  
Zero Downtime Migration service host or GoldenGate Hub | Target ADB | TCPS | 1522, or a database SCAN Listener secure port in case of TCPS enabled on source or target | SQL*Net | ADB-S uses port 1522 so you should allow incoming connections on the ADB tenancy from GoldeGate or ZDM, or from the target if the source listens on 1522, and transfer medium is DBLINK over TCPS  
Zero Downtime Migration service host | Oracle Cloud Interface REST endpoint | SSL | 443 | OCI REST endpoint | Target discovery for logical migrations. **Note**: Ensure that you consider all the necessary proxy details and provide the details in the ZDM response file.   
Source database servers | Target database servers | TCP | 1521 or database SCAN Listener port applicable for the job  | SQL*Net | Should allow Oracle client connections to the database over Oracle's SQL*Net protocol Perform database queries, Data Guard sync, and configuration<br>**Note:** If you are using a non-default port number (that is, something other than port 1521) for the local listener address, then connections should be allowed to the non-default port.   
Target database servers | Source database servers | TCP | 1521 or a database SCAN Listener port applicable | SQL*Net | Should allow Oracle client connections to the database over Oracle's SQL*Net protocol Allows redo log shipping if source database needs to be in sync with the new primary on Oracle Cloud after switchover. If there is no communication possible from Oracle Cloud to source database server then set `SKIP_FALLBACK` to `TRUE` in the response file to avoid this communication. <br>**Note:** If you are using a non-default port number (that is, something other than port 1521) for the local listener address, then connections should be allowed to the non-default port.   
Source database servers | Oracle Cloud Object Store Service | SSL | 443 | Database backup store. | If the chosen backup method uses Oracle Cloud Object Store Service as the backup medium, then access ports as documented Oracle Cloud Object Store Service applies. **Note**: Ensure that you consider all the necessary proxy details and provide the details in the ZDM response file.   
Target database servers | Oracle Cloud Object Store Service | SSL | 443 | Database backup store.  | If the chosen backup method uses Oracle Cloud Object Store Service as the backup medium, then access ports as documented Oracle Cloud Object Store Service apply. **Note**: Ensure that you consider all the necessary proxy details and provide the details in the ZDM response file.   

> **note:** If there is no SSH access to the source database, the following action is skipped as part of the Zero Downtime Migration work flow. 
        * Zero Downtime Migration will not validate whether the source export path is writable for the database user.



> **note:** When performing a migration with root credentials (`migrate database -scroot`), during the setup phase, Zero Downtime Migration uses six ports chosen from the ephemeral range, or six ports from the range of ports set in `TRANSFERPORT_RANGE` in `*`zdmbase`*/crsdata/*`zdm_service_host`*/rhp/conf/rhp.pref`. The specified ports must be allowed to accept incoming connections from the Zero Downtime Migration service host on the source or target database server. 

### Configuring Database Connectivity for Online Physical Migration {#GUID-EB28C600-BCEB-4381-9990-505A2F32293E}

For physical migration, SQL*NET should be available between the source and target database. You have two options for configuring connectivity between the source and target database servers: SQL*Net connectivity using SCAN or SSH. Optionally, you can configure the connectivity between the target and source database.

> **note:** Configuring connectivity between the source and target database servers is applicable only for online physical migrations. 

#### SQL*Net Connectivity Using SCAN {#GUID-D8D3BC1A-DDB2-43A7-BE30-4B8AFBF5697C}

* To use this option, ensure that the source can connect to the target through the SCAN of the target database and vice versa.
* Connect to the target database instance over target SCAN through the respective SCAN port and vice versa, using the source database server you specify in the `ZDMCLI migrate database` command `-sourcenode` parameter. 
* With SCAN connectivity from both sides, the source database and target databases can synchronize from either direction. If the source database server SCAN cannot be resolved from the target database server, then the `SKIP_FALLBACK` parameter in the response file must be set to `TRUE`, and the target database and source database cannot synchronize after switchover. 



**Test Connectivity**

To test connectivity from the source to the target environment, add a TNS Alias entry in `tnsnames.ora` file that maps to a database connect descriptor. See, [Local Naming Parameters in the tnsnames.ora File](https://docs.oracle.com/en/database/oracle/oracle-database/21/netrf/local-naming-parameters-in-tns-ora-file.md) . 
```
[oracle@sourcedb ~] tnsping tnsping
```


To test connectivity from the target to the source environment, a TNS Alias entry in `tnsnames.ora` file that maps to a database connect descriptor. 
```
[oracle@targetdb ~] tnsping tnsping
```


> **note:** Database migration to Oracle Exadata Database Service on Cloud@Customer using the Zero Data Loss Recovery Appliance requires mandatory SQL*Net connectivity from the target database server to ZDLRA. 

> **note:** See Also:[Zero Downtime Migration Port Requirements](configuring-required-connections1.md#GUID-E6F8EF13-03A5-43DD-8F98-182632C83CB3)
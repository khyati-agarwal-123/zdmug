## CProviding Passwords Non-Interactively Using a Wallet {#GUID-CED7A2D9-6AD9-4527-BE29-2F036B6CEDCC}

You can avoid entering passwords in the command line and run the `ZDMCLI migrate database` command without user interaction, such as when you do automation using Rundeck. 

Currently, whenever you submit the `$ZDM_HOME/bin/zdmcli migrate database` command, it prompts for the source database `SYS` password, Object Store user swift authentication token, and the source database Transparent Data Encryption (TDE) keystore password (if the wallet was configured as a `PASSWORD`-based TDE wallet). 

**Wallet Creation Examples**

The following examples show how to create auto-login wallets for the source database SYS user, the Object Store user, the source database TDE keystore, and the target CDB database TDE keystore password.

Run the following commands on the Zero Downtime Migration service host as Zero Downtime Migration software owner (for example, `zdmuser`). 

**To create an auto-login wallet for the source database SYS user:**

1. Create a directory where you want to create and store the wallet.
```
zdmuser> mkdir sys_wallet_path
```


For example: 
```
/u01/app/zdmhome> mkdir sysWallet
```


2. Create a wallet.
```
zdmuser> $ZDM_HOME/bin/orapki wallet create -wallet sys_wallet_path
-auto_login_only
```


For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/orapki wallet create -wallet sysWallet
-auto_login_only
Oracle PKI Tool Release 19.0.0.0.0 - Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.
```


3. Add a `SYS` user login credentials to wallet. 
```
zdmuser> $ZDM_HOME/bin/mkstore -wrl sys_wallet_path
-createCredential store sysuser
```


At the prompt, enter the source database `SYS` password. 

For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/mkstore -wrl ./sysWallet
-createCredential store sysuser
Oracle Secret Store Tool Release 19.0.0.0.0 - Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019, Oracle and/or its affiliates. All rights reserved.

Your secret/Password is missing in the command line
Enter your secret/Password:
Re-enter your secret/Password:
```


4. Verify that the wallet files were created.
```
zdmuser> ls -l sys_wallet_path
```


For example
```
/u01/app/zdmhome> ls -l sysWallet/
total 4
-rw-------. 1 opc opc 581 Jun  2 08:00 cwallet.sso
-rw-------. 1 opc opc   0 Jun  2 08:00 cwallet.sso.lck
```





**To create an auto-login wallet for the Object Store user:**

1. Create a directory where you want to create and store the wallet.
```
zdmuser> mkdir oss_wallet_path
```


For example
```
/u01/app/zdmhome> mkdir ossWallet
```


2. Create a wallet
```
zdmuser> $ZDM_HOME/bin/orapki wallet create -wallet oss_wallet_path
-auto_login_only
```


For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/orapki wallet create
-wallet ./ossWallet -auto_login_only
Oracle PKI Tool Release 19.0.0.0.0 -Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019,
Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.
```


3. Add the Object Store user login credentials to the wallet.
```
zdmuser> $ZDM_HOME/bin/mkstore -wrl oss_wallet_path
-createCredential store ossuser
```


For the prompt,

* If the backup destination is Object Store (Bucket), then enter the user swift authentication token. 

* If the backup destination is Storage Classic (Container), then enter your tenancy login password.

For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/mkstore -wrl ./ossWallet
-createCredential store ossuser
Oracle Secret Store Tool Release 19.0.0.0.0 - Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019, Oracle and/or its affiliates. All rights reserved.

Your secret/Password is missing in the command line
Enter your secret/Password:
Re-enter your secret/Password:
```


4. Verify that the wallet files were created.
```
zdmuser> ls -l oss_wallet_path
```


For example
```
/u01/app/zdmhome> ls -l ./ossWallet
total 4
-rw-------. 1 opc opc 597 Jun  2 08:02 cwallet.sso
-rw-------. 1 opc opc   0 Jun  2 08:01 cwallet.sso.lck
```





**To create an auto-login wallet for the source database TDE keystore:**

1. Create a directory where you want to create and store the wallet.
```
zdmuser> mkdir tde_wallet_path
```


For example
```
/u01/app/zdmhome> mkdir tdeWallet
```


2. Create a wallet.
```
zdmuser> $ZDM_HOME/bin/orapki wallet create -wallet tde_wallet_path
-auto_login_only
```


For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/orapki wallet create -wallet ./tdeWallet
-auto_login_only
Oracle PKI Tool Release 19.0.0.0.0 - Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.
```


3. Add the source database TDE keystore credentials to the wallet.
```
zdmuser> $ZDM_HOME/bin/mkstore -wrl tde_wallet_path
-createCredential store tdeuser
```


At the prompt, enter the TDE keystore password.

For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/mkstore -wrl ./tdeWallet
-createCredential store tdeuser
Oracle Secret Store Tool Release 19.0.0.0.0 - Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019, Oracle and/or its affiliates. All rights reserved.

Your secret/Password is missing in the command line
Enter your secret/Password:
Re-enter your secret/Password:
```


4. Verify that the wallet files were created.
```
zdmuser> ls -l tde_wallet_path
```


For example
```
/u01/app/zdmhome> ls -l tdeWallet
total 4
-rw-------. 1 opc opc 581 Jun  2 08:06 cwallet.sso
-rw-------. 1 opc opc   0 Jun  2 08:04 cwallet.sso.lck
```





**To create an auto-login wallet for the target CDB database TDE keystore password:**

1. Create a directory where you want to create and store the wallet.
```
zdmuser> mkdir cdb_tde_wallet_path
```


For example
```
/u01/app/zdmhome> mkdir cdbtdeWallet
```


2. Create a wallet.
```
zdmuser> $ZDM_HOME/bin/orapki wallet create -wallet cdb_tde_wallet_path
-auto_login_only
```


For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/orapki wallet create -wallet ./cdbtdeWallet
-auto_login_only
Oracle PKI Tool Release 19.0.0.0.0 - Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.
```


3. Add the source database TDE keystore credentials to the wallet.
```
zdmuser> $ZDM_HOME/bin/mkstore -wrl cdb_tde_wallet_path
-createCredential store cdbtdeuser
```


At the prompt, enter the TDE keystore password.

For example
```
/u01/app/zdmhome> $ZDM_HOME/bin/mkstore -wrl ./cdbtdeWallet
-createCredential store cdbtdeuser
Oracle Secret Store Tool Release 19.0.0.0.0 - Production
Version 19.4.0.0.0
Copyright (c) 2004, 2019, Oracle and/or its affiliates. All rights reserved.

Your secret/Password is missing in the command line
Enter your secret/Password:
Re-enter your secret/Password:
```





**To create an auto-login wallet to store the set of passwords for dbuser:**

1. Create an auto login wallet to store the set of passwords for `dbuser` using the following command:
```
./orapki wallet create -wallet  -auto_login_only
```


2. Create the entries for the usernames used by the `-asuser` option, in step 1:
```
./mkstore -wrl  -createEntry
```





**Accessing the Wallets in a Logical Migration Job**

In a logical migration you configure the wallet parameters using the RSP `WALLET_*` parameters appropriate for your migration use case. See the following links for details about the individual parameters. 

* [WALLET_AMAZONS3SECRET](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-A3332E65-40AC-4851-96F2-D5B55A96B156): Amazon S3 Secret Key wallet path 
* [WALLET_SOURCEGGADMIN](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-7D3B6B7F-6B41-4D8F-80EB-82D9D2EF2EE3): Source database administrative user `ggadmin` password wallet path 
* [WALLET_SOURCECONTAINER](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-0E154BF7-2B40-48CB-9157-E90C291E2072): Source database administrative user password wallet path 
* [WALLET_SOURCECGGADMIN](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-464EB423-D8D4-4E28-978F-FD98B9B2F867): Source database administrative user `c##ggadmin` password wallet path 
* [WALLET_TARGETADMIN](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-08E166D0-56CE-4FA8-96E4-6658B7A00DE2): Target database administrative user password wallet path 
* [WALLET_TARGETGGADMIN](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-93A493C5-8D35-475A-9E11-68564AE28534): Target database administrative user `ggadmin` password wallet path 
* [WALLET_OGGADMIN](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-28C5B8A0-B382-4043-BBDB-9C0F34928CA0): Oracle GoldenGate hub administrative password wallet path 
* [WALLET_DATAPUMPENCRYPTION](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-C794FD29-CB88-4CF0-81E2-A698E37E8D38): Oracle Data Pump encryption password wallet path 
* [WALLET_OCIAUTHTOKEN](zero-downtime-migration-logical-migration-response-file-parameters-reference.md#GUID-8098E64D-C466-4E6E-826E-5C2277D02D32): OCI Auth Token password wallet path 
* [WALLET_USERACTION](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-38FD145F-B908-48A8-ADDA-2A92DADFB3E3): Auto-login wallet to store the set of passwords for `dbuser`



**Accessing the Wallets in a Physical Migration Job**

In a physical migration you configure the wallet parameters as options in the `ZDMCLI database migration` command. See [migrate database](zero-downtime-migration-zdmcli-command-reference.md#GUID-37ABF830-2FC3-4F71-9132-DF05DCFABBB9) for information about the `database migration` options. 

* -sourcesyswallet *`sys_wallet_path`*: Source database `SYS` password wallet path 
* -osswallet *`oss_wallet_path`*: Object Storage Service (OSS) backup user wallet path 
* -dvwallet *`dv_wallet_path`*: Oracle Database Vault owner wallet path 
* -tdekeystorewallet *`tde_wallet_path`*: Transparent Data Encryption (TDE) keystore password wallet path 
* -tgttdekeystorewallet *`tde_wallet_path`*: Target container database TDE keystore password wallet path 
* -backupwallet *`backup_wallet_path`*: RMAN backup password wallet path 



Note that if you are converting a non-multitenant source database to a multitenant architecture on the target, that is a pluggable database (PDB), then you can also create an auto-login wallet for the target container database (CDB) TDE keystore password.

**Setting Command Options to Access the Wallets**

To specify wallet information in the `ZDMCLI MIGRATE DATABASE` command, set the `-sourcesyswallet`, `-osswallet`, `-tdekeystorewallet`, and `-tgttdekeystorewallet` options as shown here. 
```
zdmuser> $ZDM_HOME/bin/zdmcli migrate database
-sourcedb source_db_unique_name_value
-sourcenode source_database_server_name
-srcauth zdmauth
-srcarg1 user:source_database_server_login_user_name
-srcarg2 identity_file:zdm_installed_user_private_key_file_location
-srcarg3 sudo_location:/usr/bin/sudo
-targetnode target_database_server_name
-backupuser object_store_login_user_name
-rsp response_file_location
-tgtauth zdmauth
-tgtarg1 user:target_database_server_login_user_name
-tgtarg2 identity_file:zdm_installed_user_private_key_file_location
-tgtarg3 sudo_location:/usr/bin/sudo
**-sourcesyswallet sys_wallet_path
-osswallet oss_wallet_path
-tdekeystorewallet tde_wallet_path
-tgttdekeystorewallet cdb_tde_wallet_path**
-eval
```


* `-sourcesyswallet *`sys_wallet_path`*` specifies the full path for the auto-login wallet file on the Zero Downtime Migration host containing the `SYS` password of the source database 
* `-osswallet *`oss_wallet_path`*` specifies the full path for the auto-login wallet file on the Zero Downtime Migration host containing credentials for the Object Storage Service backup user 
* `-tdekeystorewallet *`tde_wallet_path`*` specifies the full path for the auto-login wallet file on the Zero Downtime Migration host containing the TDE keystore password 
* `-tgttdekeystorewallet *`cdb_tde_wallet_path`*` specifies the full path for the auto-login wallet file on the Zero Downtime Migration host containing the target CDB TDE keystore password 



**Evaluation Mode Example**
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
**-sourcesyswallet /u01/app/zdmhome/sysWallet
-osswallet /u01/app/zdmhome/ossWallet
-eval**

Operation "zdmcli migrate database" scheduled with the job ID "1".
```


**Migration Mode Example**
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
**-sourcesyswallet /u01/app/zdmhome/sysWallet
-osswallet /u01/app/zdmhome/ossWallet**

Operation "zdmcli migrate database" scheduled with the job ID "2".
```

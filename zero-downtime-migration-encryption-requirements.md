## BZero Downtime Migration Encryption Requirements {#GUID-BE79A11D-3F15-4DAC-9B5B-27B691AB1265}

Zero Downtime Migration does not always require encryption at the source. However, it is recommended to encrypt all tablespaces in the Cloud. Any new tablespaces created in the Cloud is encrypted by default.

* To verify the encryption state of the tablespaces, use the following query: `SQL> select tablespace_name, encrypted from dba_tablespaces;`
* To encrypt the tablespaces post migration: For encrypting existing tablespaces, see [Encryption Conversions for Tablespaces and Databases](https://docs.oracle.com/en/database/oracle/oracle-database/21/asoag/encryption-conversions-tablespaces-and-databases1.md#GUID-1CA947A5-164A-4AA9-AD75-F7D9D4F6FFF8) for more information. 



The following tables list specific cases when encryption is not required.

**Table B-1 On-Premises Unencrypted Primary and Cloud Encrypted Standby**

Operation | On-Premises Primary 11g R2 | Cloud Standby 11g R2 | On-Premises Primary 12c R1 | Cloud Standby 12c R1 | On-Premises Primary 12c R2 | Cloud Standby 12c R2 and later | Notes  
---|---|---|---|---|---|---|---  
Data Guard initial setup for on-premises primary and cloud standby | Unencrypted | Encrypted | Unencrypted | Encrypted | Unencrypted | Encrypted | In these cases the standby database is manually encrypted after instantiation  
New tablespace creation on-premises primary | Unencrypted | Unencrypted | Unencrypted | Unencrypted | Unencrypted | Unencrypted | Requires manual TDE conversion for standby database  
Redo generated in on-premises primary | Unencrypted | Unencrypted | Unencrypted | Unencrypted | Unencrypted | Unencrypted |   
Archived logs | Unencrypted | Unencrypted | Unencrypted | Unencrypted | Unencrypted | Unencrypted |   
New and changed blocks | Unencrypted | Encrypted | Unencrypted | Encrypted | Unencrypted | Encrypted | Redo shipped from the on-premises primary to the cloud is not encrypted  
Recovery in the cloud standby | N/A | Encrypted | N/A | Encrypted | N/A | Encrypted | Redo shipped from the on-premises primary to the cloud is not encrypted  

**Table B-2 Cloud Encrypted Primary and On-Premises Unencrypted Standby**

Operation | Cloud Primary 11g R2  | On-Premises Standby 11g R2 | Cloud Primary 12c R1 | On-Premises Standby 12c R1 | Cloud Primary 12c R2 | On-Premises Standby 12c R2 and later | Notes  
---|---|---|---|---|---|---|---  
New tablespace creation in cloud primary | Encrypted | Encrypted | Encrypted | Encrypted | Encrypted | Encrypted | ASO required for on-premises to decrypt  
Redo generated in cloud primary | Encrypted | Encrypted | Encrypted | Encrypted | Encrypted | Encrypted | ASO required for on-premises to decrypt  
Archived logs | Encrypted | Encrypted | Encrypted | Encrypted | Encrypted | Encrypted | ASO required for on-premises to decrypt  
New and changed blocks for existing unencrypted tablespace on standby | Encrypted | Encrypted* | Encrypted | Encrypted* | Encrypted | Unencrypted | ASO is required on-premises to decrypt and encrypt * For 11g R2 and 12c R1 redo apply will encrypt only if redo is encrypted  
Recovery in the on-premises standby | N/A | Encrypted | N/A | Encrypted | N/A | Unencrypted data depends on whether the datafile is encrypted | ASO required for on-premises database
## 7Customizing a Migration Job {#GUID-193B52E9-A094-472A-9204-3A77EF5137E7}

You can customize the Zero Downtime Migration work flow with scripts which can be run at the beginning or end of a specified migration job phase. In Zero Downtime Migration, these customizations are called **custom plug-ins with user actions**. 

The following topics describe how to customize a migration job.

### About Custom Plug-ins with User Actions {#GUID-A2B28FC5-3C51-4FD2-9C88-28E7F9FFB197}

In Zero Downtime Migration, a custom script, or bundle of scripts, that you want to plug in and run as part of a migration job is called a **custom plug-in with user action**. 

A custom plug-in with user action, which is also referred to as a **user action** , is associated with an operational phase in the migration job and can be run before the phase or after it. 

A **pre-action** is a user action performed at the beginning of the associated phase. Likewise, a **post-action** is performed at the end of the associated phase. 

Once a user action is associated with a migration phase, Zero Downtime Migration copies it to the respective node (for non-Autonomous Databases) and runs the user action locally. Zero Downtime Migration supplies the user action script execution with a set of parameters for developers to use the DBNAME, DBHOME, DB SCAN and ZDM log locations, and many more parameters.

For Autonomous Database, Zero Downtime Migration allows SQL to be registered as a user action and executes the SQL in an Autonomous Database instance from the Zero Downtime Migration server through a JDBC connection.

### Parameters Supplied for Custom Plug-ins with Shell Script User Actions {#GUID-6219034A-97DD-4787-B089-AC4AE97853E5}

At run time, Zero Downtime Migration invokes the user action script and supplies it with set of auto-populated environment variables that can be used to program the logic.

These variables are supplied as environment variables with values so you can program the custom plug-in using these values. 

For example, the value of `ZDM_SRCDBHOME` specifies the database home associated with the current migration job source database, and similarly `SRC_SCAN_NAME` indicates the scan listener information for the source database that you can use to connect to the database. 

The following is a listing of the user action parameters with expected values. See the footnotes for more details.
```
ZDM_OPTYPE=MIGRATE_DATABASE
ZDM_PHASE=PRE
ZDM_SRCDB=src_db_name
ZDM_SRCDBHOME=src_db_home
ZDM_TARGETDB=tgt_db_name
ZDM_TARGETDBHOME=tgt_db_home
ZDM_PROGRESSLISTENERHOST=zdm_node_name
ZDM_PROGRESSLISTENERPORT=zdm_progress_listener_port
SRC_SCAN_NAME=src_scan_name
SRC_SCAN_PORT=src_scan_port
TGT_SCAN_NAME=tgt_scan_name
TGT_SCAN_PORT=tgt_scan_port
ZDM_USERACTIONDATA=user_action_data1
ZDM_OP_PHASE=current_job_phase2
RHP_UA_CHAIN_LIST=list_of_useractions3
ZDM_SRCDBSID=src_db_sid
ZDM_TGTDBSID=tgt_db_sid
ZDM_VERSION=zdm_version
ZDM_CLI=zdm_command
TGT_LOG_PATH= tgt_log_path4
SRC_LOG_PATH=src_log_path5
TGT_DB_SERVICE=tgt_db_service
SRC_DB_SERVICE=src_db_service
ZDM_DATAPUMP_EXPORT_DIR_NAME=DATA_PUMP_DIR_NAME
ZDM_DATAPUMP_EXPORT_DIR_PATH=data_pump_dir_path
ZDM_DATAPUMP_DUMP_PREFIX=data_pump_prefix
ZDM_SRCDBPDB=
ZDM_TGTDBPDB= Note: This is populated
using: SQL> 'SELECT * FROM DBA_PDBS'
```


1`ZDM_USERACTIONDATA` specifies the useraction argument that was specified in `zdmcli migrate database` command option `-useractiondata *`user_action_data`*`

2`ZDM_OP_PHASE` specifies the current phase of the migration job, for example,`ZDM_VALIDATE_SRC`. 

3`ZDM_UA_CHAIN_LIST` is passed to every user action execution call. This parameter holds the list of user actions chained to the executing user action. Chained user actions are the user actions whose output will be supplied to the executing user action. 

4` TGT_LOG_PATH` specifies the target node Zero Downtime Migration log path for the custom plug-in to store any log files for future reference. 

5 `SRC_LOG_PATH` specifies the source node Zero Downtime Migration log path for the custom plug-in to store any log files for future reference. 

### Variables Supplied for Custom Plug-ins with SQL Based User Actions {#GUID-63FC40E4-B703-4D0E-928D-4DA549925B6A}

SQL based user actions are used for databases where the database node is not accessible, for example, Oracle Autonomous Database (ADB).

For SQL based user actions, the following variables are supplied: 

* `AS_USER`: The user action will be executed by this database user. This user is set while creating the user action using the `-asuser` option. 
* `ORACLE_HOME`: The home path of the database. 
* `ACTION_OUTPUT_DIR`: Directory where the output of all user actions will be stored for the ongoing migration. 
* `OUTPUT_FILE` \- This gives the full path to the output file on which the user action output will be stored in the following format of the variable value: `/.out`

You can access the above variables with the SQL user action by specifying the variable with a `:` before it. For example, `:ACTION_OUTPUT_DIR` in the SQL will be replaced by the actual `ACTION_OUTPUT_DIR`. 

### User Action Scripts {#GUID-0AF4DAD2-5B19-40CF-9076-2AEDAA6E9005}

You can use the following example user action scripts to help you create your own scripts.

For an on-premises source and Cloud target with node access, Zero Downtime Migration requires that the user action be a script. The script file is copied over to the respective node and is executed locally.

You can specify the service name alias to be used for executing the specific user action. If you do not specify the service name alias, then ZDM will connect with the service specified in the response parameter file. You can specify the required service alias as a first line comment in the user action script as follows:
```
--USERACTION_SERVICE=
```


For example : 
```
--USERACTION_SERVICE=TP or
--USERACTION_SERVICE=LOW
```


This is followed by the actual SQLs. 

For Autonomous Database targets, Zero Downtime Migration requires that the user action script be a SQL file (.sql), which is executed in the Autonomous Database target using a JDBC connection.

Example 7-1 action.sh

Shown here is a sample user action script that discovers the supplied parameters.
```
#!/bin/sh

for var in $@
do
if [[ ${var} == *"ZDM_SRCDB="* ]]
then
IFS='=' read -ra DBARR <<< "${var}"
SRCDBNAME=${DBARR[1]}
fi
if [[ ${var} == *"ZDM_TARGETDB="* ]]
then
IFS='=' read -ra DBARR <<< "${var}"
TARGETDBNAME=${DBARR[1]}
fi
if [[ $var == *"ZDM_SRCDBHOME="* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
SRCDBHOME=${PATHARR[1]}
fi
if [[ $var == *"ZDM_TARGETDBHOME="* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
TARGETDBHOME=${PATHARR[1]}
fi
if [[ $var == *"ZDM_OP_PHASE="* ]]
then
IFS='=' read -ra PHASEARR <<< "$var"
ZDMPHASE=${PHASEARR[1]}
fi
if [[ $var == *"eval"* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
EVALRUN=${PATHARR[1]}
fi
if [[ $var == *"LOG_PATH"* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
LOGPATH=${PATHARR[1]}
fi
done

echo "`date` Starting CUSTOM_USERACTION" >> $LOG_PATH/CUSTOM_USERACTION.log
echo $@ >> $LOG_PATH/CUSTOM_USERACTION.log
```


Example 7-2 Running SQL*Plus in a user action
```
#!/bin/sh

for var in $@
do
if [[ ${var} == *"ZDM_SRCDB="* ]]
then
IFS='=' read -ra DBARR <<< "${var}"
SRCDBNAME=${DBARR[1]}
fi
if [[ ${var} == *"ZDM_TARGETDB="* ]]
then
IFS='=' read -ra DBARR <<< "${var}"
TARGETDBNAME=${DBARR[1]}
fi
if [[ $var == *"ZDM_SRCDBHOME="* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
SRCDBHOME=${PATHARR[1]}
fi
if [[ $var == *"ZDM_TARGETDBHOME="* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
TARGETDBHOME=${PATHARR[1]}
fi
if [[ $var == *"ZDM_OP_PHASE="* ]]
then
IFS='=' read -ra PHASEARR <<< "$var"
ZDMPHASE=${PHASEARR[1]}
fi
if [[ $var == *"eval"* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
EVALRUN=${PATHARR[1]}
fi
if [[ $var == *"LOG_PATH"* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
LOGPATH=${PATHARR[1]}
fi
done

check_dv_staus()
{
export ORACLE_HOME=${2}
export PATH=$ORACLE_HOME/bin:$PATH
export LOGFILE=$3
export currenthostname=`hostname -a` >> $LOGFILE
echo "Current DB Host=$currenthostname" >> $LOGFILE
CURRENTNODE=`srvctl status database -db ${1} | grep $currenthostname | cut -d" " -f2` >> $LOGFILE
SQLRETURN=$?
echo "Curent DB Node=${CURRENTNODE}" >> $LOGFILE
if [ "$SQLRETURN" -ne "0" ]; then
return 1
fi
export ORACLE_SID=${CURRENTNODE}
echo "`date` Checking Database Vault Status" >> $LOGFILE
$ORACLE_HOME/bin/sqlplus -s / as sysdba 2>> $LOGFILE << EOF > /dev/null
whenever sqlerror exit 1
SET PAGESIZE 60
SET LINESIZE 1300
SET VERIFY OFF TRIMSPOOL ON  HEADING OFF  TERMOUT OFF  FEEDBACK OFF
spool ${LOGFILE} append;

SELECT 'Check Status : '||PARAMETER FROM V\$OPTION WHERE PARAMETER = 'Oracle Database Vault' AND VALUE='TRUE';
SELECT 'Check Grant : '||GRANTED_ROLE from dba_role_privs where GRANTED_ROLE in ('DV_PATCH_ADMIN') and grantee='SYS';
select distinct ('Check TDE enabled ' || ENCRYPTED) from dba_tablespaces where ENCRYPTED='YES';
spool off
EOF
SQLRETURN=$?
if [ "$SQLRETURN" -ne "0" ]; then
return 1
fi

if grep -q "Check Status : Oracle Database Vault" $LOGFILE;
then
echo "`date`:$ORACLE_SID:Database Vault is enabled " >> $LOGFILE
if grep -q "Check Grant : DV_PATCH_ADMIN" $LOGFILE;
then
return 3 # sys privs already granted
else
return 4 # sys privs are not granted
fi
else
echo "`date`:$ORACLE_SID: DV is not enabled" >> $LOGFILE
return 2
fi
}

if [[ $ZDMPHASE == "ZDM_VALIDATE_SRC" || $ZDMPHASE == "ZDM_VALIDATE_TGT" ]]
then
export LOGFILE=$LOG_PATH/${SRCDBNAME}_${ZDMPHASE}_datavault.log
echo "`date` Start User Action for Phase: $ZDMPHASE " > $LOGFILE
echo $@ >> $LOGFILE
check_dv_staus $SRCDBNAME $SRCDBHOME $LOGFILE
CHECKDV_STATUS_RETURN_CODE=$?
if [ "$CHECKDV_STATUS_RETURN_CODE" -eq "1" ]; then
echo "`date`:${SRCDBNAME}:Unable to check DataVault status" >> $LOGFILE
exit 1
fi

if [ "$CHECKDV_STATUS_RETURN_CODE" -eq "2" ]; then
echo "`date`:${SRCDBNAME}:DataVault is not enabled " >> $LOGFILE
exit 0
fi

if [ "$CHECKDV_STATUS_RETURN_CODE" -eq "3" ];
then
echo "`date`:${SRCDBNAME}:Database Vault SYS privileges granted" >> $LOGFILE
exit 0
fi
```


Example 7-3 Action file archive extractor action_extract.sh

If you bundle more than one user action into an action file, as described in [Registering User Actions](customizing-migration-job1.md#GUID-56DA9423-2DD4-4CC9-8F82-5F594753F23D), you must also supply a script that unzips the action file, as shown in this example. 
```
#!/bin/sh
MKDIR=/bin/mkdir
DATE=/bin/date
UNZIP=/usr/bin/unzip
#get the current location, extract path from it and then from the same
directory perform unzip to /tmp directory
script_path=$0
script_dir=${script_path%/*}
timestamp=$($DATE +%s)
unzip_dir=/tmp/rhp_${timestamp}
$MKDIR $unzip_dir
$UNZIP ${script_dir}/pack.zip -d $unzip_dir

echo "/bin/sh ${unzip_dir}/pack/main.sh $@"
/bin/sh ${unzip_dir}/pack/main.sh  "$@"
```


Once the archive is extracted, action_extract.sh runs main.sh, which in turn runs any user action scripts.
```
#!/bin/sh
script_path=$0
script_dir=${script_path%/*}
#extract args and execute all scripts
echo "/bin/sh ${script_dir}/wc_add_pre.sh $@"
/bin/sh ${script_dir}/wc_add_pre.sh  "$@"
```


action_phase_pre.sh
```
#!/bin/sh
touch /tmp/SAMPLE_PRE_OUT.txt;
echo $* >> /tmp/SAMPLE_PRE_OUT.txt;
```


### User Action for Oracle Autonomous Database Target and Amazon Web Services RDS Source {#GUID-61040402-B875-4146-B81F-9005C348E630}

You can create user action scripts with SQL commands when the source database is Oracle Database from Amazon Web Services (AWS) RDS and the target database is Oracle Autonomous Database (ADB).

Oracle Database from Amazon Web Services (AWS) RDS source and Oracle Autonomous Database (ADB) target do not support shell script user actions. They support SQL-only user actions.

The SQL action is performed as user specified for accessing the Amazon Web Services RDS Source.

### Registering User Actions {#GUID-56DA9423-2DD4-4CC9-8F82-5F594753F23D}

User actions must be registered with the Zero Downtime Migration service host to be plugged in as customizations for a particular operational phase.

Use the Zero Downtime Migration software installed user (for example, `zmduser`) to add user actions to a database migration job. 

The ZDMCLI command `add useraction` registers a custom action script. The user action script, and any work flow options specified for when to run it, are copied to the Zero Downtime Migration metadata. 

In the `add useraction` command, specify the following options. 

* **-useraction user_action_name** (required) 

Assign the user action a name. 

* **-actionscript script_name** (required) 

Provide the full path and script file name, for example, `/home/zdmuser/useract.sh`. 

* **-optype MIGRATE_DATABASE** (required) 

Define the operation for which the user action is configured as `MIGRATE_DATABASE`. 

* **-phase**

To run the script at the proper phase of the migration job work flow, specify the migration job phase that the action must be associated with. You can register custom plug-ins for phases after `ZDM_SETUP_TGT` in the migration job work flow. 

See [Zero Downtime Migration Process Phases](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-DA18E66B-2943-4C67-91B7-0CF3E0B0DC6B)

* **-pre** and **-post**

Indicate whether the user action should run before or after the specified phase.

* **-onerror**

You can specify a behavior if the user action encounters an error at runtime, which you can set to either `ABORT`, to end the process, or `CONTINUE`, to continue the migration job even if the custom plug-in exits with an error. 

* **-asuser**

Allows you to specify the database user to run the SQL commands in the user action.

See [WALLET_USERACTION](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-38FD145F-B908-48A8-ADDA-2A92DADFB3E3)




The following example shows you how to add user actions `zdmvaltgt` and `zdmvalsrc`. 
```
zdmuser> $ZDM_HOME/bin/zdmcli add useraction
-useraction zdmvaltgt
-actionscript /home/zdmuser/useract.sh
-optype MIGRATE_DATABASE
-phase ZDM_VALIDATE_TGT
-pre
-onerror ABORT
-asuser dbuser


zdmuser> $ZDM_HOME/bin/zdmcli add useraction
-useraction zdmvalsrc
-actionscript /home/zdmuser/useract1.sh
-optype MIGRATE_DATABASE
-phase ZDM_VALIDATE_SRC
-pre
-onerror CONTINUE
-asuser dbuser
```


In the above commands, the scripts `useract.sh` and `useract1.sh`, specified in the `-actionscript` option, are copied to the Zero Downtime Migration service host repository meta data, and they are run if they are associated with any migration job run using an action template. 

**Running Multiple Scripts in One Action**

If you need to run multiple scripts as a single user action, all of the action scripts can be bundled as an **action file** archive and supplied along with an action script, as shown in the following example. The action script should unzip the action file and invoke the scripts within. 
```
zdmuser> $ZDM_HOME/bin/zdmcli add useraction
-useraction reconfigure_services
**-actionscript /home/zdmuser/action_extract.sh**
**-actionfile /home/zdmuser/pack.zip**
-optype MIGRATE_DATABASE
-phase ZDM_RECOVER_TGT
-post
-onerror ABORT
```


For example scripts and script requirements, see [User Action Scripts](customizing-migration-job1.md#GUID-0AF4DAD2-5B19-40CF-9076-2AEDAA6E9005). 

For information about other options you can use with the ZDMCLI command `add useraction` see [add useraction](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-A37D4FCB-A617-4E64-9866-E22D51959DF3)

### Creating an Action Template {#GUID-AA8C2539-C6EF-4163-B002-2A1D1F8DD6DC}

After the useraction plug-ins are registered, you create an action template that combines a set of action plug-ins which can be associated with a migration job.

An action template is created using the ZDMCLI command `add imagetype`, where the image type, `imagetype`, is a bundle of all of the useractions required for a specific type of database migration. Create an image type that associates all of the useraction plug-ins needed for the migration of the database. Once created, the image type can be reused for all migration operations for which the same set of plug-ins are needed. 

The base type for the image type created here must be `CUSTOM_PLUGIN`, as shown in the example below. 

For example, you can create an image type `ACTION_ZDM` that bundles both of the useractions created in the previous example, zdmvalsrc and zdmvaltgt. 
```
zdmuser> $ZDM_HOME/bin/zdmcli add imagetype -imagetype ACTION_ZDM -basetype
CUSTOM_PLUGIN -useractions zdmvalsrc,zdmvaltgt
```


### Associating an Action Template with a Migration Job {#GUID-5844AEE1-0AC9-4705-9295-52CA4DFFA26D}

When you run a migration job you can specify the image type that specifies the plug-ins to be run as part of your migration job.

As an example, run the migration command specifying the action template ACTION_ZDM created in previous examples, `-imagetype ACTION_ZDM`, including the image type results in running the useract.sh and useract1.sh scripts as part of the migration job workflow. 

By default, the action plug-ins are run for the specified operational phase on all nodes of the cluster. If the access credential specified in the migration command option `-tgtarg2` is unique for a specified target node, then an additional auth argument should be included to specify the auth credentials required to access the other cluster nodes. For example, specify `-tgtarg2 nataddrfile:*`auth_file_with_node_and_identity_file_mapping`*`. 

A typical nataddrfile for a 2 node cluster with node1 and node2 is shown here.
```
node1:node1:identity_file_path_available_on_zdmservice_node
node2:node2:identity_file_path_available_on_zdmservice_node
```


### Querying Action Plug-ins {#GUID-759E7B0F-8AB2-469B-90B9-9D7D18495314}

You can query action plug-ins registered with the Zero Downtime Migration service host.

To display the configuration of a user action:
```
zdmuser> $ZDM_HOME/bin/zdmcli query useraction -useraction user_action_name
```


See [query useraction](zero-downtime-migration-zdmcli-command-reference.md#GUID-91A2DEE3-4448-4F29-8509-7678A5AEEF79) for usage information. 

### Updating Action Plug-ins {#GUID-9A066824-4105-4F1B-9842-068669EE23EB}

You can update a registered Zero Downtime Migration user action plug-in configuration.

The following example shows you how to modify the user action `zdmvalsrc` to run the user action script after phase `ZDM_VALIDATE_SRC` instead of before it. 
```
zdmuser> $ZDM_HOME/bin/zdmcli modify useraction
-useraction zdmvalsrc
-phase ZDM_VALIDATE_SRC
-optype MIGRATE_DATABASE
**-post**
```


This change is propagated to all of the associated action templates, so you do not need to update the action templates.

To modify a user action script and update the user action, follow the instructions in [Modifying User Action Scripts](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMUG-GUID-2CAAD8F2-C46A-460C-8623-26239C038CFE). 

### Modifying User Action Scripts {#GUID-2CAAD8F2-C46A-460C-8623-26239C038CFE}

You can edit a user action script locally and use the ZDMCLI command `modify useraction` to update the script in the user action metadata. 

1. If you do not have a local copy of the script, you can locate the cached user action script in the user action metadata using the ZDMCLI command `query useraction`. 
```
zdmuser> $ZDM_HOME/bin/zdmcli query useraction
-useraction zdmvalsrc
```


The full base path to the script is displayed. 

> **note:** Do not directly modify the script in the location shown in the command output. 

2. Copy the script to a temporary location and edit it.

3. Update the script in the user action metadata using the ZDMCLI command `modify useraction`. 
```
zdmuser> $ZDM_HOME/bin/zdmcli modify useraction
-useraction zdmvalsrc
-actionscript /home/zdmuser/useract1.sh
```





### Modifying an Action Template {#GUID-7EEA0FE8-0393-4F59-87E6-5DF367A4AD34}

You can modify an action template to change the set of action plug-ins associated with a migration job.

An action template is modified using the ZDMCLI command `modify imagetype`. This command is useful if you register a new user action script and you want to add it to an existing template. 

For example, to add a new user action named zdmclonetgt to image type `ACTION_ZDM`, run `modify imagetype` with the new complete list of user actions as shown here: 
```
zdmuser> $ZDM_HOME/bin/zdmcli modify imagetype -imagetype ACTION_ZDM
-useractions zdmvalsrc,zdmvaltgt,**zdmclonetgt**
```


### Chained User Action Output {#GUID-C5785B18-97C5-4C2F-B033-86673DE13108}

You can get data from one or more user actions and use that data to run another user action. For example, you can perform an action on the source database and use that data to perform an action on the target database.

When adding a user action, you can use the `-outputfrom` option to provide the list of user actions whose output will be supplied to the user action being added. 

In the following example there are two user actions already registered in Zero Downtime Migration, USERAC1 and USERAC2, both of which generate some output. This output can be used by a third user action, USERAC3, by listing them with the `-outputfrom` option, as shown here. 
```
$ZDM_HOME/bin/zdmcli add useraction
-useraction USERAC3
-actionscript useractionscript.sh
-outputfrom USERAC1,USERAC2
```


**Accessing the Data**

The output file is copied to the same location where the useraction file runs from, so the content of the output file can be accessed from the same location.

* On an Autonomous Database system:

    To access the output file in an Autonomous Database system user action SQL script, use 
    
    `@:ACTION_OUTPUT_DIR/*`useraction_name`*.out`
    
    Note that `ACTION_OUTPUT_DIR` indicates the directory where outputs will be stored, and `@` will replace this variable with the actual directory path. 
    
    For example, to use output from the get_ddl1 user action, specify
    
    `@:ACTION_OUTPUT_DIR/get_ddl1.out;`. 

* On a Co-Managed database system:

    To access the output file in a user action script run on a co-managed database system, use 
    
    `OUTPUTFILE=$CURR_DIR/*`useraction_name`*.out;`. 
    
    Where `CURR_DIR` is the current directory where the target user action file and the output from the other user actions are placed. 
    
    For example, to use output from the get_ddl1 user action, specify
    
    `OUTPUTFILE=$CURR_DIR/get_ddl1.out;`. 
    
    The user action script must also contain the parameter `RHP_UA_CHAIN_LIST` which gives the list of chained user action names. 




**Example User Actions in an Autonomous Database Migration**

The following example shows how to get DDL output from the source database, and runs it on the target database.

User action `get_ddl1` runs on the source database and generates DDL output. 
```
$ZDM_HOME/bin/zdmcli add useraction
-useraction get_ddl1
-phase ZDM_VALIDATE_SRC
-pre
-optype MIGRATE_DATABASE
-runscope ONENODE
-onerror ABORT
-actionscript /home/useraction1.sh
```


User action `table_fromddl` runs on the target database, and uses the output from user action `get_ddl1`. 
```
$ZDM_HOME/bin/zdmcli add useraction
-useraction table_fromddl
-phase ZDM_VALIDATE_TGT
-pre
-optype MIGRATE_DATABASE
-runscope ONENODE
-onerror ABORT
-actionscript /home/useraction2.sh
**-outputfrom get_ddl1**
```


Adding both user actions to an imagetype:
```
$ZDM_HOME/bin/zdmcli add imagetype
-imagetype OUTPUTIMG1
-basetype CUSTOM_PLUGIN
-useractions get_ddl1,table_fromddl
```


Content of useraction1.sh:
```
#!/bin/sh

for var in $@
do
if [[ $var == *"ZDM_SRCDBHOME="* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
SRCDBHOME=${PATHARR[1]}
fi

if [[ $var == *"LOG_PATH"* ]]

then

IFS='=' read -ra PATHARR <<< "$var"

LOGPATH=${PATHARR[1]}

fi
done

echo $SRCDBHOME;

ORACLE_HOME=$SRCDBHOME;
export ORACLE_HOME;
ORACLE_SID=zdm1121;
export ORACLE_SID;
export TMPLOG=/tmp/tmptmp.log;
true>$TMPLOG;
export PATH=$ORACLE_HOME/bin:$PATH
#echo "home is $ORACLE_HOME";
$ORACLE_HOME/bin/sqlplus -s / as sysdba 2>> $TMPLOG << EOF > /tmp/testlog
whenever sqlerror exit 1
SET PAGESIZE 60
SET LINESIZE 1300
SET VERIFY OFF TRIMSPOOL ON HEADING OFF TERMOUT OFF FEEDBACK OFF
spool ${TMPLOG} append;

select dbms_metadata.get_ddl('TABLE', 'TEST1') from DUAL;
spool off
EOF

SQLRETURN=$?
if [ "$SQLRETURN" -ne "0" ]; then
echo "Failed to run $SQLRETURN";
fi
sed -i 's/PCTFREE 10 PCTU//g' $TMPLOG;
echo "$(cat $TMPLOG)";
```


Content of useraction2.sh:
```
select name from v$database;
@:ACTION_OUTPUT_DIR/ZDM_VALIDATE_SRC_PRE_get_ddl1.out;
select name from v$database;
```


**Example User Actions in a Co-Managed Database Migration**

For this example, the `add useraction` and `add imagetype` commands are same as above in the Autonomous Database use case, but the script contents are different. 

Content of useraction1.sh
```
#!/bin/sh

for var in $@
do
if [[ $var == *"ZDM_SRCDBHOME="* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
SRCDBHOME=${PATHARR[1]}
fi
done

ORACLE_HOME=$SRCDBHOME;
export ORACLE_HOME;
ORACLE_SID=zdmsrc1;
export ORACLE_SID;
export TMPLOG=/tmp/tmptmp.log;
true>$TMPLOG;
export PATH=$ORACLE_HOME/bin:$PATH
#echo "home is $ORACLE_HOME";
$ORACLE_HOME/bin/sqlplus -s / as sysdba 2>> $TMPLOG << EOF > /tmp/testlog
whenever sqlerror exit 1
SET PAGESIZE 60
SET LINESIZE 1300
SET VERIFY OFF TRIMSPOOL ON HEADING OFF TERMOUT OFF FEEDBACK OFF
spool ${TMPLOG} append;

select dbms_metadata.get_ddl('TABLE', 'TEST1') from DUAL;
spool off
EOF

SQLRETURN=$?
if [ "$SQLRETURN" -ne "0" ]; then
echo "Failed to run $SQLRETURN";
fi
echo "$(cat $TMPLOG)";
```


Content of useraction2.sh:
```
#!/bin/sh

for var in $@
do
if [[ $var == *"ZDM_TARGETDBHOME="* ]]
then
IFS='=' read -ra PATHARR <<< "$var"
TARGETDBHOME=${PATHARR[1]}
fi
done

CURR_DIR=$(dirname "$(readlink -f "$0")")
export OUTPUTFILE=$CURR_DIR/get_ddl1.out;
# export ORACLE_HOME=/u01/app/oracle/product/19.0.0.0/dbhome_1;
ORACLE_HOME=$TARGETDBHOME;
export ORACLE_HOME;
ORACLE_SID=zdmnov19;
export ORACLE_SID;
export TMPLOG=/tmp/tmptmp.log;
sed -i 's/PCTFREE 10 PCTUS//g' $OUTPUTFILE;
export OUTPUT=$(cat $OUTPUTFILE);
echo $OUTPUT;
echo "">$TMPLOG;
$ORACLE_HOME/bin/sqlplus -s / as sysdba 2>> $TMPLOG << EOF > /tmp/testlog
whenever sqlerror exit 1
SET PAGESIZE 60
SET LINESIZE 1300
SET VERIFY OFF TRIMSPOOL ON HEADING OFF TERMOUT OFF FEEDBACK OFF
spool ${TMPLOG} append;

$OUTPUT;
spool off
EOF

echo "$(cat $TMPLOG)";
#SELECT 'NAME: '|| NAME FROM V\$DATABASE;
```


### Customizing Job to Setup Standby {#GUID-7DAEFC2F-5AC9-4DDB-89E0-A5254DD5D0F2}

Use the `-stopafter` option to create a standby on Cloud and stop the ZDM flow. 

The `-stopafter` option truncates the work flow by the specified phase, and upon completion of the specified phase, the migration job is marked with status completed. 

See [migrate database](https://docs.oracle.com/en/database/oracle/zero-downtime-migration/21.4/zdmug/zero-downtime-migration-zdmcli-command-reference.md#GUID-37ABF830-2FC3-4F71-9132-DF05DCFABBB9) for usage information. 
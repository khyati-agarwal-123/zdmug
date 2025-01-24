## 10Troubleshooting Zero Downtime Migration {#GUID-049A2119-EE11-4FA6-81B5-ACC98E8BC23A}

This section describes how to handle migration job failures.

For more information about troubleshooting Zero Downtime Migration and known issues in the current release, see the [Zero Downtime Migration Release Notes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMRN-GUID-A1A467DC-FC06-4409-AF7F-BF0186CD8C54). 

### Handling Migration Job Failures {#GUID-52614A34-155A-4CC7-9D06-E2364B677B59}

If your migration job fails, the following logs can help you discover the issue.

**Migration Job Output Logs**

If your migration job encounters an error, refer to the migration job output logs, Zero Downtime Migration service logs, and server-specific operational phase logs present at the respective source or target database servers.

If the migration job encounters an exception (that is, fails) then the logs can provide some indication of the nature of the fault. The logs for the migration procedures executed in the source and target environments are stored on the servers in the respective source and target environments. The Zero Downtime Migration command output location is provided to you when the migration job is run with the `ZDMCLI` command `migrate database`. You can also find the log file location (`Result file path`) in the output of the `ZDMCLI` command `query job -jobid *`job-id`*`. 

**Zero Downtime Migration Service Host Log**

Determine which operational phase the migration job was in at the time of failure, and whether the phase belongs to the source (phase name contains `SRC`) or target (phase name contains `TGT`) . Check the Zero Downtime Migration service host log at `$ZDM_BASE/crsdata/*`zdm_service_host`*/rhp/zdmserver.log.0`, and access the respective source or target server to check the log associated with the operational phase in `$ORACLE_BASE/zdm/zdm_*`db_unique_name`*_*`job-id`*/zdm/log`. 

If the Zero Downtime Migration service does not start, then check the Zero Downtime Migration service logs for process start-up errors to determine the cause of the error reported. The Zero Downtime Migration service log can be found at `$ZDM_BASE/crsdata/*`zdm_service_host`*/rhp/zdmserver.log.0`. 

**Data Pump Log**

For logical migration jobs on co-managed target databases, refer to the Data Pump log in the specified `DATA_PUMP_DIR`. For Autonomous Database targets, the Data Pump logs are uploaded to a data bucket specified with `DATAPUMPSETTINGS_DATABUCKET_*` in the response file. If the data bucket is not specified, then you will need to upload the dump from Autonomous Database to Object Storage to access it. See [How To View Import Log Generated For ADW/ATP (Doc ID 2448060.1)](https://support.oracle.com/rs?type=doc&id=2448060.1)

If a migration job fails, you can fix the cause of the failure and then re-run the job while monitoring the logs for progress.
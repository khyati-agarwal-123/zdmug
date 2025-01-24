## 9Managing the Zero Downtime Migration Service {#GUID-9F558CC4-3B34-422E-9E9A-33D1C1CFB19E}

Perform Zero Downtime Migration service life cycle operations using `zdmservice`. 

### Starting and Stopping the Zero Downtime Migration Service {#GUID-27AAF8E7-C289-498A-9C21-C71C64A4BA7A}

You must start the Zero Downtime Migration service before you can migrate your databases using Zero Downtime Migration.

Start the Zero Downtime Migration service, `zdmservice`, as user `zdmuser`, with the following command. 
```
zdmuser> $ZDM_HOME/bin/zdmservice start
```


If you must stop the Zero Downtime Migration service, run the following command.
```
zdmuser> $ZDM_HOME/bin/zdmservice stop
```


### Checking Zero Downtime Migration Service Status {#GUID-55588EBB-2112-4671-9ED2-2CDD8C8DD1BB}

Check the status of the Zero Downtime Migration to see if it is running, and other service details.

To check the Zero Downtime Migration service status use the following command.

```
zdmuser> $ZDM_HOME/bin/zdmservice status
---------------------------------------
Service Status
---------------------------------------
Running:       true
Tranferport:   5000-7000
Conn String:   jdbc:mysql://localhost:8897/
RMI port:      8895
HTTP port:     8896
Wallet path:   /u01/app/zdmbase/crsdata/fopds/security
```


### Updating Zero Downtime Migration Software {#GUID-6D5B6C15-C2A8-4E8E-915A-4E829393FF29}

If you already have Zero Downtime Migration software installed on a host, you should always make sure it is the latest available release. Zero Downtime Migration software updates give you the latest features and fixes while retaining existing job information, metadata, and log files.

Before you begin the software update, review the following requirements.

* Updating Zero Downtime Migration to the latest software can only be done from the latest Zero Downtime Migration kit. The current version is 21.5, and update can be done from version 21.4. Verify your software kit version using the following command:
```
zdmuser> $ZDM_HOME/bin/zdmcli -build
version: 21.0.0.0.0
full version: 21.5.0.0.0
patch version: N/A
label date: 240219.8
ZDM kit build date: Jul 30 2024 16:31:49 UTC
CPAT build version: 24.6.0
```


* Verify that your existing Zero Downtime Migration software install location has at least 15GB free space.

* Verify that you have enough space to back up the existing Zero Downtime Migration home (`ZDM_HOME`) and `ZDM_BASE` to the software download location. 

* **Important**: Run the update script from outside of the currently installed Zero Downtime Migration home. 

Running the script from within a Zero Downtime Migration home results in home install and uninstall failures and leaves the service in an inconsistent state.

* The path specified in `ziploc` should have read/write access for `zdmuser`. 

* All of the commands in the following procedure should be run as the existing Zero Downtime Migration software owner. For example, run as `zdmuser` in the examples that follow. 




1. Download the Zero Downtime Migration software kit from <https://www.oracle.com/database/technologies/rac/zdm-downloads.md> to the Zero Downtime Migration service host.
2. Change to the directory to where Zero Downtime Migration software is downloaded and unzip the software. 
```
zdmuser> cd zdm_download_directory
zdmuser> unzip zdmversion.zip
```


3. Run the `zdminstall.sh` script as the exiting Zero Downtime Migration home owner to update the software from the software download location.

    ```
    zdmuser>./zdminstall.sh update oraclehome=existing_zdm_oracle_home
    ziploc=zdm_software_location
    ```
    
    * `zmdinstall.sh` is the installation and update script 
    * `oraclehome` is the absolute path to the Oracle Home where the existing Zero Downtime Migration software is installed 
    * `ziploc` is the location of the compressed software file (zip) included in the Zero Downtime Migration kit 

    
    For example,
    ```
    zdmuser>/u01/app/oracle/zdm/shiphome/update/zdminstall.sh update
    oraclehome=/u01/app/zdmhome
    ziploc=/u01/app/oracle/zdm/shiphome/update/zdm_home.zip
    ```
    
    
    The update script does the following operations.
    
    1. Backs up the existing Zero Downtime Migration home (`ZDM_HOME`) and `ZDM_BASE` into software download location 
    
    2. Stops the currently running Zero Downtime Migration service
    
    3. Removes the currently installed Zero Downtime Migration home
    
    4. Installs the new binaries in the Zero Downtime Migration home 
    
    5. Restores the configuration data.
    
    The new Zero Downtime Migration home will retain all of the details of any migrations run with the previous Zero Downtime Migration home.
    
4. The Zero Downtime Migration service must be manually started after the upgrade. Start the Zero Downtime Migration service as user `zdmuser`.
    ```
    zdmuser> $ZDM_HOME/bin/zdmservice start
    ```


    You must start `zdmservice` before you can migrate your databases using Zero Downtime Migration. 
    
    If you must stop the Zero Downtime Migration service, run the following command.
    ```
    zdmuser> $ZDM_HOME/bin/zdmservice stop
    ```


5. Verify that the Zero Downtime Migration service installation is successful.

    When you run the following command, the output should be similar to that shown here.
```
zdmuser> $ZDM_HOME/bin/zdmservice status
---------------------------------------
Service Status
---------------------------------------
Running:       true
Tranferport:   5000-7000
Conn String:   jdbc:mysql://localhost:8899/
RMI port:      8895
HTTP port:     8896
Wallet path:   /u01/app/zdmbase/crsdata/fopds/security
```





### Uninstalling Zero Downtime Migration Software {#GUID-6EE7E484-03C6-4C49-9109-C1580C04C3F2}

Remove Zero Downtime Migration software from the Zero Downtime Migration service host.

All commands are run as `zdmuser`. 

1. Stop the Zero Downtime Migration service.
```
zdmuser> $ZDM_HOME/bin/zdmservice stop
```


2. Run the following command to uninstall the software.
```
zdmuser> $ZDM_HOME/bin/zdmservice deinstall
```





### Performing a Silent Update or Deinstallation {#GUID-4EC29B94-8980-482B-9708-F75A6260E405}

You can skip the confirmation prompt during Zero Downtime Migration software update or deinstallation to ensure these operations run smoothly.

A `-silent` option is available for zdminstall.sh `update` or `deinstall` operations to avoid being asked for confirmation, as shown in the following examples. 

Example of silent update:
```
zdmuser> cd zdm_download_directory
zdmuser> unzip zdmversion.zip
...
zdmuser>./zdminstall.sh update -silent oraclehome=absolute_path_to_zdm_home
ziploc=zdm_software_location
```


Example of silent deinstall:
```
zdmuser> $ZDM_HOME/bin/zdmservice deinstall -silent
```


### Viewing the Cloud Premigration Advisor Tool Version {#GUID-20A483C6-F4BB-403C-AADD-A9B2A71293BD}

There are two ways to display the current version for the Cloud Premigration Advisor Tool (CPAT).

* Run the Cloud Premigration Advisor Tool script with the `--version` option. 
```
$ZDM_HOME/rhp/zdm/lib/cpat/premigration.sh --version
```


* Run ZDMCLI with the `-build` option. 
```
zdmuser> $ZDM_HOME/bin/zdmcli -build
```





### Updating the Cloud Premigration Advisor Tool {#GUID-05BDFF44-A3BF-43DD-BFDF-2CB562EF9180}

Keep the Cloud Premigration Advisor Tool up to date to get the latest migration remedies, as well as any bug fixes in the tool.

> **note:** Schedule Cloud Premigration Advisor Tool updates when no migrations are running. 

1. Download the latest version of the tool from [Cloud Premigration Advisor Tool (CPAT) Analyzes Databases for Suitability of Cloud Migration (Doc ID 2758371.1)](https://support.oracle.com/rs?type=doc&id=2758371.1). 

2. Check the version of the tool that is currently installed in your Zero Downtime Migration home.
```
./ZDM_home/rhp/zdm/lib/cpat/premigration.sh --version
```


3. Back up the currently installed tool by making a copy of the contents of directory `$ZDM_HOME/rhp/zdm/lib/cpat`. 

4. Delete all of the files in directory `$ZDM_HOME/rhp/zdm/lib/cpat`. 

5. Unzip the new Cloud Premigration Advisor Tool release file to `$ZDM_HOME/rhp/zdm/lib/cpat`. 

6. If needed, change the permissions on the unzipped files to remove unauthorized access.

7. Verify that the new version of the tool is installed in your Zero Downtime Migration home.
```
./ZDM_home/rhp/zdm/lib/cpat/premigration.sh --version
```





### Setting the MySQL Port {#GUID-3684AD13-E271-4024-A47C-F634E0D7C367}

You can discover and set the port number that Zero Downtime Services uses for MySQL.

**MySQL Default Port Number**

Zero Downtime Migration uses MySQL internally, configuring it by default on port 3306\. If a port number is not specified and the default is not available, Zero Downtime Migration increases the port value by one and retries up to five times.

**Finding the Current Port Number**

Run `zdmservice status` to see the current MySQL port number in the connection string, as shown here. 
```
zdmuser> $ZDM_HOME/bin/zdmservice status

Conn String: jdbc:mysql://localhost:8897/
```


**Changing the Port Number**

You can change this default to another value using the `zdmservice modify mysqlPort=*`port`*` option. 
```
zdmuser> $ZDM_HOME/bin/zdmservice modify mysqlPort=port
```

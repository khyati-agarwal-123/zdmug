## 2Setting Up Zero Downtime Migration Software {#GUID-A55FEDBA-236A-4006-91A5-6F28D100C5B2}

When you install Zero Downtime Migration software, read this section carefully as there may have been changes since the last time you performed an installation.

The Zero Downtime Migration software kit supports both physical and logical migrations. You only need to install one kit to get all of the functionality.

Always see the [Zero Downtime Migration Release Notes](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMRN-GUID-A1A467DC-FC06-4409-AF7F-BF0186CD8C54) for the latest information about known issues. Also, see the README file included with the downloaded Zero Downtime Migration software for any additional information about software installation and updates. 

If you already have Zero Downtime Migration software installed on a host, you should always make sure it is the latest available release. Zero Downtime Migration software updates give you the latest features and fixes while retaining existing job information, metadata, and log files. Always check the version and determine if it is the latest by comparing it with what's available on the downloads page.

For information about updating existing software to the latest release, removing the software, and starting and stopping the Zero Downtime Migration service, see [Managing the Zero Downtime Migration Service](managing-zero-downtime-migration-service1.md#GUID-9F558CC4-3B34-422E-9E9A-33D1C1CFB19E). 

### Prepare a Host for Zero Downtime Migration Software Installation {#GUID-A19ADA5C-755B-468B-86BC-A6642D3F3F34}

If a host has not had Zero Downtime Migration software installed on it previously, verify that it complies with the requirements and perform any pre-installation tasks, then download and install the software. Once the software is installed, the host is referred to as the Zero Downtime Migration service host.

Provision a host with the following prerequisites and complete the following pre-installation tasks before installing Zero Downtime Migration software on it.

* The Zero Downtime Migration service host should be a dedicated system, but it can be shared for other purposes.
However, if the Zero Downtime Migration service is installed on the same host where RHP server is deployed, see [Running RHP and Zero Downtime Migration Service on the Same Host](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/zero-downtime-migration/21.5/zdmug&id=ZDMRN-GUID-8F391EE6-EC27-4DB7-88D3-1FFAD2E97EED) for more info. 

* Zero Downtime Migration software requires a standalone Linux host running on any one of the following platforms:
Oracle Linux 7, Oracle Linux 8, or Red Hat Enterprise Linux 8. 

* The Zero Downtime Migration service host must be able to connect to the source and the target database servers.

* Ensure that the Linux host has 100 GB of free storage space.

* You may use an existing user, or, on the Zero Downtime Migration service host, as root user, create a `zdm` group and add `zdmuser` user to the group. 

For example,
```
root> groupadd zdm
root> useradd –g zdm zdmuser
```


* Verify that the `glibc-devel` and `expect` packages are installed. 
For Oracle Linux 8 installations with Base Environment "Minimal Install" you also need to install the packages `unzip libaio oraclelinux-developer-release-el7 libnsl ` and `ncurses-compat-libs`.
```
- Minimum OS version Oracle Linux 7
- Ensure Packages 'glibc-devel' and 'expect' are installed
- For higher versions such as Oracle Linux 8:
- Ensure Packages 'libnsl' and 'ncurses-compat-libs' are also installed
- glibc-devel' and 'expect are a must for either versions
- unzip libaio oraclelinux-developer-release-el7 libnsl and ncurses-compat-libs are required for Oracle Linux 8
```


* Verify that the `/etc/hosts` entry for the host name and IP address are configured as expected, so that the host selected for Zero Downtime Migration software installation resolves to the correct IP address and the IP address is reachable with `ping`. 

> **note:** Using aliases is not supported. 

* During the installation, the script might report any missing packages and instructions for setting appropriate values for kernel parameters. Be sure to install the missing packages and set the kernel parameters before the Zero Downtime Migration software installation.

* Optionally, set a `ZDM_HOME` environment variable to the absolute path of the directory where the Zero Downtime Migration software will be installed. All of the examples in this document use `$ZDM_HOME`. 
```
zdmuser> export ZDM_HOME=absolute_path_to_zdm_home
```


* The release info file includes the ZDM jar dependencies and the included versions, `zdm.release.info` is located in `zdm home:./zdm_home/rhp/zdm.release.info` .
```
Example:
cat rhp/zdm.release.infoFeb 10 2024 01:32:57
UTCjlib/jipher-jce.jar 10.31jlib/jipher-pki.jar 10.15jlib/sshd-common.jar
2.10.0jlib/jersey-common.jar 2.36.0jlib/jackson-core.jar
2.15.2jlib/json-java.jar 20231013.0.0jlib/org-everit-json-schema.jar
1.14.2rhp/zdm/lib/autoupgrade.jar
23.4.230921rhp/zdm/lib/cpat/lib/premigration.jar
23.12.0rhp/zdm/lib/opc_install.jar
RDBMS_MAIN_LINUX.X64_170816tomcat/lib/catalina.jar
N/Amicronaut/jlib/micronaut-core.jar 3.8.5-2
```


* In secured environments where access to internet is restricted from the ZDM server, you may have to allow the OCI endpoints in your network firewall. These end points are used by ZDM for API calls to the Oracle Cloud for Autonomous targets. Allow the region-specific endpoints for the following services: 
    * [Core services](https://docs.oracle.com/en-us/iaas/api/#/en/iaas/20160918/)
    * [IAM](https://docs.oracle.com/en-us/iaas/api/#/en/identity/20160918/)
    * [Database](https://docs.oracle.com/en-us/iaas/api/#/en/database/20160918/)
    * [Object Storage](https://docs.oracle.com/en-us/iaas/api/#/en/objectstorage/20160918/)



### Install Zero Downtime Migration Software {#GUID-7722EA14-6DD0-4879-A62C-884A6AC52B73}

All commands are run as `zdmuser`. 

1. Download the Zero Downtime Migration software kit from <https://www.oracle.com/database/technologies/rac/zdm-downloads.html> to the Zero Downtime Migration service host.

> **note:** Review for new patches if required, by reviewing the Patch 33509650: ZDM PATCH USING MOS. 

2. Create home and base directories.

    The home directory is where the Zero Downtime Migration software will be installed.

    The base directory is where all of the Zero Downtime Migration configuration files, logs, and other artifacts are stored.

    For example:

`/u01/app/zdmhome`

`/u01/app/zdmbase`

3. Install the Zero Downtime Migration software as a non-root user.

    In this example the installation user is `zdmuser`. 

    1. Change to the directory to where Zero Downtime Migration software is downloaded and unzip the software. 
    ```
    zdmuser> cd zdm_download_directory
    zdmuser> unzip zdmversion.zip
    ```
    
    
    2. Run the Zero Downtime Migration installation script.
    ```
    zdmuser>./zdminstall.sh setup oraclehome=zdm_oracle_home oraclebase=zdm_base_directory
    ziploc=zdm_zip_location
    ```

    
    * `zmdinstall.sh` is the installation script 
    * `oraclehome` is the absolute path to the Oracle Home directory where the Zero Downtime Migration software will be installed 
    * `oraclebase` is the absolute path to the base directory where all of the Zero Downtime Migration configuration files, logs, and other artifacts are stored 
    * `ziploc` is the location of the compressed software file (zip) included in the Zero Downtime Migration kit 

    For example,
    ```
    zdmuser>./zdminstall.sh setup oraclehome=/u01/app/zdmhome
    oraclebase=/u01/app/zdmbase ziploc=/u01/app/oracle/zdm/shiphome/zdm_home.zip
    ```
    
    
    Note that the Zero Downtime Migration service host requires PERL to run the install script.
    
    Hereafter, the `oraclehome` value is referred to as `ZDM_HOME`, and the `oraclebase` value is referred to as `ZDM_BASE`. 
    
    Ignore the following messages which are displayed on the terminal at the end of installation. There is no need to run these scripts.
    ```
    As a root user, execute the following script(s):
    1. $ZDM_HOME/inventory/orainstRoot.sh
    2. $ZDM_HOME/root.sh
    ```

4. Start the Zero Downtime Migration service as user `zdmuser`.
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


6. If necessary, change the default MySQL port.

    Zero Downtime Migration uses MySQL internally, configuring it by default on port 8897, as shown in the above `zdmservice status` example output. If you want to change this port number, see [Setting the MySQL Port](managing-zero-downtime-migration-service1.md#GUID-3684AD13-E271-4024-A47C-F634E0D7C367). 




### Installing Zero Downtime Migration on VM in Oracle Cloud Infrastructure {#GUID-20F1B58E-6521-4C2A-90EB-C3072189E339}

You can install ZDM on Oracle Linux 8 VM in OCI by performing the following steps:

#### To install Zero Downtime Migration on Oracle Linux 8 VM in OCI {#TASK_XHK_DSR_JWB}

1. Create your VM instance in OCI and download the private and public keys.
2. Connect to your VM instance through SSH by using your downloaded private key.
3. Download the ZDM image from [Oracle Zero Downtime Migration](https://www.oracle.com/database/technologies/rac/zdm.md).
4. Install the following software for ZDM: 

* `expect`
* `glibc-devel`
* `ncurses-compat-libs`
* `libnsl`

5. Run the following command: 

    `sudo yum install oraclelinux-developer-release-el8 libnsl perl unzip glibc-devel expect libaio ncurses-compat-libs ncurses-devel numactl-libs openssl mlocate bind-utils `

6. Create a directory to store the ZDM home and base.

    For example, `mkdir /home/opc/zdm`

7. Unzip the ZDM image. 

    For example, `[opc@zdminstance20230201 zdm]$ unzip ZDMKIT.zip`

8. Run the ZDM installer:

    `[opc@zdminstance20230201 zdm]$ /home/opc/zdm/zdmkit/zdminstall.sh setup oraclehome=/home/opc/zdm/home oraclebase=/home/opc/zdm/base ziploc=/home/opc/zdm/zdmkit/zdm_home.zip `

9. Start the ZDM service:

    `[opc@zdminstance20230201 zdm]$ home/bin/zdmservice start`

10. Verify the installed version.

    Example:
    ```
    [opc@zdminstance20230201 zdm]$ home/bin/zdmcli -build
    version: 21.0.0.0.0
    full version: 21.5.0.0.0
    patch version: N/A
    label date: 240219.8
    ZDM kit build date: Jul 30 2024 16:31:49 UTC
    CPAT build version: 24.6.0
    ```


11. When ZDM is configured on a compute VM provisioned in OCI, you may need to update `/etc/hosts` to resolve the source and/or target database systems. If the ZDM compute VM is rebooted for some reason, the custom entries in `/etc/hosts` are not saved. To save these custom entries, edit `/etc/oci-hostname.conf` as follows: 
    ```
    # This configuration file controls the hostname persistence behavior for Oracle Linux
    
    # compute instance on Oracle Cloud Infrastructure (formerly Baremetal Cloud Services)
    
    # Set PRESERVE_HOSTINFO to one of the following values
    
    # -- default behavior to update hostname, /etc/hosts and /etc/resolv.conf to
    
    #        reflect the hostname set during instance creation from the metadata service
    
    # -- preserve user configured hostname across reboots; update /etc/hosts and
    
    #           /etc/resolv.conf from the metadata service
    
    # -- preserve user configured hostname across instance reboots; no custom
    
    #        changes to /etc/hosts and /etc/resolv.conf from the metadata service,
    
    #        but dhclient will still overwrite /etc/resolv.conf
    
    # -- preserve hostname and /etc/hosts entries across instance reboots;
    
    #        update /etc/resolv.conf from instance metadata service
    
    PRESERVE_HOSTINFO=2      ==> Set this to at least 2.
    ```


Entries in `/etc/hosts` will now be saved across instance reboots for ZDM running on a provisioned compute instance or VM in OCI. 




### Installing Zero Downtime Migration on Red Hat Enterprise Linux 8 in an Oracle Cloud Infrastructure Instance {#GUID-71605843-565A-461A-B088-A6F11A3E21A9}

You can install ZDM on a Red Hat Enterprise Linux 8 in an OCI instance by performing the following steps:

#### To install Zero Downtime Migration on Red Hat Enterprise Linux 8 {#unique_784145950}

1. Obtain Red Hat Enterprise Linux 8.8 KVM Guest Image from [here](https://access.redhat.com/downloads/content/rhel). 
2. Upload the image to a new OCI bucket.
3. Import the image as custom image.
4. Create an instance from the image.
5. Generate new SSH keys.
6. Connect to the RHEL8 OCI instance.



Perform the following steps:

1. Configure `yum` repositories by running the following command:
    ```
    $ sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    $ sudo yum-config-manager --add-repo http://yum.oracle.com/repo/OracleLinux/OL8/baseos/latest/x86_64
    $ sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
    $ sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8
    $ sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-beta
    ```


2. Enable `swap` by running the following commands:
    ```
    $ sudo swapon --show
    $ sudo fallocate -l 1G /swapfile
    $ sudo chmod 600 /swapfile
    $ sudo mkswap /swapfile
    $ sudo swapon /swapfile
    $ sudo swapon --show
    ```


3. Install the required Red Hat Enterprise Linux 8 packages as follows:
    ```
    $ sudo yum install zip.x86_64 --nogpgcheck
    $ sudo yum install expect.x86_64 --nogpgcheck
    $ sudo yum install glibc-devel.x86_64 --allowerasing --nogpgcheck
    $ sudo yum install libnsl.x86_64 --allowerasing --nogpgcheck
    $ sudo yum install ncurses-compat-libs.x86_64 --allowerasing --nogpgcheck
    $ sudo yum install libaio.x86_64 --allowerasing --nogpgcheck
    ```


4. Install Zero Downtime Migration.



### Installing Zero Downtime Migration on Red Hat Enterprise Linux 9 in an Oracle Cloud Infrastructure Instance {#GUID-1D27981C-8E50-420B-B1F2-83A082C44098}

You can install ZDM on a Red Hat Enterprise Linux 9 in an OCI instance by performing the following steps:

#### To install Zero Downtime Migration on Red Hat Enterprise Linux 9 {#unique_23132015}

1. Obtain Red Hat Enterprise Linux 9 KVM Guest Image from [here](https://access.redhat.com/downloads/content/rhel). 
2. Upload the image to a OCI bucket.
3. Create a custom image using the downloaded `qcow2` file. 
4. Create an `IaaS` instance from the new custom image. 
5. Generate and download new SSH Keys. You can also use any existing SSH keys.
6. Connect to the instance by using SSH keys or cloud-user.



Perform the following steps:

1. Configure `yum` repositories by running the following command:
    ```
    [root@rhel9zdm yum.repos.d]# yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm -y
    [root@rhel9zdm yum.repos.d]# pwd
    /etc/yum.repos.d
    
    [root@rhel9zdm yum.repos.d]# ls -l
    total 160
    -rw-r--r--. 1 root root 1142 Aug 17 2023 epel-cisco-openh264.repo
    -rw-r--r--. 1 root root 1552 Aug 17 2023 epel-testing.repo
    -rw-r--r--. 1 root root 1453 Aug 17 2023 epel.repo
    -rw-r--r--. 1 root root 3883 Apr 9 16:48 ol9.repo
    -rw-r--r--. 1 root root 145427 Apr 9 16:38 redhat.repo
    
    
    [root@rhel9zdm yum.repos.d]# cat epel.repo
    [epel]
    name=Extra Packages for Enterprise Linux $releasever - $basearch
    # It is much more secure to use the metalink, but if you wish to use a local mirror
    # place its address here.
    #baseurl=https://download.example/pub/epel/$releasever/Everything/$basearch/
    metalink=https://mirrors.fedoraproject.org/metalink?repo=epel-$releasever&arch=$basearch&infra=$infra&content=$contentdir
    enabled=1
    gpgcheck=1
    countme=1
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-$releasever
    
    [epel-debuginfo]
    name=Extra Packages for Enterprise Linux $releasever - $basearch - Debug
    # It is much more secure to use the metalink, but if you wish to use a local mirror
    # place its address here.
    #baseurl=https://download.example/pub/epel/$releasever/Everything/$basearch/debug/
    metalink=https://mirrors.fedoraproject.org/metalink?repo=epel-debug-$releasever&arch=$basearch&infra=$infra&content=$contentdir
    enabled=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-$releasever
    gpgcheck=1
    
    [epel-source]
    name=Extra Packages for Enterprise Linux $releasever - $basearch - Source
    # It is much more secure to use the metalink, but if you wish to use a local mirror
    # place its address here.
    #baseurl=https://download.example/pub/epel/$releasever/Everything/source/tree/
    metalink=https://mirrors.fedoraproject.org/metalink?repo=epel-source-$releasever&arch=$basearch&infra=$infra&content=$contentdir
    enabled=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-$releasever
    gpgcheck=1
    [root@rhel9zdm yum.repos.d]#
    ```


2. Install the required packages as `root` user and run the following commands :
    
    ```
    [root@rhel9zdm yum.repos.d]# yum install zip.x86_64 --nogpgcheck
    [root@rhel9zdm yum.repos.d]# yum install glibc-devel.x86_64 --allowerasing --nogpgcheck
    [root@rhel9zdm yum.repos.d]# yum install libaio.x86_64 --allowerasing --nogpgcheck
    [root@rhel9zdm yum.repos.d]# yum install libnsl --allowerasing --nogpgcheck
    [root@rhel9zdm yum.repos.d]# yum install ncurses-compat-libs.x86_64 --allowerasing --nogpgcheck
    [root@rhel9zdm yum.repos.d]# yum install glibc-devel.x86_64 --allowerasing --nogpgcheck
    [root@rhel9zdm yum.repos.d]# yum install expect.x86_64 --nogpgcheck
    [root@rhel9zdm yum.repos.d]# yum install wget
    ```


3. Enable `swap` by running the following commands:
    ```
    [root@rhel9zdm ~]# swapon --show
    [root@rhel9zdm ~]# fallocate -l 1G /swapfile
    [root@rhel9zdm ~]# chmod 600 /swapfile
    [root@rhel9zdm ~]# mkswap /swapfile
    [root@rhel9zdm ~]# swapon /swapfile
    [root@rhel9zdm ~]# swapon --show
    NAME      TYPE  SIZE USED PRIO
    /swapfile file 1024M   0B   -2
    
    [root@rhel9zdm ~]#
    ```


4. Install Zero Downtime Migration.


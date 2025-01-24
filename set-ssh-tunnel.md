## Set up an SSH Tunnel for Online Physical Migration {#GUID-808F9EBE-036E-48CF-B55F-396C3681754E}

If connectivity using SCAN and the SCAN port is not possible between the source and target database servers, set up an SSH tunnel from the source database server to the target database server.

The following procedure sets up an SSH tunnel on the source database servers for the root user. Note that this procedure amounts to setting up what may be considered a temporary channel. Using this connectivity option, you will not be able to synchronize between the target database and source database after switchover, and with this configuration you cannot fall back to the original source database.

> **note:** The following steps refer to Oracle Cloud Infrastructure, but are also applicable to Oracle Exadata Database Service on Cloud@Customer and Oracle Exadata Database Service on Dedicated Infrastructure. 

1. Generate an SSH key file without a passphrase for the `opc` user on the target Oracle Cloud Infrastructure server, using the information in [Generate SSH Keys Without a Passphrase](generate-ssh-keys-passphrase.md#GUID-602216AD-4B59-441B-BA8D-AC7B333038FD). If the target is an Oracle RAC database, then generate an SSH key file without a passphrase from the target Oracle RAC server specified in `-targetnode` argument.
2. Add the contents of the Oracle Cloud Infrastructure server opc_user_home/.ssh/id_rsa.pub file into the Oracle Cloud Infrastructure server opc_user_home/.ssh/authorized_keys file.
3. Copy the target Oracle Cloud Infrastructure server private SSH key file onto the source server in the /root/.ssh/ directory. If the source is an Oracle RAC database, copy the file into all of the source servers.

For better manageability, keep the private SSH key file name the same as the target server name, and keep the .ppk extension. For example, ocidb1.ppk (where ocidb1 is the target server name). 

The file permissions should be similar to the following.
```
/root/.ssh>ls -l ocidb1.ppk
-rw------- 1 root root 1679 Oct 16 10:05 ocidb1.ppk
```


4. Put the following entries in the source server /root/.ssh/config file.
```
Host *
ServerAliveInterval 10
ServerAliveCountMax 2

Host OCI_server_name
HostName OCI_server_IP_address
IdentityFile Private_key_file_location
User OCI_user_login
ProxyCommand /usr/bin/nc -X connect -x proxy_name:proxy_port %h %p
```


Where

* *`OCI_server_name`* is the Oracle Cloud Infrastructure target database server name without the domain name. For an Oracle RAC database use the node specified for `-targetnode` without the domain name. 
* *`OCI_server_IP_address`* is the Oracle Cloud Infrastructure target database server IP address. For an Oracle RAC database use the node specified for `-targetnode`. 
* *`Private_key_file_location`* is the location of the private key file on the source database server, which you copied from the target database server in step 3 above. 
* *`OCI_user_login`* is the OS user used to access the target database servers. 
* *`proxy_name`* is the host name of the proxy server. 
* *`proxy_port`* is the port of the proxy server. 

> **note:** The ProxyCommand option is specified when using a proxy server for connectivity. 

For example, after specifying the relevant values, the /root/.ssh/config file should be similar to the following.
```
Host *
ServerAliveInterval 10
ServerAliveCountMax 2

Host ocidb1
HostName 192.0.2.1
IdentityFile /root/.ssh/ocidb1.ppk
User opc
ProxyCommand /usr/bin/nc -X connect -x www-proxy.example.com:80 %h %p
```


The file permissions should be similar to the following.
```
/root/.ssh>ls -l config
-rw------- 1 root root 1679 Oct 16 10:05 config
```


In the above example, the Oracle Cloud Infrastructure server name is ocidb1, and the Oracle Cloud Infrastructure server public IP address is 192.0.2.1.

If the source is an Oracle Cloud Infrastructure Classic server, the *`proxy_name`* is not required, so you can remove or comment the line starting with `ProxyCommand`. 

If the source is an Oracle RAC database, then copy the same /root/.ssh/config file onto all of the source Oracle RAC database servers. This file will have the Oracle Cloud Infrastructure server name, Oracle Cloud Infrastructure server public IP address, and private key file location of first Oracle Cloud Infrastructure Oracle RAC server information configured.

5. Make sure that you can SSH to the first target Oracle Cloud Infrastructure server from the source server before you enable the SSH tunnel.

For an Oracle RAC database, test the connection from all of the source servers to the first target Oracle Cloud Interface server. 

Using the private key:
```
[root@ocicdb1 ~] ssh -i /root/.ssh/ocidb1.ppk opc@ocidb1
Last login: Fri Dec  7 14:53:09 2018 from 192.0.2.3

[opc@ocidb1 ~]$
```


> **note:** SSH connectivity requires direct, non-interactive access between the source and target database servers, without the need to enter a passphrase. 

6. Run the following command on the source server to enable the SSH tunnel.
```
ssh -f OCI_hostname_without_domain_name -L ssh_tunnel_port_number:OCI_server_IP_address:OCI_server_listener_port -N
```


Where

* *`OCI_hostname_without_domain_name`* is the Oracle Cloud Infrastructure target database server name without a domain name. For an Oracle RAC database use the node specified for `-targetnode` without the domain name. 
* *`ssh_tunnel_port_number`* is any available ephemeral port in the range (1024-65545). Make sure that the SSH tunnel port is not used by any other process in the server before using it. 
* *`OCI_server_listener_port`* is the target database listener port number. The listener port must be open between the source database servers and Oracle Cloud Infrastructure target servers. 
* *`OCI_server_IP_address`* is the IP address of the target database server. For a single instance database, specify the Oracle Cloud Infrastructure server IP address. For an Oracle RAC database, specify the Oracle Cloud Infrastructure scan name with the domain name. If the scan name with domain name is not resolvable or not working, then specify the IP address obtained using the `lsnrctl status` command output. For example,
```
Listening Endpoints Summary...
(DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=LISTENER)))
(DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.0.2.9)(PORT=1521)))
(DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=192.0.2.10)(PORT=1521)))
```


The following is an example of the command run to enable the SSH tunnel.
```
[root@ocicdb1~]ssh -f ocidb1 -L 9000:192.0.2.9:1521 -N
```


For an Oracle RAC database, this step must be repeated on all of the source servers.

7. Test the SSH tunnel.

Log in to source server, switch to the `oracle` user and source the database environment, and run the following command. 
```
tnsping localhost:ssh_tunnel_port
```


For example, 
```
[oracle@ocicdb1 ~] tnsping localhost:9000
```


The command output is similar to the following.
```
TNS Ping Utility for Linux: Version 12.1.0.2.0 - Production on 22-JAN-2019 05:41:57
Copyright (c) 1997, 2014, Oracle.  All rights reserved.
Used parameter files:
Used HOSTNAME adapter to resolve the alias
Attempting to contact (DESCRIPTION=(CONNECT_DATA=(SERVICE_NAME=))(ADDRESS=(PROTOCOL=TCP)(HOST=127.0.0.1)(PORT=9000)))
OK (50 msec)
```


If tnsping does not work, then the SSH tunnel is not enabled.

For Oracle RAC, this step must be repeated on all of the source servers.



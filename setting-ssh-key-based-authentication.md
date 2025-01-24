## ISetting Up SSH Key Based Authentication {#GUID-06286CA9-C5DE-4C85-9AD9-245C4C2B0F24}

> **note:** These steps are applicable for physical migrations and logical migrations where either the source and/or target are accessed using SSH keys (co-managed databases). 

These steps are **not applicable** for logical migration where the target is Autonomous Database. 

Complete the following procedure to ensure the required connectivity between the Zero Downtime Migration service host and the source and target database servers. 

1. On the Zero Downtime Migration service host, verify that the RSA authentication key pairs are available without a passphrase for the Zero Downtime Migration software installed user.

If a new key pair must be generated without the passphrase, then, as a Zero Downtime Migration software installed user, generate new key pairs as described in [Generate SSH Keys Without a Passphrase](generate-ssh-keys-passphrase.md#GUID-602216AD-4B59-441B-BA8D-AC7B333038FD). 

2. Rename the private key file.

Rename the *`zdm_installed_user_home`*/.ssh/id_rsa file name to *`zdm_installed_user_home`*/.ssh/*`zdm_service_host`*.ppk. 

3. Add the contents of the *`zdm_installed_user_home`*/.ssh/id_rsa.pub file to the *`opc_user_home`*/.ssh/authorized_keys file, with the following dependencies:

For the source database server:

* If the source database server is accessed with the root user, then no action is required.
* If the source database server is accessed through SSH, then add the contents of the *`zdm_installed_user_home`*/.ssh/id_rsa.pub file into the *`opc_user_home`*/.ssh/authorized_keys file on all of the source database servers. 

For the target database server:

* Because the target database server is on cloud only and access is through SSH, add the contents of the *`zdm_installed_user_home`*/.ssh/id_rsa.pub file into the *`opc_user_home`*/.ssh/authorized_keys file on *all* of the target database servers. 

Note that the `opc` user is a standard Oracle cloud user that is used to access database servers, but you can use any privileged user that has sudo privileges. You can also use different users for the source and target databases. 

4. Make sure that the source and target database server names are resolvable from the Zero Downtime Migration service host through either resolving name servers or alternate ways approved by your IT infrastructure.

One method of resolving source and target database server names is to add the source and target database server names and IP address details to the Zero Downtime Migration service host `/etc/hosts` file. 

In the following example, the IP address entries are shown as 192.x.x.x, but you must add your actual public IP addresses.
```
#OCI public IP two node RAC server details
192.0.2.1 ocidb1
192.0.2.2 ocidb2
#OCIC public IP two node RAC server details
192.0.2.3 ocicdb1
192.0.2.4 ocicdb2
```


Optionally, Zero Downtime Migration allows connectivity through bastion hosts for both logical and physical migrations.

5. Make certain that port 22 in the source and target database servers accept incoming connections from the Zero Downtime Migration service host.
6. Test the connectivity from the Zero Downtime Migration service host to all source and target database servers.
```
zdmuser> ssh -i zdm_service_host_private_key_file_location user@source/target_database_server_name
```


For example, 
```
zdmuser> ssh -i /home/zdmuser/.ssh/zdm_service_host.ppk opc@ocidb1
zdmuser> ssh -i /home/zdmuser/.ssh/zdm_service_host.ppk opc@ocicdb1
```


> **note:** SSH connectivity during Zero Downtime Migration operations requires direct, non-interactive access between the Zero Downtime Migration service host and the source and target database servers without the need to enter a passphrase. 

7. Disable TTY and verify that it is disabled for the SSH privileged user.

TTY needs to be turned off so that Zero Downtime Migration can run commands on the remote hosts non-interactively.

Because there are many ways to set sudo privileges, there are many ways to disable TTY for the `zdmuser`. As an example, you could set the following default in `/etc/sudoers` file. 
```
Defaults:zdmuser !requiretty
```


Run the following command to verify that TTY is disabled:
```
ssh -i zdm_service_host_private_key_file_location
user@source_database/target_database_server_name
"sudo_location_source/target_database /bin/sh -c date"
```


If TTY is disabled, the command above returns the date from the remote host without any errors.

Optionally, if SSH is configured to require TTY, the output shows an error, such as the following:
```
[opc@zdm-server ~]$ ssh -i /home/zdmuser/.ssh/zdm_service_host.ppk opc@ocidb1
"/usr/bin/sudo /bin/sh -c date"

sudo: sorry, you must have a tty to run sudo
```


> **note:** Usage of `sudo` is optional here. 




> **note:** See Also:[Zero Downtime Migration Port Requirements](configuring-required-connections1.md#GUID-E6F8EF13-03A5-43DD-8F98-182632C83CB3)
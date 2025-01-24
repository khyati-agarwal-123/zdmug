## Generate SSH Keys Without a Passphrase {#GUID-602216AD-4B59-441B-BA8D-AC7B333038FD}

You can generate a new SSH key without a passphrase if on the Zero Downtime Migration service host the authentication key pairs are not available without a passphrase for the Zero Downtime Migration software installed user.

> **note:** 

Currently, only the RSA key format is supported for configuring SSH connectivity, so use the `ssh-keygen` command, which generates both of the authentication key pairs (public and private). 

The following example shows you how to generate an SSH key pair for the Zero Downtime Migration software installed user. You can also use this command to generate the SSH key pair for the `opc` user. 

Run the following command on the Zero Downtime Migration service host.
```
zdmuser> ssh-keygen -m PEM -b 2048 -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/zdmuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/zdmuser/.ssh/id_rsa.
Your public key has been saved in /home/zdmuser/.ssh/id_rsa.pub.
The key fingerprint is:
c7:ed:fa:2c:5b:bb:91:4b:73:93:c1:33:3f:23:3b:30 zdmuser@zdm_service_host
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|                 |
|         . . .   |
|        S o . =  |
|         . E . * |
|            X.+o.|
|          .= Bo.o|
|          o+*o.  |
+-----------------+
```


This command generates the `id_rsa` and `id_rsa.pub` files in the `zdmuser` home, for example, `/home/zdmuser/.ssh`. 
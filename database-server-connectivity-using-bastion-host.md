## ADatabase Server Connectivity Using a Bastion Host {#GUID-E4DA4615-BE8D-4BAF-9123-5C1EF93FA379}

Zero Downtime Migration lets you configure connectivity to the source and target database servers through a bastion host for both physical and logical migration work flows.

Note that a bastion host cannot be used to connect to an Autonomous Database, except for JDBC connections.

Use the following sections to configure the appropriate parameters for physical and logical migrations that must connect to the source or target database server through a bastion host.

SSH Connection to Database Servers

Connecting database servers through a bastion host requires the following information:

* **Bastion Host IP Address and Source Database Server IP Address:** To connect to the database server through a bastion host, the bastion host IP address and the source node IP address are required. 

* **Bastion Port Number:** The port number defaults to 22 if not specified. 

* **Bastion User:** The bastion host user is only required if the user specified for the argument `zdmauth` plug-in is different from the user of the bastion host. The bastion user defaults to the user specified for the source `zdmauth` plug-in if the property is not specified. 

* **Bastion Identity File:** If the `*`SRC/TGT`*_BASTION_IDENTITY_FILE` parameter is not specified, the value defaults to the value specified for the `identity_file` argument of the `zdmauth` plug-in argument. 




Physical Migration Response File Parameters

Configure the following response file parameters for a physical migration.

**Source Database Server **

`SRC_BASTION_HOST_IP=`

`SRC_BASTION_PORT=`

`SRC_BASTION_USER=`

`SRC_BASTION_IDENTITY_FILE=`

`SRC_HOST_IP=`

**Target Database Server **

`TGT_BASTION_HOST_IP=`

`TGT_BASTION_PORT=`

`TGT_BASTION_USER=`

`TGT_BASTION_IDENTITY_FILE=`

`TGT_HOST_IP=`

Logical Migration Response File Parameters

Configure the following response file parameters for a logical migration.

**Source Database Server **

`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP=`

`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT=`

`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE=`

`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME=`

`SOURCECONTAINERDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP=`

**Target Database Server (including Autonomous Database)**

`TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IP=`

`TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_PORT=22`

`TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_IDENTITYFILE=`

`TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_USERNAME=`

`TARGETDATABASE_CONNECTIONDETAILS_BASTIONDETAILS_REMOTEHOSTIP=`
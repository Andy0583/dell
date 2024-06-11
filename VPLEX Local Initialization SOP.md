# VPLEX Local initialization SOP

### MGMT Server Name 及管理IP設定

預設帳密：service / Mi@Dim7T

```bash
service@VPLEX-1:~> **sudo /opt/emc/VPlex/tools/ipconfig/changehostname.py -n mgmt-1**

service@VPLEX-1:~> **hostname**
mgmt-1

service@VPLEX-1:~> **vplexcli**
Trying ::1...
Connected to localhost.
Escape character is '^]'.

****VPlexcli:/> **ll /management-server/ports/eth3**                                                                        
/management-server/ports/eth3: 
Name            Value
--------------  --------------------------------------------
address         192.168.0.62
auto-negotiate  -
duplex          -
gateway         192.168.0.254
inet6-address   fe80:0:0:0:21b:21ff:feb2:567e/64 Scope: Link
inet6-gateway   -
net-mask        255.255.255.0
speed           -
status          -

VPlexcli:/> **management-server set-ip -i 192.168.0.1/255.255.255.0 -g 192.168.0.254 eth3**

VPlexcli:/> **ll /management-server/ports/eth3**                                                                        
/management-server/ports/eth3: 
Name            Value
--------------  --------------------------------------------
address         192.168.0.1
auto-negotiate  -
duplex          -
gateway         192.168.0.254
inet6-address   fe80:0:0:0:21b:21ff:feb2:567e/64 Scope: Link
inet6-gateway   -
net-mask        255.255.255.0
speed           -
status          - 
```

### 初始化前置作業

- 初始化前需查看資訊是否正確
- 初始化完成後，才會帶起back-end、local-com

```bash
VPlexcli:/> **configuration connect-local-directors**
Connected to Plex firmware director-1-1-A.
Connected to Plex firmware director-1-1-B.

VPlexcli:/> **ll /engines/*/directors**
/engines/engine-1-1/directors:
Name            Director   Cluster   Commissioned  Operational  Communication
--------------  ID         ID        ------------  Status       Status
--------------  ---------  --------  ------------  -----------  ---------------
director-1-1-A  -1         -1        false         ok           unknown
director-1-1-B  -1         -1        false         ok           unknown

VPlexcli:/> **version -a**
What                                          Version         Info
--------------------------------------------  --------------  ----
Product Version                               6.2.0.04.00.07  -
SMSv2                                         162.4.0.7.0     -
Mgmt Server Base                              162.4.0.2       -
Mgmt Server Software                          162.4.0.13      -
/engines/engine-1-1/directors/director-1-1-B  162.4.0.13.0    -
/engines/engine-1-1/directors/director-1-1-A  162.4.0.13.0    -

VPlexcli:/> **ll engines/engine-1-1/directors/director-1-1-*/hardware/ports/**
/engines/engine-1-1/directors/director-1-1-A/hardware/ports:
Name     Address             Role       Port Status
-------  ------------------  ---------  -----------
A0-FC00  0x0000000000000000  front-end  down
A0-FC01  0x0000000000000000  front-end  down
A0-FC02  0x0000000000000000  front-end  down
A0-FC03  0x0000000000000000  front-end  down
A1-FC00  0x0000000000000000  back-end   down
A1-FC01  0x0000000000000000  back-end   down
A1-FC02  0x0000000000000000  back-end   down
A1-FC03  0x0000000000000000  back-end   down
A3-FC00  0x0000000000000000  local-com  down
A3-FC01  0x0000000000000000  local-com  down
A3-FC02  0x0000000000000000  -          down
A3-FC03  0x0000000000000000  -          down

/engines/engine-1-1/directors/director-1-1-B/hardware/ports:
Name     Address             Role       Port Status
-------  ------------------  ---------  -----------
B0-FC00  0x0000000000000000  front-end  down
B0-FC01  0x0000000000000000  front-end  down
B0-FC02  0x0000000000000000  front-end  down
B0-FC03  0x0000000000000000  front-end  down
B1-FC00  0x0000000000000000  back-end   down
B1-FC01  0x0000000000000000  back-end   down
B1-FC02  0x0000000000000000  back-end   down
B1-FC03  0x0000000000000000  back-end   down
B3-FC00  0x0000000000000000  local-com  down
B3-FC01  0x0000000000000000  local-com  down
B3-FC02  0x0000000000000000  -          down
B3-FC03  0x0000000000000000  -          down
```

### 開始進行初始化

```bash
VPlexcli:/> **configuration system-setup**
Verifying the pre-requisites for EZ-Setup...
Info: Able to contact director-1-1-A at 128.221.252.35.
Info: Able to contact director-1-1-B at 128.221.252.36.
Verification of the pre-requisites for EZ-Setup successful
Initializing CST...
CST initialization complete

  Welcome to the VPLEX EZ-Setup Wizard

  The Setup Wizard collects information about your configuration and uses it to set up
  your VPLEX implementation. Type exit at any prompt to stop. Your answers
  will be saved so you can restart the process if needed.

  Before starting, please read the VPLEX Installation and Setup Guide and the Release
  Notes.

  Continue? (yes/no) [**yes**]:
  Select a Configuration Option
  What would you like to configure?
  1.    Configure a VPLEX Local (single cluster).
  2.    Configure a VPLEX Metro (synchronous, 2 clusters).
  Select your configuration choice. (1-2): **1**

  You have selected to configure the only cluster of a VPLEX Local. If you want to
  configure a VPLEX Metro, please select option 2
  Are you sure you want to configure a VPLEX Local? (yes/no): **yes**

  Customize Login Banner (Optional)
  This VPLEX cluster can be configured to display a customized login banner. To
  configure this, you need to provide the login banner text file.
  Or, you may choose not to configure the login banner at this time. You may configure
  it at any time, using 'security set-login-banner' VPLEX CLI command.
  Would you like to configure the login banner? (yes/no)  [**no**]:

  Configure Authentication Service Provider (Optional)
  You may select to use your existing LDAP or Active Directory as a directory service to
  authenticate VPLEX users. To configure this, you will need the authentication service
  provider server information, and the security information to map the users.
  Or, you may choose not to configure an authentication service provider at this time.
  You may configure an authentication service provider for authentication at any time,
  using VPLEX CLI commands.
  Would you like to configure an authentication service provider to authenticate VPLEX
  users?
  (yes/no) [**no**]:

  Select EMC Notification Options
  By configuring EMC Event Notifications, EMC will be able to better serve you.
  Would you like to configure this cluster to send event notifications to EMC?
  (yes/no) [yes]: **no**

  Configure SNMP to Collect Performance Statistics
  The system can be configured to collect VPLEX performance statistics via the SNMP
  protocol. Statistics such as I/O operations and latencies as well as director memory
  statistics can be collected by issuing SNMP GET, GET-NEXT or GET-BULK messages to the
  SNMP agent. Additional details on the values collected can be found in the VPLEX
  Installation and Setup Guide.
  Before you get started you will need username, password & pass-phrase for SNMPv3 and
  the community string for SNMPv2C GET, GET-NEXT and GET-BULK messages. The default
  authorization level is read-only.
  Would you like to configure the cluster to run the SNMP Agent Service? (yes/no) [**no**]:

  Specify Certificate Configuration
  A Certificate Authority is needed to create a digital Host Certificate for the
  management server. For security reasons, this certificate must expire in 5 years or
  less. For more information, see the VPLEX Security Configuration Guide.
  A Host Certificate will be created to configure the web server. It also secures
  communication between management servers for VPLEX Geo configurations. For security
  reasons, this certificate must expire in 2 years or less. For more information, see
  the VPLEX Security configuration Guide.
  In addition to specifying certificate expirations, you will need to create certificate
  passphrases. These passphrases will be needed later in the setup process, so please
  make a note of it.
  Do you want to import host certificates? (yes/no). [**no**]:
  
  How many years should the CA certificate remain valid before expiring?
  EMC recommends 5 years (maximum is 5 years). [**5**]:

  How many years should the host certificate remain valid before expiring?
  EMC recommends 2 years (maximum is 2 years). [**2**]:

  Do you want to import web certificates? (yes/no). [**no**]:
  Certificates configuration information is saved.
  Review and Finish

  Review the configuration information below. If correct, enter yes (or simply
  accept the default and press Enter) to start the setup process. If the
  values are not correct, enter no to go back and make changes or to exit the
  setup.

    Cluster Information
      This is the only cluster in a VPLEX Local.

    Login Banner Information
      The Login Banner will not be configured.

    Authentication Service Provider Information
      An Authentication Service Provider will not be configured on this cluster.

    Performance Statistics
      The performance statistics will not be configured.

    Security Certificate Information
      The Certificate Authority will expire in 5 years.
      The Host Certificate will expire in 2 years.

  Would you like to run the setup process now? (yes/no): **yes**

Checking if the default password is in use...
*************************************************************************************************************************************************************
*                                                                                                                                                           *
*                                                                                                                                                           *
                                                                        NOTICE
                                  To ensure the highest levels of support, notify EMC support of any service password changes.
*                                                                                                                                                           *
*                                                                                                                                                           *
*************************************************************************************************************************************************************
Setting up environment for certificate creation...
Certificate creation started
Directors are connected...

host cert and key certificate import paths were not provided.
Default self-signed host certificates will be generated.

web cert and key certificate import paths were not provided.
Default self-signed web certificates will be generated.

Please create a passphrase (at least 8 characters) for the Certificate Authority Key.  Make a note of this passphrase as you will need it to configure a second cluster of a VPLEX Metro or Geo.
 Certificate Authority passphrase:

Re-enter the passphrase for the Certificate Authority Key:
New CA certificate /etc/ipsec.d/cacerts/strongswanCert.pem created

New CA key /etc/ipsec.d/private/strongswanKey.pem created

Please create a passphrase (at least 8 characters) for the Local Host Certificate Keys to be used to configure the VPN.  Make a note of this passphrase as you will need it later.
Host Certificate passphrase:

Re-enter:
New Host certificate request /etc/ipsec.d/reqs/hostCertReq.pem created

New Host certificate /etc/ipsec.d/certs/hostCert.pem created and signed by the CA Certificate /etc/ipsec.d/cacerts/strongswanCert.pem

Please create a passphrase (at least 8 characters) for the Web Host Certificate Keys to be used to configure the web server.  Make a note of this passphrase as you will need it later.
Web Host Certificate passphrase:

Re-enter:
New Host certificate request /etc/ipsec.d/reqs/webServerHostCertReq.pem created

New Host certificate /etc/ipsec.d/certs/webServerHostCertFile.pem created and signed by the CA Certificate /etc/ipsec.d/cacerts/strongswanCert.pem

addWebapp(/ui,/opt/emc/VPlex/tomcat/webapps/ui)
addWebapp(/apidoc,/opt/emc/VPlex/tomcat/webapps/apidoc)
addWebapp(/smsflex,/opt/emc/VPlex/tomcat/webapps/smsflex)
addWebapp(/cimom,/opt/emc/VPlex/tomcat/webapps/cimom)
addWebapp(/,/opt/emc/VPlex/tomcat/webapps/ROOT)
addWebapp(/WebHelp,/opt/emc/VPlex/tomcat/webapps/WebHelp)
addWebapp(/vplex,/opt/emc/VPlex/tomcat/webapps/vplex)
SSL protocols enabled for Server: TLSv1,TLSv1.1,TLSv1.2,SSLv2Hello
SSL protocols enabled for Client: TLSv1,TLSv1.1,TLSv1.2
https keystore: /var/log/VPlex/cli/.keystore
started web server on ports {'http': 49880, 'https': 49881}

Local Configuration of certificates completed successfully
Validating that the Certificate Authority and the Host Certificates have been created...
Certificate creation is completed successfully

Setting up environment for EMA Adaptor Configuration...
Directors are connected
EMA adaptor configuration started...
Verifying that all the pre-conditions for configuring the EMA adaptor are satisfied...
Verifying that the EMA adaptor file is present.
Verifying that the Cluster TLA is set.
Verification of the pre-conditions for the run complete.
Configuring EMA adaptor...
EMA adaptor configuration is completed successfully

setup : disconnecting from all directors
Disconnected from remote systems director-1-1-A, director-1-1-B.
Running the basic setup task
Verifying that the system is configured properly...
Connecting to all the directors and validating them...
Connected to Plex firmware director-1-1-A.

Connected to Plex firmware director-1-1-B.

Setting the cluster id on the directors and validating them...
Successfully updated 'cluster-id' for Director: director-1-1-A.
Successfully updated 'cluster-id' for Director: director-1-1-B.
Setting the director count and validating it...
Successfully updated 'director-count' for Director: director-1-1-A.
Successfully updated 'director-count' for Director: director-1-1-B.
Restarting GeoSynchrony software on all directors...
This may take a few moments...

For /engines/engine-1-1/directors/director-1-1-B:
Status    Description
--------  -----------
Started.

For /engines/engine-1-1/directors/director-1-1-A:
Status    Description
--------  -----------
Started.

Waiting for firmware to start...
...
Waiting for firmware run level to initialize...

Commissioning all the directors...
This may take a few moments...
Single-engine cluster configurations do not have local comm fibre channel switches.
Enabling COM and back-end ports...
Validating the health status of the directors...
This may take a few moments...
Setting the director product revision.
Basic setup task is completed successfully.

Running the UpdateCallHomePropertiesTask setup task
    Configuration Complete!

  The Setup Wizard has completed the automated portion of configuring your cluster. From
  this point, please follow the manual procedures defined in the Installation and Setup
  Guide.
```

### 配置System Volume

查看下列WWN，進行Back-end與Storage Zonnig，並配置Meta Volume 78G X 2 ＋ Backup Volume 78G X 2(為好區分Backup Volume使用80G)

```bash
VPlexcli:/> **ll engines/engine-1-1/directors/director-1-1-*/hardware/ports/**
/engines/engine-1-1/directors/director-1-1-A/hardware/ports:
Name     Address             Role       Port Status
-------  ------------------  ---------  -----------
A-CMI00  1                   -          down
A0-FC00  0x0000000000000000  front-end  down
A0-FC01  0x0000000000000000  front-end  down
A0-FC02  0x0000000000000000  front-end  down
A0-FC03  0x0000000000000000  front-end  down
A1-FC00  0x50001442801f1410  back-end   up
A1-FC01  0x50001442801f1411  back-end   up
A1-FC02  0x50001442801f1412  back-end   no-link
A1-FC03  0x50001442801f1413  back-end   no-link
A2-XG00  0.0.0.0|            wan-com    down
A2-XG01  0.0.0.0|            wan-com    down
A3-FC00  0x50001442801f1430  local-com  up
A3-FC01  0x50001442801f1431  local-com  up
A3-FC02  0x0000000000000000  -          down
A3-FC03  0x0000000000000000  -          down

/engines/engine-1-1/directors/director-1-1-B/hardware/ports:
Name     Address             Role       Port Status
-------  ------------------  ---------  -----------
B-CMI00  2                   -          down
B0-FC00  0x0000000000000000  front-end  down
B0-FC01  0x0000000000000000  front-end  down
B0-FC02  0x0000000000000000  front-end  down
B0-FC03  0x0000000000000000  front-end  down
B1-FC00  0x50001442901f1410  back-end   up
B1-FC01  0x50001442901f1411  back-end   up
B1-FC02  0x50001442901f1412  back-end   no-link
B1-FC03  0x50001442901f1413  back-end   no-link
B2-XG00  0.0.0.0|            wan-com    down
B2-XG01  0.0.0.0|            wan-com    down
B3-FC00  0x50001442901f1430  local-com  up
B3-FC01  0x50001442901f1431  local-com  up
B3-FC02  0x0000000000000000  -          down
B3-FC03  0x0000000000000000  -          down
```

### 設定Meta-Volume

因Lab環境電池有問題，所以cluster status有錯誤，請忽略

```bash
VPlexcli:/> **configuration show-meta-volume-candidates**
Name                                      Capacity  Vendor    IO Status  Type         Array Name
----------------------------------------  --------  --------  ---------  -----------  ---------------------------
VPD83T3:600601607b3156007553686678d09772  78G       DGC       alive      traditional  EMC-CLARiiON-DE406214258921
VPD83T3:600601607b31560075536866c39953cb  78G       DGC       alive      traditional  EMC-CLARiiON-DE406214258921
VPD83T3:600601607b315600a053686610eff495  80G       DGC       alive      traditional  EMC-CLARiiON-DE406214258921
VPD83T3:600601607b315600a1536866032fd40d  80G       DGC       alive      traditional  EMC-CLARiiON-DE406214258921

VPlexcli:/> **meta-volume create -n vplex-meta -d VPD83T3:600601607b3156007553686678d09772,VPD83T3:600601607b31560075536866c39953cb**
This may take a few minutes...
WARNING:
Creating a meta-volume from storage volumes on a single storage array is not recommended due to risk of possible data unavailability.
Adding another array leg when creating meta-volumes decreases risk of data unavailability. Do you wish to proceed?   (Yes/No)  
**yes**

Meta-volume vplex-meta is created at /clusters/cluster-1/system-volumes.

VPlexcli:/> **ll /clusters/cluster-1/system-volumes**
/clusters/cluster-1/system-volumes:
Name        Volume Type  Operational  Health  Active  Ready  Geometry  Component  Block     Block  Capacity  Slots
----------  -----------  Status       State   ------  -----  --------  Count      Count     Size   --------  -----
----------  -----------  -----------  ------  ------  -----  --------  ---------  --------  -----  --------  -----
vplex-meta  meta-volume  ok           ok      true    true   raid-1    2          20446976  4K     78G       64000

VPlexcli:/> **cluster status**
Cluster cluster-1
        operational-status:            ok
        transitioning-indications:
        transitioning-progress:
        health-state:                  major-failure
        health-indications:            engine-1-1 : director-1-1-A : stand-by-power-supply-A : The state of the specified FRU is faulted
                                       engine-1-1 : director-1-1-B : stand-by-power-supply-B : The state of the specified FRU is faulted
        local-com:                     ok
        license:                       License is not installed. Install valid license through either Unisphere or VPLEX CLI.
```

### 設定meta-data backup

依據每日備份時間設定（UTC時間）

```bash
VPlexcli:/> **configuration metadata-backup**

  Configuring Meta-data Backups

  To configure meta-data backups you will need to select two unclaimed volumes (78G or
  greater), preferably on two different arrays. Backups will occur automatically each
  day, at a time you specify. Please note: All times are UTC and are not based on the
  local time.
  Available Volumes for Meta-data Backup
Name                                      Capacity  Vendor    IO Status  Type         Array Name
----------------------------------------  --------  --------  ---------  -----------  ---------------------------
VPD83T3:600601607b315600a053686610eff495  80G       DGC       alive      traditional  EMC-CLARiiON-DE406214258921
VPD83T3:600601607b315600a1536866032fd40d  80G       DGC       alive      traditional  EMC-CLARiiON-DE406214258921

Please select volumes for meta-data backup, preferably from two different arrays (volume1,volume2):**VPD83T3:600601607b315600a053686610eff495,VPD83T3:60060160 7b315600a1536866032fd40d**

  What hour of the day (UTC) should the meta-data be backed up? (0..23): **15**
  What minute of the hour should the meta-data be backed up? (0..59): **55**

  VPLEX is configured to back up meta-data every day at 15:55 (UTC).

  Would you like to change the time the meta-data is backed up? [**no**]:
  You have chosen to configure the backup of the meta-data. Please note: All times are
  UTC and are not based on the local time.

  Review and Finish

  Review the configuration information below. If the values are correct, enter
  yes (or simply accept the default and press Enter) to start the
  setup process. If the values are not correct, enter no to go back and make
  changes or to exit the setup.

    Meta-data Backups
      Meta-data will be backed up every day at 15:55.
      The following volumes will be used for the backup
      :VPD83T3:600601607b315600a053686610eff495,VPD83T3:600601607b315600a1536866032fd40d

  Would you like to run the setup process now? [**yes**]:
Scheduling the backup of metadata...
Performing metadata backup (This will take a few minutes)
Successfully performed the initial backing up of metadata
Successfully scheduled the backing up of metadata
Successfully scheduled the metadata backup
The metadata backup has been successfully scheduled.

VPlexcli:/> **ll /clusters/cluster-1/system-volumes**
/clusters/cluster-1/system-volumes:
Name                                Volume Type  Operational  Health  Active  Ready  Geometry  Component  Block     Block  Capacity  Slots
----------------------------------  -----------  Status       State   ------  -----  --------  Count      Count     Size   --------  -----
----------------------------------  -----------  -----------  ------  ------  -----  --------  ---------  --------  -----  --------  -----
vplex-meta                          meta-volume  ok           ok      true    true   raid-1    2          20446976  4K     78G       64000
vplex-meta_backup_2024Jun11_140505  meta-volume  ok           ok      false   true   raid-1    1          20971264  4K     80G       64000
vplex-meta_backup_2024Jun11_140627  meta-volume  ok           ok      false   true   raid-1    1          20971264  4K     80G       64000
```

### 最後完成步驟

```bash
VPlexcli:/> **configuration complete-system-setup**
Initializing perpetual monitors on local directors.
This will take a few moments...

VPlexcli:/> **configuration enable-front-end-ports**
Verifying if the FE Ports are enabled
Running the enableFEPortTask setup task
Verifying if the FE Ports are enabled
Enable FE Ports task completed.
The Front End Ports for this cluster are now enabled.

VPlexcli:/> **ll engines/engine-1-1/directors/director-1-1-*/hardware/ports/**
/engines/engine-1-1/directors/director-1-1-A/hardware/ports:
Name     Address             Role       Port Status
-------  ------------------  ---------  -----------
A-CMI00  1                   -          down
A0-FC00  0x50001442801f1400  front-end  up
A0-FC01  0x50001442801f1401  front-end  up
A0-FC02  0x50001442801f1402  front-end  no-link
A0-FC03  0x50001442801f1403  front-end  no-link
A1-FC00  0x50001442801f1410  back-end   up
A1-FC01  0x50001442801f1411  back-end   up
A1-FC02  0x50001442801f1412  back-end   no-link
A1-FC03  0x50001442801f1413  back-end   no-link
A2-XG00  0.0.0.0|            wan-com    down
A2-XG01  0.0.0.0|            wan-com    down
A3-FC00  0x50001442801f1430  local-com  up
A3-FC01  0x50001442801f1431  local-com  up
A3-FC02  0x0000000000000000  -          down
A3-FC03  0x0000000000000000  -          down

/engines/engine-1-1/directors/director-1-1-B/hardware/ports:
Name     Address             Role       Port Status
-------  ------------------  ---------  -----------
B-CMI00  2                   -          down
B0-FC00  0x50001442901f1400  front-end  up
B0-FC01  0x50001442901f1401  front-end  up
B0-FC02  0x50001442901f1402  front-end  no-link
B0-FC03  0x50001442901f1403  front-end  no-link
B1-FC00  0x50001442901f1410  back-end   up
B1-FC01  0x50001442901f1411  back-end   up
B1-FC02  0x50001442901f1412  back-end   no-link
B1-FC03  0x50001442901f1413  back-end   no-link
B2-XG00  0.0.0.0|            wan-com    down
B2-XG01  0.0.0.0|            wan-com    down
B3-FC00  0x50001442901f1430  local-com  up
B3-FC01  0x50001442901f1431  local-com  up
B3-FC02  0x0000000000000000  -          down
B3-FC03  0x0000000000000000  -          down
```

### Dump Config檔

```bash
VPlexcli:/> **cd clusters/**

VPlexcli:/clusters> **configdump -c cluster-1 -f /var/log/VPlex/cli/cluster1_config.xml**
Initiating cluster configdump...
```

### 查看Web是否可登入

![Untitled](VPLEX%20Local%20%E5%88%9D%E5%A7%8B%E5%8C%96SOP%201b48104450d34198970414ccaf3cd88e/Untitled.png)
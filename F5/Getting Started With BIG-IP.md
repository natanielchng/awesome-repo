# Getting Started With BIG-IP --- Summary
_Last Update: 13 June 2025_


* [Intro to BIG-IP](#1-intro-to-the-big-ip-system)
* [Setting Up BIG-IP](#2-setting-up-big-ip)
* [Archiving BIG-IP](#3-archiving-big-ip-configuration)

## 1: Intro to the BIG-IP System

### BIG-IP Architecture
* **Default Deny System**
  * Any traffic that reaches the BIG-IP system is denied unless configured otherwise
* **Full Proxy Architecture**
  * Client connection with BIG-IP is fully independent of the server connection with BIG-IP
  * Client-side connection can have a different connection behaviour – protocols – than the server-side connection

### Internal Structure
* **Application Delivery with TMOS (Traffic Management OS)**
  * OS specifically designed for BIG-IP applications. Modules like Local Traffic Manager (LTM) and Access Policy Manager (APM) run on top of TMOS.
* **Administration with Linux and GUI**
  * Based on Linux OS and not involved in application delivery.  
  * Provides tools for administration (via self or management IP): TMOS Shell (TMSH) / GUI

## 2: Setting Up BIG-IP

### Management Interface
* **Management Interface Connection Methods**
  * `192.168.1.245/24` with no default gateway specified
  * Console
  * Use LCD Panel and controls

### Access
* **Root and Admin Accounts**
  * Root account credentials ---> access to CLI only; cannot be changed
  * Admin account credentials ---> access to all GUI, but no CLI access <br> can be changed with SSH Access and SSH IP Allow
* **Change Default Administrative Passwords**
  * Password becomes expired on new device installation.  <br> Note: For upgrades, passwords from previous configuration remain valid and are not marked as expired 

### BIG-IP License
* **License Activation Flow**
  * [1] Base registration key  
  * [2] base key generates dossier  
  * [3] send dossier to F5 license server  
  * [4] generate license  
  * [5] bring license back to BIG-IP  
  * [6] finish licensing process on BIG-IP
* **Licensing Types**
  * **Licensed** --> can be provisioned
  * **Unlicensed** --> can be provisioned but will not work
  * **Limited** --> can be provisioned but have limited functionality
* **Provisioning Options**
  * **nominal** --> minimum resources needed for module functionality (recommended settings)
  * **minimum** --> amount require to enable the module; no additional resources
  * **dedicated** --> module is the only one provisioned on the system. All other modules’ provisioning is set to “None”.

### Device Certificates
* SSL certificates are used for administrative tasks and inter-system communication
* BIG-IP self-signed (default) / CA-signed certificate (optional) <br> Stored in `/config/httpd/conf/ssl.crt/server.crt`
* **Considerations**
  * Correct storage location
  * Valid and current certificates
  * High availability configurations have unique device certificates

### Time
* **Hardware clock**
  * Keep time even when device is unplugged
  * for initialising the operating system clock during boot
  * `date MMDDhhmmYYYY.ss`
* **OS clock**
  * stores time according to time zone configured
* **NTP**
  * synchronising clocks of computer systems in a network for accurate time
  * Add NTP server onto BIG-IP “Time Server List” using IP address of FQDN

### Network and High Availability
* **VLANS**
  * logical subset of hosts on a LAN that operate in the same address space
  * **External VLAN** (where clients are)
  * **Internal VLAN** (where application servers are)
* **Self IP Address**
  * IP address that you associate with a VLAN to access hosts on that VLAN  
  * Each self-IP in VLAN are auto assigned a MAC address by BIG-IP system
  * **Static (non-floating)** <br> IP address that the BIG-IP system does not share with another BIG-IP system
  * **Floating** <br> IP that BIG-IP systems in a HA device group share

### DNS
* When using FQDN instead of IP addresses
* **DNS Lookup Server List** <br> name servers that BIG-IP queries

## 3: Archiving BIG-IP Configuration
* **UCS File**
  * Backup of configuration; compressed file with all important configuration information. 
  * Settings for UCS files can be overridden or customised.
  * TMSH can be used to create a UCS <br> `save /sys ucs backup.ucs`  <br> Saved in `/var/local/ucs/` <br> Restore in n TMSH --> `load /sys ucs restore.ucs`
* **Archiving Flow**
    1. BIG-IP system auto creates a rotating backup of current configuration and saving it as `cs backup.ucs`. Number of files in rotation is set in `cs_backup_rotate.conf`
    2.	BIG-IP replaces the stored configuration on the hard drive with contents of UCS file
    3.	New configuration is loaded into memory for operation. Reboot may be needed.
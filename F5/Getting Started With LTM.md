# Getting Started With Load Traffic Manager (LTM) --- Summary
_Last Update: 13 June 2025_

* [Device Service Clustering](#1-device-service-clustering-dsc)
* [Setup of Active-Standby Pair](#2-set-up-of-active-standby-pair)
* [Load Balancing Methods](#3-load-balancing-methods)
* [HA Configuration](#4-ha-configuration)
* [iRules Overview](#5-irules-overview)
* [OneConnect](#6-oneconnect)


## 1: Device Service Clustering (DSC)

### High Availability
* ensures continouts operation for a long time <br> Eliminates single point of failure
* **Methods**
  * Active / Standby
    * Traffic processed through active system until it fails <br> Once restored, traffic goes through active unit again
  * Active / Active

### Components of DSC
* **Device**
  * physical or virtual bigip system
* **Device Trust**
  * Devices trust one another via device certificates
* **Device Groups**
  * multiple devices that trust each other
  * sync configuration data for fail over
  * Up to 8 BIG-IP devices in a group
* **Failover Options**
  * Sync-only --> just sync configuration data
  * Sync-failover --> configuration data and traffic processing

### Traffic Group (TG)
* Related configuration objects that process application traffic for failover
* processing responsibility will "float" to another device in the group <br> (New device will manage said traffic group)
* Can only manually force a TG to standby on the device where it is currently active
* **TG Behaviour**
  * If device goes down, traffic group go to another standby unit
  * When device comes up again, it will just remain in standby
* **TG Management** <br> Sending of heartbeat packets between devices <br> If no heartbeat, failover event is triggered.

### ConfigSync
* Process of sync config data between devices in a device group
* All config objects must be the same and support on all devices

## 2: Set up of Active-Standby Pair
* **General Steps**
  * HA Options
  * HA VLAN <br> NTP (mitigate time discrepancies) <br> DNS if using FQDN
  * ConfigSync, Failover, Mirroring
  * Peer discovery (used in active-standby pairing)
  * Device trust and group

### Prep Activities
* Management interface
* same licensed modules on each system and provisioning
* Valid device certs
* FQDN provided
* System clocks in sync via NTP
* VLAN and Self-IPs configured
* Admin user and password configured
* Backup of each system

## 3: Load Balancing Methods

### Overview
* **Static: Prefined distribution pattern**
  * Round Robin (default)
  * Ratio
* **Dynamic: Predefined distribution metrics & as-needed distribution pattern**
  * least connections
  * weighted least connections
  * fastest
  * observed
  * predictive
  * dynamic ratio
  * least sessions
* **Failure Mechanisms**
  * priority based member activation
  * fallback host

### Ratio
* Use when application server capacity is unequal
* **Ratio (Member)**
  * Larger capacity member receives a higher ratio of connections
  * higher capacity application server --> assign higher ratio number
* **Ratio (Node)**
  * same concept as above

### Priority Based Member Activation
* Pool member in higher priority groups (higher number) accept traffic before members in lower priority groups
* **Priority Group Activation**
  * specify minium threshold number of members that must be available for load balancing
  * Given activation=X <br> If available members < X, all members are activated for load balancing <br> If available members >= X only use members in higher priority group for load balancing

## 4: HA Configuration

### Methods to configure HA
* GUI
* CLI
* ConfigSync / HA utility

## 5: iRules 

### Characteristics
* TCL based scripting language
* programmatic access to traffic for customised buisness logic
* executed when certain events occured related to virtual server
  * example: CLIENT_ACCEPTED(tcp handshake) state <br> CLIENT_DATE state (client sends data) <br> HTTP_REQUEST state when there is a http profile implemented
* iRule is attached to a virtual server <br> Upon attachment, iRule becomes active and will be executed every time connection to virtual server is accepted

### Constructs
* operators (includes starts_with contains ends_with)
* functions (findstr, getfield, substr)
* statements (if-else, switch log, pool)
* commands (`HTTP::uri` `AES::encrypt`)
* **syntax**
  * event driven
    ```
    when EVENT { ... }
    case sensitive
    if { condition } { ... }
    ```

## 6: OneConnect

### Purpose
* Keeping http connection alive between virtual server and application server has high overhead for the application server (context switching overhead)
* reduces concurrent connections and connection rate on back-end servers


### Behaviour
1. BIG-IP makes a load balancing decision
2. BIG-IP creates server side connection
3. HTTP request goes to server
4. Server-side connection temporarily left open for reuse by same or different client in a connection reuse pool
5. A different client makes a request and BIG-IP load balances to the same server
6. Uses the existing open connection without having to reopen a TCP connection

consideration: requests can appear to come from the same IP address

### Configuration
* OneConnect profile to be configured
* **source prefix length**
  * eligibility for reusing an open idle connection
  * default is `0.0.0.0` i.e. all 
  * source address is translated (SNAT) before oneconnect
* **max size**
  * max connections held in connection reuse pool
  * if pool is already full, close server side connection after response completed
* **max age**
  * how long a connection can stay open and idle before being closed and removed from connection reuse pool
* **max reuse**
  * max no. of times a connection can be reused


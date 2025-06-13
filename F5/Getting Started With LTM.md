# Getting Started With Load Traffic Manager (LTM) --- Summary
_Last Updated: 13 June 2025_


## Device Service Clustering (DSC)

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

### ConfigSync
* Process of sync config data between devices in a device group
* All config objects must be the same and support on all devices

## Set up of Active-Standby Pair
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

## Load Balancing Methods

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

# Managing Traffic Groups
* Sending of heartbeat packets between devices <br> If no heartbeat, failover event is triggered.
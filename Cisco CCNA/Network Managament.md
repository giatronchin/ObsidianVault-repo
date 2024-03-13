#cpt10 #cdp #lldp #ntp #snmp 

## CDP - Cisco Discovery Protocol (Layer 2)
CDP is enabled by default. For security reasons, it may be desirable to disable CDP on a network device globally, or per interface. With CDP, an attacker can gather valuable insight about the network layout, such as IP addresses, IOS versions, and types of devices.

To verify the status of CDP and display information about CDP, enter the **show cdp** command, as displayed in the example.

```
Router# show cdp
Global CDP information:
      Sending CDP packets every 60 seconds
      Sending a holdtime value of 180 seconds
      Sending CDPv2 advertisements is enabled
```

To ==enable CDP globally== for all the supported interfaces on the device, enter **cdp run** in the global configuration mode. ==CDP can be disabled for all the interfaces== on the device with the **no cdp run** command in the global configuration mode.

- Globally enable CDP --> `Router(config)# cdp run`
- Globally disable CDP --> `Router(config)# no cdp run`

```
Router(config)# no cdp run
Router(config)# exit
Router# show cdp
CDP is not enabled
Router# configure terminal
Router(config)# cdp run
```

- Locally enable CDP on specific interface --> `Switch(config-if)# cdp enable`
- Locally disable CDP on specific interface --> `Switch(config-if)# no cdp enable`

```
Switch(config)# interface gigabitethernet 0/0/1
Switch(config-if)# cdp enable
```

To verify the status of CDP and display a list of neighbors, use the **show cdp neighbors** command in the privileged EXEC mode.

```
Router# show cdp neighbors
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone,
                  D - Remote, C - CVTA, M - Two-port Mac Relay
 
Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
 
Total cdp entries displayed : 0
```

Use the **show cdp interface** command to display the interfaces that are CDP-enabled on a device. The status of each interface is also displayed.

```
Router# show cdp interface
GigabitEthernet0/0/0 is administratively down, line protocol is down
  Encapsulation ARPA
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/1 is up, line protocol is up
  Encapsulation ARPA
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/2 is down, line protocol is down
  Encapsulation ARPA
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
Serial0/1/0 is administratively down, line protocol is down
  Encapsulation HDLC
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
Serial0/1/1 is administratively down, line protocol is down
  Encapsulation HDLC
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0 is down, line protocol is down
  Encapsulation ARPA
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
 cdp enabled interfaces : 6
 interfaces up          : 1
 interfaces down        : 5
```

Network administrator can also use **show cdp neighbors detail** to discover the IP addresses of discovered neighbors devices.

---

## Link Layer Discovery Protocol (LLDP)

Depending on the device, LLDP may be enabled by default. To enable LLDP globally on a Cisco network device, enter the **lldp run** command in the global configuration mode. To disable LLDP, enter the **no lldp run** command in the global configuration mode.

Similar to CDP, LLDP can be configured on specific interfaces. However, LLDP must be configured separately to transmit and receive LLDP packets. 

1) To disable the sending of LLDP messages on an interface enter no lldp transmit  
2) To disable the receiving of LLDP messages on the interface enter no lldp receive 

To verify LLDP has been enabled on the device, enter the **show lldp** command in privileged EXEC mode.

```
Switch# conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)# lldp run
Switch(config)# interface gigabitethernet 0/1
Switch(config-if)# lldp transmit
Switch(config-if)# lldp receive
Switch(config-if)# end
Switch# show lldp
Global LLDP Information:
    Status: ACTIVE
    LLDP advertisements are sent every 30 seconds
    LLDP hold time advertised is 120 seconds
    LLDP interface reinitialisation delay is 2 seconds
```

With LLDP enabled, device neighbors can be discovered by using the **show lldp neighbors** command, as displayed in the output.

```
S1# show lldp neighbors
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
R1                  Fa0/5          117        R               Gi0/0/1
S2                  Fa0/1          112        B               Fa0/1
Total entries displayed: 2
```

--- 
## NTP - Network Time Protocol

NTP uses **UDP** (layer 4) port **123** and is documented in RFC 1305.

NTP networks use a hierarchical system of time sources. Each level in this hierarchical system is called a stratum. The stratum level is defined as the number of hop counts from the authoritative source. The synchronized time is distributed across the network by using NTP. The figure displays a sample NTP network.

NTP servers are arranged in three levels showing the three strata. Stratum 1 is connected to Stratum 0 clocks.

![[Pasted image 20240313090102.png]]

At startup, time is manually configured on most network devices. The **show clock** command displays the current time on the software clock. With the **detail** option, notice that the time source is user configuration. That means the time was manually configured with the **clock** command.

```
R1# show clock detail 
20:55:10.207 UTC Fri Nov 15 2019
Time source is user configuration
```

The **ntp server** _ip-address_ command is issued in global configuration mode to configure 209.165.200.225 as the NTP server for R1. To verify the time source is set to NTP, use the **show clock detail** command. Notice that now the time source is NTP.

```
R1(config)# ntp server 209.165.200.225 
R1(config)# end 
R1# show clock detail 
21:01:34.563 UTC Fri Nov 15 2019
Time source is NTP
```

The **show ntp associations** and **show ntp status** commands are used to verify that R1 is synchronized with the NTP server at 209.165.200.225. Notice that R1 is synchronized with a stratum 1 NTP server at 209.165.200.225, which is synchronized with a GPS clock. The **show ntp status** command displays that R1 is now a stratum 2 device that is synchronized with the NTP server at 209.165.220.225.

**Note**: The highlight **st** stands for stratum.

```
R1# show ntp associations   
  address         ref clock       st   when   poll reach  delay  offset   disp
*~209.165.200.225 .GPS.           1     61     64   377  0.481   7.480  4.261
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
 
R1# show ntp status 
Clock is synchronized, stratum 2, reference is 209.165.200.225
nominal freq is 250.0000 Hz, actual freq is 249.9995 Hz, precision is 2**19
ntp uptime is 589900 (1/100 of seconds), resolution is 4016
reference time is DA088DD3.C4E659D3 (13:21:23.769 PST Fri Nov 15 2019)
clock offset is 7.0883 msec, root delay is 99.77 msec
root dispersion is 13.43 msec, peer dispersion is 2.48 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000001803 s/s
system poll interval is 64, last update was 169 sec ago.
```

--- 
## SNMP - Simple Network Management Protocol


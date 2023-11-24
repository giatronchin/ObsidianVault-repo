#cisco #switch #security 

# Disable Unused Ports
All switch ports (interfaces) should be secured before the switch is deployed for production use. How a port is secured depends on its function.

A simple method that many administrators use to help secure the network from unauthorized access is to disable all unused ports on a switch.

On a single port, use the **interface** command to disable/enable the port:

```
S1(config-if)# shutdown

----------------------

S1(config-if)#no shutdown
```

To configure a range of ports, use the **interface range** command.
```
Switch(config)# **interface range** _type module/first-number – last-number_
```
For example, to shutdown ports for Fa0/8 through Fa0/24 on S1, you would enter the following command.

```
S1(config)# interface range fa0/8 - 24
S1(config-if-range)# shutdown
%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down
(output omitted)
%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down
S1(config-if-range)#
```

# Port Security
#portsecurity

The simplest and most effective method to prevent MAC address table overflow attacks is to enable port security. Port security limits the number of valid MAC addresses allowed on a port.

Port security can only be configured on **manually configured access ports** or **manually configured trunk ports**, no auto.

```
S1(config)# interface f0/1
S1(config-if)# switchport port-security
Command rejected: FastEthernet0/1 is a dynamic port.
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security
S1(config-if)# end
S1#
```

---

**Learning MAC addresses**

The switch can be configured to **learn about MAC addresses** on a secure port in one of three ways:

1. **Manually configured** - means that admin specify static MAC addresses allowed

```
Switch(config-if)# switchport port-security mac-address mac-address
```
2. **Dynamically Learned** - the current source MAC for the device connected to the port is automatically secured but is not added to the startup configuration. If the switch is rebooted, the port will have to re-learn the device’s MAC address.

3. **Dynamicall Learned - Sticky** - The administrator can enable the switch to dynamically learn the MAC address and “stick” them to the running configuration by using the following command:
```
Switch(config-if)# **switchport port-security mac-address sticky** 
```
Saving the running configuration will commit the dynamically learned MAC address to NVRAM.


---

 **Maximum Number of MAC addresses**

To set the maximum number of MAC addresses allowed on a port, use the following command:

```
Switch(config-if)# switchport port-security maximum value 
```
The default port security value is 1. The maximum number of secure MAC addresses that can be configured depends the switch and the IOS. For example the maximum can be 8192.

---
**Validation command**

The `show port-security interface` command to display the current port security settings for that interface:

```
S1# show port-security interface f0/1
Port Security              : Enabled
Port Status                : Secure-down
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0
S1#
```

Use the **show port-security interface** and the **show port-security address** command to verify the configuration.

```
*Mar  1 00:12:38.179: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up
*Mar  1 00:12:39.194: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#
S1(config)# interface fa0/1
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security
S1(config-if)# switchport port-security maximum 2
S1(config-if)# switchport port-security mac-address aaaa.bbbb.1234
S1(config-if)# switchport port-security mac-address sticky 
S1(config-if)# end
S1# show port-security interface fa0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 2
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : a41f.7272.676a:1
Security Violation Count   : 0

----------------------------------------------------------------------------

S1# show port-security address
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)    
----    -----------       ----                          -----   -------------
1    a41f.7272.676a    SecureSticky                  Fa0/1        -
1    aaaa.bbbb.1234    SecureConfigured              Fa0/1        -
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 1
Max  Addresses limit in System (excluding one mac per port) : 8192
S1#
```

The output of the **show port-security interface** command verifies that port security is enabled, there is a host connected to the port (i.e., Secure-up), a total of 2 MAC addresses will be allowed, and S1 has learned one MAC address statically and one MAC address dynamically (i.e., sticky).

The output of the **show port-security address** command lists the two learned MAC addresses.

To display all secure MAC addresses that are manually configured or dynamically learned on all switch interfaces, use the **show port-security address** command as shown in the example.

```
S1# show port-security address
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
   1    a41f.7272.676a    SecureSticky                  Fa0/1        -
   1    aaaa.bbbb.1234    SecureConfigured              Fa0/1        -
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 1
Max Addresses limit in System (excluding one mac per port) : 8192
S1#
```


---

**Aging**

Port security aging can be used to set the aging time for static and dynamic secure addresses on a port. Two types of aging are supported per port:

- **Absolute** - The secure addresses on the port are deleted after the specified aging time.
- **Inactivity** - The secure addresses on the port are deleted only if they are inactive for the specified aging time.

Use the **switchport port-security aging** command to enable or disable static aging for the secure port, or to set the aging time or type.

`Switch(config-if)# **switchport port-security aging** { **static** | **time** _time_ | **type** {**absolute** | **inactivity**}}`

|**Parameter**|**Description**|
|---|---|
|**static**|Enable aging for statically configured secure addresses on this port.|
|**time** _time_|Specify the aging time for this port. The range is 0 to 1440 minutes. If the time is 0, aging is disabled for this port.|
|**type absolute**|Set the absolute aging time. All the secure addresses on this port age out exactly after the time (in minutes) specified and are removed from the secure address list.|
|**type inactivity**|Set the inactivity aging type. The secure addresses on this port age out only if there is no data traffic from the secure source address for the specified time period.|

---

**Security Violation Policy**

If the MAC address of a device attached to the port differs from the list of secure addresses, then a port violation occurs.By default, the port enters the **error-disabled state**. The **port is physically shutdown** and placed in the error-disabled state, and **no traffic is sent or received on that port**.

<mark>Note</mark>: To **re-enable the port**, first use the **shutdown** command, then, use the **no shutdown** command to make the port operational.

To set the port security violation mode, use the following command:

`Switch(config-if)# switchport port-security violation { protect | restrict | shutdown}`

|Mode|Description|
|---|---|
|**shutdown** (default)|The port transitions to the error-disabled state immediately, turns off the port LED, and sends a syslog message. It increments the violation counter. When a secure port is in the error-disabled state, an administrator must re-enable it by entering the **shutdown** and **no shutdown** commands.|
|**restrict**|The port drops packets with unknown source addresses until you remove a sufficient number of secure MAC addresses to drop below the maximum value or increase the maximum value. This mode causes the Security Violation counter to increment and generates a syslog message.|
|**protect**|This is the least secure of the security violation modes. The port drops packets with unknown MAC source addresses until you remove a sufficient number of secure MAC addresses to drop below the maximum value or increase the maximum value. No syslog message is sent.|

|Violation Mode|Discards Offending Traffic|Sends Syslog Message|Increase Violation Counter|Shuts Down Port|
|---|---|---|---|---|
|Protect|Yes|No|No|No|
|Restrict|Yes|Yes|Yes|No|
|Shutdown|Yes|Yes|Yes|Yes|

# Mitigate VLAN Hopping Attacks

Use the following steps to mitigate VLAN hopping attacks:

**Step 1**: Disable DTP (auto trunking) negotiations on non-trunking ports by using the **switchport mode access** interface configuration command.

**Step 2**: Disable unused ports and put them in an unused VLAN.

**Step 3**: Manually enable the trunk link on a trunking port by using the **switchport mode trunk** command.

**Step 4**: Disable DTP (auto trunking) negotiations on trunking ports by using the **switchport nonegotiate** command.

**Step 5**: Set the native VLAN to a VLAN other than VLAN 1 by using the **switchport trunk native vlan** _vlan_number_ command.

# Mitigate DHCP Attacks
## DHCP Snooping

DHCP snooping does not rely on source MAC addresses. Instead, DHCP snooping determines whether DHCP messages are from an administratively configured trusted or untrusted source. It then filters DHCP messages and rate-limits DHCP traffic from untrusted sources.

Use the following steps to enable DHCP snooping to mitigate **DHCP Spoofing** attack:

**Step 1**. Enable DHCP snooping by using the **ip dhcp snooping** global configuration command.

**Step 2**. On trusted ports, use the **ip dhcp snooping trust** interface configuration command.

**Step 3**. Limit the number of DHCP discovery messages that can be received per second on untrusted ports by using the **ip dhcp snooping limit rate** interface configuration command.

**Step 4**. Enable DHCP snooping by VLAN, or by a range of VLANs, by using the **ip dhcp snooping** _vlan_ global configuration command.

**Example**
Notice that F0/5 is an untrusted port because it connects to a PC. F0/1 is a trusted port because it connects to the DHCP server.

![[Pasted image 20230614113744.png]]

Notice how DHCP snooping is first enabled. Then the *upstream interface to the DHCP server* is **explicitly trusted**. Next, the *range of FastEthernet ports from F0/5 to F0/24 are untrusted by default*, so a rate limit is set to six packets per second. Finally, DHCP snooping is enabled on VLANS 5, 10, 50, 51, and 52.

```
S1(config)# ip dhcp snooping
S1(config)# interface f0/1
S1(config-if)# ip dhcp snooping trust
S1(config-if)# exit
S1(config)# interface range f0/5 - 24
S1(config-if-range)# ip dhcp snooping limit rate 6
S1(config-if-range)# exit
S1(config)# ip dhcp snooping vlan 5,10,50-52
S1(config)# end
S1#
```

Use the **show ip dhcp snooping** privileged EXEC command to verify DHCP snooping and **show ip dhcp snooping binding** to view the clients that have received DHCP information, as shown in the example.

**Note**: DHCP snooping is also required by Dynamic ARP Inspection (DAI), which is the next topic

# Mitigate ARP Attacks

## Dynamic ARP Inspection
#dhcp-snooping #dai 

DHCP snooping is required. To mitigate the chances of ARP spoofing and ARP poisoning, follow these DAI implementation guidelines:

- Enable DHCP snooping globally.
- Enable DHCP snooping on selected VLANs.
- Enable DAI on selected VLANs.
- Configure trusted interfaces for DHCP snooping and ARP inspection.

It is generally advisable to configure all access switch ports as untrusted and to configure all uplink ports that are connected to other switches as trusted.

![[Pasted image 20230614115922.png]]


In the previous topology, S1 is connecting two users on VLAN 10. DAI will be configured to mitigate against ARP spoofing and ARP poisoning attacks.

As shown in the example, DHCP snooping is enabled because DAI requires the DHCP snooping binding table to operate. Next, DHCP snooping and ARP inspection are enabled for the PCs on VLAN10. The uplink port to the router is trusted, and therefore, is configured as trusted for DHCP snooping and ARP inspection.

```
S1(config)# ip dhcp snooping
S1(config)# ip dhcp snooping vlan 10
S1(config)# ip arp inspection vlan 10
S1(config)# interface fa0/24
S1(config-if)# ip dhcp snooping trust
S1(config-if)# ip arp inspection trust
```

DAI can also be configured to check for both destination or source MAC and IP addresses:

- **Destination MAC** - Checks the destination MAC address in the Ethernet header against the target MAC address in ARP body.
- **Source MAC** - Checks the source MAC address in the Ethernet header against the sender MAC address in the ARP body.
- **IP address** - Checks the ARP body for invalid and unexpected IP addresses including addresses 0.0.0.0, 255.255.255.255, and all IP multicast addresses.

The `ip arp inspection validate {[src-mac] [dst-mac] [ip]}` global configuration command is used to configure DAI to drop ARP packets when the IP addresses are invalid. It can be used when the MAC addresses in the body of the ARP packets do not match the addresses that are specified in the Ethernet header. Notice in the following example how only one command can be configured. Therefore, entering multiple `ip arp inspection validate` commands overwrites the previous command. To include more than one validation method, enter them on the same command line as shown and verified in the following output.

```
S1(config)# ip arp inspection validate ?
dst-mac  Validate destination MAC address
  ip       Validate IP addresses
  src-mac  Validate source MAC address
S1(config)# ip arp inspection validate src-mac
S1(config)# ip arp inspection validate dst-mac
S1(config)# ip arp inspection validate ip
S1(config)# do show run | include validate
ip arp inspection validate ip 
S1(config)# ip arp inspection validate src-mac dst-mac ip
S1(config)# do show run | include validate
ip arp inspection validate src-mac dst-mac ip 
S1(config)#
```


# Mitigate STP Attacks

#portfast #bpdu-guard

To mitigate Spanning Tree Protocol (STP) manipulation attacks, use PortFast and Bridge Protocol Data Unit (BPDU) Guard:

- **PortFast** - PortFast immediately brings an interface configured as an access port to the forwarding state from a blocking state, bypassing the listening and learning states. Apply to all end-user ports. PortFast should only be configured on ports attached to end devices.
- **BPDU Guard** - BPDU guard immediately error disables a port that receives a BPDU. Like PortFast, BPDU guard should only be configured on interfaces attached to end devices.ù

## PortFast
PortFast bypasses the STP listening and learning states to minimize the time that access ports must wait for STP to converge. If PortFast is enabled on a port connecting to another switch, there is a risk of creating a spanning-tree loop.

1. **PortFast can be enabled on an interface** by using the `spanning-tree portfast` interface configuration command. 
2. Alternatively, **Portfast can be configured globally** on all access ports by using the `spanning-tree portfast default` global configuration command.

To verify whether PortFast is enabled **globally** you can use either the `show running-config | begin span` command or the `show spanning-tree summary` command. To verify if PortFast is enabled on **interface**, use the `show running-config interface type/number` command, as shown in the following example. The `show spanning-tree interface type/number detail` command can also be used for verification.

Notice that when PortFast is enabled, warning messages are displayed.

```
S1(config)# interface fa0/1
S1(config-if)# switchport mode access
S1(config-if)# spanning-tree portfast
%Warning: portfast should only be enabled on ports connected to a single
 host. Connecting hubs, concentrators, switches, bridges, etc... to this
 interface when portfast is enabled, can cause temporary bridging loops.
 Use with CAUTION
%Portfast has been configured on FastEthernet0/1 but will only
 have effect when the interface is in a non-trunking mode.
S1(config-if)# exit
S1(config)# spanning-tree portfast default
%Warning: this command enables portfast by default on all interfaces. You
 should now disable portfast explicitly on switched ports leading to hubs,
 switches and bridges as they may create temporary bridging loops.
S1(config)# exit
S1# show running-config | begin span
spanning-tree mode pvst
spanning-tree portfast default
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport mode access
 spanning-tree portfast
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
! 
(output omitted)
S1#
```

## BPDU-Guard
Even though PortFast is enabled, the interface will still listen for BPDUs. Unexpected BPDUs might be accidental, or part of an unauthorized attempt to add a switch to the network.

If any BPDUs are received on a BPDU Guard enabled port, that port is put into error-disabled state. This means the port is shut down and must be manually re-enabled or automatically recovered through the **errdisable recovery cause bpduguard** global command.

1. BPDU Guard can be **enabled on a port** by using the ` spanning-tree bpduguard enable` interface configuration command. 
2. Alternatively, Use the `spanning-tree portfast bpduguard default` **global** configuration command to globally enable BPDU guard on all PortFast-enabled ports.

To display information about the state of spanning tree, use the `show spanning-tree summary` command. In the example, PortFast default and BPDU Guard are both enabled as the default state for ports configured as access mode.

**Note**: Always enable BPDU Guard on all PortFast-enabled ports.

```
S1(config)# interface fa0/1
S1(config-if)# spanning-tree bpduguard enable
S1(config-if)# exit
S1(config)# spanning-tree portfast bpduguard default
S1(config)# end
S1# show spanning-tree summary
Switch is in pvst mode
Root bridge for: none
Extended system ID           is enabled
Portfast Default             is enabled
PortFast BPDU Guard Default  is enabled
Portfast BPDU Filter Default is disabled
Loopguard Default            is disabled
EtherChannel misconfig guard is enabled
UplinkFast                   is disabled
BackboneFast                 is disabled
Configured Pathcost method used is short
(output omitted)
S1#
```
#switch #cisco
To prepare a switch for remote management access, the switch must have a **switch virtual interface (SVI)** configured with an IPv4 address and subnet mask or an IPv6 address and a prefix length for IPv6. The SVI is a virtual interface, not a physical port on the switch. Keep in mind that to manage the switch from a remote network, the switch must be configured with a default gateway. This is very similar to configuring the IP address information on host devices.

![[Pasted image 20230426144226.png]]

By default, the switch is configured to have its management controlled through VLAN 1. All ports are assigned to VLAN 1 by default. For security purposes, it is considered a best practice to use a VLAN other than VLAN 1 for the management VLAN, such as VLAN 99 in the example.

## Step 1

**Configure the Management Interface**

From VLAN interface configuration mode, an IPv4 address and subnet mask is applied to the management SVI of the switch. Specifically, SVI VLAN 99 will be assigned the 172.17.99.11/24 IPv4 address and the 2001:db8:acad:99::1/64 IPv6 address as shown.

**Note**: The SVI for VLAN 99 will not appear as “up/up” until VLAN 99 is created and there is a device connected to a switch port associated with VLAN 99.

**Note**: The switch may need to be configured for IPv6. For example, before you can configure IPv6 addressing on a Cisco Catalyst 2960 running IOS version 15.0, you will need to enter the global configuration command **sdm prefer dual-ipv4-and-ipv6 default** and then **reload** the switch.

| **Task**  | **IOS Commands** | 
| --- | --- |
|Enter global configuration mode.|S1# **configure terminal**|
|Enter interface configuration mode for the SVI.|S1(config)# **interface vlan 99**|
|Configure the management interface IPv4 address.|S1(config-if)# **ip address 172.17.99.11 255.255.255.0**|
|Configure the management interface IPv6 address|S1(config-if)# **ipv6 address 2001:db8:acad:99::11/64**|
|Enable the management interface.|S1(config-if)# **no shutdown**|
|Return to the privileged EXEC mode.|S1(config-if)# **end**|
|Save the running config to the startup config.|S1# **copy running-config startup-config**|

## Step 2

**Configure the Default Gateway**

The switch should be configured with a default gateway if it will be managed remotely from networks that are not directly connected.

**Note**: Because, it will receive its default gateway information from a router advertisement (RA) message, the switch does not require an IPv6 default gateway.

|**Task**|**IOS Commands**|
| --- | --- |
|Enter global configuration mode.|S1# **configure terminal**|
|Configure the default gateway for the switch.|S1(config)# **ip default-gateway 172.17.99.1**|
|Return to the privileged EXEC mode.|S1(config)# **end**|
|Save the running config to the startup config.|S1# **copy running-config startup-config**|

## **Step 3**

**Verify Configuration**

The **show ip interface brief** and **show ipv6 interface brief** commands are useful for determining the status of both physical and virtual interfaces. The output shown confirms that interface VLAN 99 has been configured with an IPv4 and IPv6 address.

**Note**: An IP address applied to the SVI is only for remote management access to the switch; this does not allow the switch to route Layer 3 packets.
```
S1# show ip interface brief
Interface IP-Address OK? Method Status Protocol Vlan
99 172.17.99.11 YES manual down down 
(output omitted) 

S1# show ipv6 interface brief 
Vlan99 [down/down]
FE80::C27B:BCFF:FEC4:A9C1
2001:DB8:ACAD:99::11
(output omitted)
```
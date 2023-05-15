Both this protocol allow host to dynamically configure an IPv6 **GUA** - *global unicast address*. GUA unlink **LLA** - *link local address* cannot be assigned by the host itself.

# SLAAC enabling
#slaac
![[Pasted image 20230515154341.png]]
- Verify IPv6 addresses on **R1**
```
R1# show ipv6 interface G0/0/1
GigabitEthernet0/0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Description: Link to LAN
  Global unicast address(es):
    2001:DB8:ACAD:1::1, subnet is 2001:DB8:ACAD:1::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
(output omitted)
R1#
```

- **Enable IPv6 routing**- Although the router interface has an IPv6 configuration, it is still not yet enabled to send RAs containing address configuration information to hosts using SLAAC. To enable the sending of RA messages, a router must join the IPv6 all-routers group using the **ipv6 unicast-routing** global config command, as show in the output.

```
R1(config)# ipv6 unicast-routing
R1(config)# exit
R1# 
```

- **Verify SLAAC enabled**
The IPv6 all-routers group responds to the IPv6 multicast address ff02::2. You can use the **show ipv6 interface** command to verify if a router is enabled as shown, in the output. An IPv6-enabled Cisco router sends RA messages to the IPv6 all-nodes multicast address ff02::1 every 200 seconds.

```
R1# show ipv6 interface G0/0/1 | section Joined
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
R1#
```

# DHCPv6

## Stateless DHCP Server

The stateless DHCPv6 server is only providing information that is identical for all devices on the network such as the IPv6 address of a DNS server. Stateless DHCPv6 is enabled on a router interface using the **ipv6 nd other-config-flag** interface configuration command. This sets the O flag to 1.

The highlighted output confirms the RA will tell receiving hosts to use stateless autoconfigure (A flag = 1) and contact a DHCPv6 server to obtain another configuration information (O flag = 1).
![[Pasted image 20230515165740.png]]

**Note:** You can use the **no ipv6 nd other-config-flag** to reset the interface to the default SLAAC only option (O flag = 0). 

```
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# end
R1#
R1# show ipv6 interface g0/0/1 | begin ND
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds (using 30000)
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
  Hosts use DHCP to obtain other configuration.
R1#
```


## Statefull DHCP Server
In this case, the RA message tells the client to obtain all addressing information from a stateful DHCPv6 server, **except the default gateway address which is the source IPv6 link-local address of the RA**.

This is known as stateful DHCPv6 because the DHCPv6 server maintains IPv6 state information.

![[Pasted image 20230515170200.png]]
Stateful DHCPv6 is enabled on a router interface using the **ipv6 nd managed-config-flag** interface configuration command. This sets the M flag to 1. The **ipv6 nd prefix default no-autoconfig** interface command disables SLAAC by setting the A flag to 0.

The highlighted output in the example confirms that the RA will tell the host to obtain all IPv6 configuration information from a DHCPv6 server (M flag = 1).

```
R1(config)# int g0/0/1
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# end
R1#
R1# show ipv6 interface g0/0/1 | begin ND
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds (using 30000)
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use DHCP to obtain routable addresses.
R1#
```

## Cisco Router as DHCPv6 Server 
### Stateless 
#stateless-dhcpv6-server
1. Enable IPv6 routing.  
```
R1(config)# ipv6 unicast-routing
R1(config)# 
```
2. Define a DHCPv6 pool name.  
```
R1(config)# ipv6 dhcp pool IPV6-STATELESS
R1(config-dhcpv6)#
```
3. Configure the DHCPv6 pool.  
```
R1(config-dhcpv6)# dns-server 2001:db8:acad:1::254
R1(config-dhcpv6)# domain-name example.com
R1(config-dhcpv6)# exit
R1(config)#
```
4. Bind the DHCPv6 pool to an interface - The router responds to stateless DHCPv6 requests on this interface with the information contained in the pool. The O flag needs to be manually changed from 0 to 1 using the interface command **ipv6 nd other-config-flag**.
```
R1(config)# interface GigabitEthernet0/0/1
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server IPV6-STATELESS
R1(config-if)# no shut
R1(config-if)# end
R1#
```
5. {CLient} Verify that the hosts have received IPv6 addressing information
```
C:\PC1> ipconfig /all
Windows IP Configuration
Ethernet adapter Ethernet0:
   Connection-specific DNS Suffix  . : example.com
   Description . . . . . . . . . . . : Intel(R) 82574L Gigabit Network Connection
   Physical Address. . . . . . . . . : 00-05-9A-3C-7A-00
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   IPv6 Address. . . . . . . . . . . : 2001:db8:acad:1:1de9:c69:73ee:ca8c (Preferred)
   Link-local IPv6 Address . . . . . : fe80::fb:1d54:839f:f595%21(Preferred)
   IPv4 Address. . . . . . . . . . . : 169.254.102.23 (Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::1%6
   DHCPv6 IAID . . . . . . . . . . . : 318768538
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-21-F3-76-75-54-E1-AD-DE-DA-9A
   DNS Servers . . . . . . . . . . . : 2001:db8:acad:1::254
   NetBIOS over Tcpip. . . . . . . . : Enabled
C:\PC1>
```

### Statefull
#stateful-dhcpv6-server
1. Enable IPv6 routing.  
```
R1(config)# ipv6 unicast-routing
R1(config)# 
```
2. Define a DHCPv6 pool name.  
```
R1(config)# ipv6 dhcp pool IPV6-STATEFUL
R1(config-dhcpv6)#
```
3. Configure the DHCPv6 pool. 
```
R1(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name example.com
R1(config-dhcpv6)#
```
4. Bind the DHCPv6 pool to an interface
```
R1(config)# interface GigabitEthernet0/0/1
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# ipv6 dhcp server IPV6-STATEFUL
R1(config-if)# no shut
R1(config-if)# end
R1#
```
5. {CLient} Verify that the hosts have received IPv6 addressing information

## Cisco Router as DHCPv6 Client

### Stateless 
#stateless-dhcpv6-client

1. Enable IPv6 routing.  
```
R3(config)# ipv6 unicast-routing
R3(config)#
```
2. Configure the client router to create an LLA.  
```
R3(config)# interface g0/0/1
R3(config-if)# ipv6 enable
R3(config-if)# 
```
3. Configure the client router to use SLAAC.  
```
R3(config-if)# ipv6 address autoconfig
R3(config-if)# end
R3#
```
4. Verify that the client router is assigned a GUA. 
```
R3# show ipv6 interface brief
GigabitEthernet0/0/0   [up/up]
    unassigned
GigabitEthernet0/0/1   [up/up]
    FE80::2FC:BAFF:FE94:29B1
    2001:DB8:ACAD:1:2FC:BAFF:FE94:29B1
Serial0/1/0            [up/up]
    unassigned
Serial0/1/1            [up/up]
    unassigned
R3#
```
5. Verify that the client router received other necessary DHCPv6 information.
```
R3# show ipv6 dhcp interface g0/0/1
GigabitEthernet0/0/1 is in client mode
  Prefix State is IDLE (0)
  Information refresh timer expires in 23:56:06
  Address State is IDLE
  List of known servers:
    Reachable via address: FE80::1
    DUID: 000300017079B3923640
    Preference: 0
    Configuration parameters:
      DNS server: 2001:DB8:ACAD:1::254
      Domain name: example.com
      Information refresh time: 0
  Prefix Rapid-Commit: disabled
  Address Rapid-Commit: disabled
R3#
```

### Stateful
#stateful-dhcpv6-client 
1. Enable IPv6 routing.  
```
R3(config)# ipv6 unicast-routing
R3(config)#
```
2. Configure the client router to create an LLA.  
```
R3(config)# interface g0/0/1
R3(config-if)# ipv6 enable
R3(config-if)# 
```
3. Configure the client router to use DHCPv6.  
```
R3(config-if)# ipv6 address dhcp
R3(config-if)# end
R3#
```
4. Verify that the client router is assigned a GUA.  
```
R3# show ipv6 interface brief
GigabitEthernet0/0/0   [up/up]
    unassigned
GigabitEthernet0/0/1   [up/up]
    FE80::2FC:BAFF:FE94:29B1
    2001:DB8:ACAD:1:2FC:BAFF:FE94:29B1
Serial0/1/0            [up/up]
    unassigned
Serial0/1/1            [up/up]
    unassigned
R3#
```
5. Verify that the client router received other necessary DHCPv6 information.
```
R3# show ipv6 dhcp interface g0/0/1
GigabitEthernet0/0/1 is in client mode
  Prefix State is IDLE (0)
  Information refresh timer expires in 23:56:06
  Address State is IDLE
  List of known servers:
    Reachable via address: FE80::1
    DUID: 000300017079B3923640
    Preference: 0
    Configuration parameters:
      DNS server: 2001:DB8:ACAD:1::254
      Domain name: example.com
      Information refresh time: 0
  Prefix Rapid-Commit: disabled
  Address Rapid-Commit: disabled
R3#
```

## Cisco Router as DHCPv6 Relay
#dhcpv6-relay

The command syntax to configure a router as a DHCPv6 relay agent is as follows:
```
Router(config-if)# ipv6 dhcp relay destination ipv6-address [interface-type interface-number]
```
![[Pasted image 20230515172508.png]]
This command is configured on the interface facing the DHCPv6 clients and specifies the DHCPv6 server address and egress interface to reach the server, as shown in the output. The egress interface is only required when the next-hop address is an LLA.

```
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 dhcp relay destination 2001:db8:acad:1::2 G0/0/0
R1(config-if)# exit
R1(config)#
```

### Verifu DHCPv6 Relay configuration

- The DHCPv6 relay agent can be verified using the **show ipv6 dhcp interface** command. This will verify that the G0/0/1 interface is in relay mode.

```
R1# show ipv6 dhcp interface
GigabitEthernet0/0/1 is in relay mode
  Relay destinations:
    2001:DB8:ACAD:1::2
    2001:DB8:ACAD:1::2 via GigabitEthernet0/0/0
R1#
```
- On R3, use the **show ipv6 dhcp binding command** to verify if any hosts have been assigned an IPv6 configuration.

Notice that a client link-local address has been assigned an IPv6 GUA. We can assume that this is PC1.

```
R3# show ipv6 dhcp binding
Client: FE80::5C43:EE7C:2959:DA68
  DUID: 0001000124F5CEA2005056B3636D
  Username : unassigned
  VRF : default
  IA NA: IA ID 0x03000C29, T1 43200, T2 69120
    Address: 2001:DB8:ACAD:2:9C3C:64DE:AADA:7857
            preferred lifetime 86400, valid lifetime 172800
            expires at Sep 29 2019 08:26 PM (172710 seconds)
R3#
```
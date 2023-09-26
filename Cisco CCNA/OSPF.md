#ospf #routing #router 

**Link** - An **interface** on a router *or* a **segment** between two routers *or* a segment between a router and a Ethernet LAN.

OSPFv2 is enabled using the **router ospf** _process-id_ global configuration mode command. After entering the **router ospf** _process-id_ command, the router enters router configuration mode, as indicated by the **R1(config-router)#** prompt.

```
R1(config)# router ospf 10
R1(config-router)# ?
  area                   OSPF area parameters
  auto-cost              Calculate OSPF interface cost according to bandwidth
  default-information    Control distribution of default information
  distance               Define an administrative distance
  exit                   Exit from routing protocol configuration mode
  log-adjacency-changes  Log changes in adjacency state
  neighbor               Specify a neighbor router
  network                Enable routing on an IP network
  no                     Negate a command or set its defaults
  passive-interface      Suppress routing updates on an interface
  redistribute           Redistribute information from another routing protocol
  router-id              router-id for this OSPF process
R1(config-router)#
```

An OSPF router ID is a 32-bit value, represented as an IPv4 address. The router ID is used to uniquely identify an OSPF router. All OSPF packets include the router ID of the originating router. Every router requires a router ID to participate in an OSPF domain. The router ID can be defined by an administrator or automatically assigned by the router. The router ID is used by an OSPF-enabled router to do the following:

- **Participate in the synchronization of OSPF databases** – During the Exchange State, the router with the highest router ID will send their database descriptor (DBD) packets first.
- **Participate in the election of the designated router (DR)** - In a multiaccess LAN environment, the router with the highest router ID is elected the DR. The routing device with the second highest router ID is elected the backup designated router (BDR).

### Configure a Loopback Interface as the Router ID

Instead of relying on physical interface, the router ID can be assigned to a loopback interface. Typically, the IPv4 address for this type of loopback interface should be configured using a 32-bit subnet mask (255.255.255.255). This effectively creates a host route. A 32-bit host route would not get advertised as a route to other OSPF routers.

```
R1(config-if)# interface Loopback 1
R1(config-if)# ip address 1.1.1.1 255.255.255.255
R1(config-if)# end
R1# show ip protocols | include Router ID
  Router ID 1.1.1.1
R1#
```

### Explicitly Configure a Router ID

```
R1(config)# router ospf 10
R1(config-router)# router-id 1.1.1.1
R1(config-router)# end
*May 23 19:33:42.689: %SYS-5-CONFIG_I: Configured from console by console
R1# show ip protocols | include Router ID
Router ID 1.1.1.1
R1#
```

### Modify a Router ID

After a router selects a router ID, an active OSPF router does not allow the router ID to be changed until the router is reloaded or the OSPF process is reset.

In example for R1, the configured router ID has been removed and the router reloaded. Notice that the current router ID is 10.10.1.1, which is the Loopback 0 IPv4 address. The router ID should be 1.1.1.1. Therefore, R1 is configured with the command **router-id 1.1.1.1**.

**Clearing the OSPF process** is the preferred method to reset the router ID.

```
R1# show ip protocols | include Router ID
Router ID 10.10.1.1
R1# conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)# router ospf 10 
R1(config-router)# router-id 1.1.1.1
% OSPF: Reload or use "clear ip ospf process" command, for this to take effect
R1(config-router)# end
R1# clear ip ospf process
Reset ALL OSPF processes? [no]: y
*Jun  6 01:09:46.975: %OSPF-5-ADJCHG: Process 10, Nbr 3.3.3.3 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Interface down or detached
*Jun  6 01:09:46.975: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
*Jun  6 01:09:46.981: %OSPF-5-ADJCHG: Process 10, Nbr 3.3.3.3 on GigabitEthernet0/0/1 from LOADING to FULL, Loading Done
*Jun  6 01:09:46.981: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from LOADING to FULL, Loading Done
R1# show ip protocols | include Router ID
Router ID 1.1.1.1
R1#
```

### Network Command - P2P Single Area OSPF

One type of network classified by OSPF is a **point-to-point network**. You can specify the interfaces that belong to a point-to-point network by configuring the **network** command. You can also configure OSPF directly on the interface with the **ip ospf** command, as we will see later.

Both commands are used to determine which interfaces participate in the routing process for an OSPFv2 area. The basic syntax for the **network** command is as follows:
```
Router(config-router)# network [network-address] [wildcard-mask] area [area-id]
```
- The **_network-address** and **wildcard-mask_** syntax is used to enable OSPF on interfaces. Any interfaces on a router that match the network address in the **network** command are enabled to send and receive OSPF packets.
- The **area** _area-id_ syntax refers to the OSPF area. When configuring single-area OSPFv2, the **network** command must be configured with the same _area-id_ value on all routers. Although any area ID can be used, it is good practice to use an area ID of 0 with single-area OSPFv2. This convention makes it easier if the network is later altered to support multiarea OSPFv2.

<mark>Wildcard Mask</mark>: The wildcard mask is typically the inverse of the subnet mask configured on that interface. In a subnet mask, binary 1 is equal to a match and binary 0 is not a match. In a wildcard mask, the reverse is true, as shown in here:

- **Wildcard mask bit 0** - Matches the corresponding bit value in the address.
- **Wildcard mask bit 1** - Ignores the corresponding bit value in the address.

The easiest method for calculating a wildcard mask is to subtract the network subnet mask from 255.255.255.255

![[Pasted image 20230926192926.png]]
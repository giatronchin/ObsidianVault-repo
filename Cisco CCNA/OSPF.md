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

### Manually configure OSPF Priority

Example of changing the R1 G0/0/0 interface priority from 1 to 255.

```
R1(config)# interface GigabitEthernet 0/0/0 
R1(config-if)# ip ospf priority 255 
R1(config-if)# end 
R1#
```

**Note**: OSPF DR and BDR elections are not pre-emptive. If a new router with a higher priority or higher router ID is added to the network after the DR and BDR election, the newly added router does not take over the DR or the BDR role. This is because those roles have already been assigned. The addition of a new router does not initiate a new election process.
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

The easiest method for calculating a wildcard mask is to subtract the network subnet mask from 255.255.255.255.

![[Pasted image 20230926192926.png]]

#### OSPF configuration with 'network' command
In the first example, the wildcard mask identifies the interface based on the network addresses. Any active interface that is configured with an IPv4 address belonging to that network will participate in the OSPFv2 routing process.

```
R1(config)# router ospf 10
R1(config-router)# network 10.10.1.0 0.0.0.255 area 0
R1(config-router)# network 10.1.1.4 0.0.0.3 area 0
R1(config-router)# network 10.1.1.12 0.0.0.3 area 0
R1(config-router)#
```

**Note**: Some IOS versions allow the subnet mask to be entered instead of the wildcard mask. The IOS then converts the subnet mask to the wildcard mask format.

As an alternative, the second example shows how OSPFv2 can be enabled by specifying the exact interface IPv4 address using a quad zero wildcard mask. Entering **network 10.1.1.5 0.0.0.0 area 0** on R1 tells the router to enable interface Gigabit Ethernet 0/0/0 for the routing process. As a result, the OSPFv2 process will advertise the network that is on this interface (10.1.1.4/30).

```
R1(config)# router ospf 10
R1(config-router)# network 10.10.1.1 0.0.0.0 area 0
R1(config-router)# network 10.1.1.5 0.0.0.0 area 0
R1(config-router)# network 10.1.1.14 0.0.0.0 area 0
R1(config-router)#
```

The advantage of specifying the interface is that the wildcard mask calculation is not necessary. Notice that in all cases, the **area** argument specifies area 0.


#### Configure OSPF Using the "ip ospf" Command

You can also configure OSPF directly on the interface instead of using the **network** command. To configure OSPF directly on the interface, use the **ip ospf** interface configuration mode command. The syntax is as follows:

`Router(config-if)# ip ospf [process-id] area [area-id]`

For R1, remove the network commandsby using the **no** form of the **network** commands. And then go to each interface and configure the **ip ospf** command, as shown in the command window.

```
R1(config)# router ospf 10
R1(config-router)# no network 10.10.1.1 0.0.0.0 area 0
R1(config-router)# no network 10.1.1.5 0.0.0.0 area 0
R1(config-router)# no network 10.1.1.14 0.0.0.0 area 0
R1(config-router)# interface GigabitEthernet 0/0/0
R1(config-if)# ip ospf 10 area 0
R1(config-if)# interface GigabitEthernet 0/0/1 
R1(config-if)# ip ospf 10 area 0
R1(config-if)# interface Loopback 0
R1(config-if)# ip ospf 10 area 0
R1(config-if)#
```

#### Passive Interface
By default, OSPF messages are forwarded out all OSPF-enabled interfaces.
Sending out unneeded messages on a LAN affects the network in three ways, as follows:

- **Inefficient Use of Bandwidth** - Available bandwidth is consumed transporting unnecessary messages.
- **Inefficient Use of Resources** - All devices on the LAN must process and eventually discard the message.
- **Increased Security Risk** - Without additional OSPF security configurations, OSPF messages can be intercepted with packet sniffing software. Routing updates can be modified and sent back to the router, corrupting the routing table with false metrics that misdirect traffic.

Use the `passive-interface` router configuration mode command to <mark>prevent the transmission of routing messages through a router interface, but still allow that network to be advertised to other routers</mark>. The configuration example identifies the R1 Loopback 0/0/0 interface as passive.

**Note**: The loopback interface in this example is representing an Ethernet network. In production networks, loopback interfaces do not require to be passive.

The `show ip protocols` command is then used to verify that the Loopback 0 interface is listed as passive. The interface is still listed under the heading, “Routing on Interfaces Configured Explicitly (Area 0)”, which means that this network is still included as a route entry in OSPFv2 updates that are sent to R2 and R3.

```
R1(config)# router ospf 10
R1(config-router)# passive-interface loopback 0
R1(config-router)# end
R1#
*May 23 20:24:39.309: %SYS-5-CONFIG_I: Configured from console by console
R1# show ip protocols
*** IP Routing is NSF aware ***
(output omitted)
Routing Protocol is "ospf 10"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 1.1.1.1
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
  Routing on Interfaces Configured Explicitly (Area 0):
    Loopback0
    GigabitEthernet0/0/1
    GigabitEthernet0/0/0
  Passive Interface(s):
    Loopback0
  Routing Information Sources:
    Gateway         Distance      Last Update
    3.3.3.3              110      01:01:48
    2.2.2.2              110      01:01:38
  Distance: (default is 110)
R1#
```

#### OSPF P2P Networks

By default, Cisco routers elect a DR and BDR on Ethernet interfaces, even if there is only one other device on the link. You can verify this with the `show ip ospf interface` command.

The DR/ BDR election process is unnecessary as there can only be two routers on the point-to-point network between R1 and R2. Notice in the output that the router has designated the network type as **BROADCAST**. To change this to a *point-to-point network*, use the interface configuration command `ip ospf network point-to-point` on all interfaces where you want to disable the DR/BDR election process. The OSPF neighbor adjacency status will go down for a few milliseconds.

```
R1(config)# interface GigabitEthernet 0/0/0
R1(config-if)# ip ospf network point-to-point
*Jun  6 00:44:05.208: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
*Jun  6 00:44:05.211: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from LOADING to FULL, Loading Done
R1(config-if)# interface GigabitEthernet 0/0/1
R1(config-if)# ip ospf network point-to-point 
*Jun  6 00:44:45.532: %OSPF-5-ADJCHG: Process 10, Nbr 3.3.3.3 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Interface down or detached
*Jun  6 00:44:45.535: %OSPF-5-ADJCHG: Process 10, Nbr 3.3.3.3 on GigabitEthernet0/0/1 from LOADING to FULL, Loading Done
R1(config-if)# end
R1# show ip ospf interface GigabitEthernet 0/0/0
GigabitEthernet0/0/0 is up, line protocol is up 
  Internet Address 10.1.1.5/30, Area 0, Attached via Interface Enable
  Process ID 10, Router ID 1.1.1.1, Network Type POINT_TO_POINT, Cost: 1
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           1         no          no            Base
  Enabled by interface config, including secondary ip addresses
  Transmit Delay is 1 sec, State POINT_TO_POINT
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:04
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 1/2/2, flood queue length 0
  Next 0x0(0)/0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 2
  Last flood scan time is 0 msec, maximum is 1 msec
  Neighbor Count is 1, Adjacent neighbor count is 1 
    Adjacent with neighbor 2.2.2.2
  Suppress hello for 0 neighbor(s)
R1#
```
Notice that the Gigabit Ethernet 0/0/0 interface now lists the network type as POINT_TO_POINT and that there is no DR or BDR on the link.

### Verify OSPF Router Roles


**R1 DROTHER**

The output generated by R1 confirms that the following:

1. R1 is not the DR or BDR, but is a DROTHER with a default priority of 1. (Line 7)
2. The DR is R3 with router ID 3.3.3.3 at IPv4 address 192.168.1.3, while the BDR is R2 with router ID 2.2.2.2 at IPv4 address 192.168.1.2. (Lines 8 and 9)
3. R1 has two adjacencies: one with the BDR and one with the DR. (Lines 20-22)

```
R1# show ip ospf interface GigabitEthernet 0/0/0
GigabitEthernet0/0/0 is up, line protocol is up 
  Internet Address 192.168.1.1/24, Area 0, Attached via Interface Enable
  Process ID 10, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 1
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           1         no          no            Base
  Enabled by interface config, including secondary ip addresses
  Transmit Delay is 1 sec, State DROTHER, Priority 1
  Designated Router (ID) 3.3.3.3, Interface address 192.168.1.3
  Backup Designated router (ID) 2.2.2.2, Interface address 192.168.1.2
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:07
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 1/1/1, flood queue length 0
  Next 0x0(0)/0x0(0)/0x0(0)
  Last flood scan length is 0, maximum is 1
  Last flood scan time is 0 msec, maximum is 1 msec
  Neighbor Count is 2, Adjacent neighbor count is 2 
    Adjacent with neighbor 2.2.2.2  (Backup Designated Router)
    Adjacent with neighbor 3.3.3.3  (Designated Router)
  Suppress hello for 0 neighbor(s)
R1#
```
### Verify DR/BDR Adjacencies

To verify the OSPFv2 adjacencies, use the **show ip ospf neighbor** command, as shown in the example for R1. The state of neighbors in multiaccess networks can be as follows:

- **FULL/DROTHER** - This is a DR or BDR router that is fully adjacent with a non-DR or BDR router. These two neighbors can exchange Hello packets, updates, queries, replies, and acknowledgments.
- **FULL/DR** - The router is fully adjacent with the indicated DR neighbor. These two neighbors can exchange Hello packets, updates, queries, replies, and acknowledgments.
- **FULL/BDR** - The router is fully adjacent with the indicated BDR neighbor. These two neighbors can exchange Hello packets, updates, queries, replies, and acknowledgments.
- **2-WAY/DROTHER** - The non-DR or BDR router has a neighbor relationship with another non-DR or BDR router. These two neighbors exchange Hello packets.

The normal state for an OSPF router is usually FULL. If a router is stuck in another state, it is an indication that there are problems in forming adjacencies. The only exception to this is the 2-WAY state, which is normal in a multiaccess broadcast network.

## Metrics - Cost

A metric gives indication of the overhead that is required to send packets across a certain interface. OSPF uses cost as a metric. A lower cost indicates a better path than a higher cost.

The Cisco cost of an interface is inversely proportional to the bandwidth of the interface. Therefore, a higher bandwidth indicates a lower cost. The formula used to calculate the OSPF cost is:

<mark>Cost = reference bandwidth / interface bandwidth</mark>

**The default reference bandwidth is 100,000,000.**

![[Pasted image 20231002121257.png]]
To correct this situation, you can:

- Adjust the reference bandwidth with the **auto-cost reference-bandwidth** command on each OSPF router.
- Manually set the OSPF cost value with the **ip ospf cost** command on necessary interfaces.

#### Adjust the Reference Bandwidth
Changing the reference bandwidth does not actually affect the bandwidth capacity on the link; rather, it simply affects the calculation used to determine the metric. To adjust the reference bandwidth, use the `auto-cost reference-bandwidth Mbps` router configuration command.

`Router(config-router)# auto-cost reference-bandwidth Mbps`

This command must be configured on every router in the OSPF domain. Notice that the value is expressed in Mbps; therefore, to adjust the costs for Gigabit Ethernet, use the command `auto-cost reference-bandwidth 1000`. 

**Note (back to default)**: To return to the default reference bandwidth, use the **auto-cost reference-bandwidth 100** command.

Whichever method is used, it is important **to apply the configuration to all routers in the OSPF routing domain.** The table shows the OSPF cost if the reference bandwidth is adjusted to accommodate 10 Gigabit Ethernet links. <mark>The reference bandwidth should be adjusted anytime there are links faster than FastEthernet (100 Mbps)</mark>.

| Interface Type | ReferenceBandwidth in bps | Default Bandwidth in bps|Cost|
|---|---|---|---|---|
|10 Gigabit Ethernet  <br>10 Gbps|10,000,000,000|÷|10,000,000,000|1|
|Gigabit Ethernet  <br>1 Gbps|10,000,000,000|÷|1,000,000,000|10|
|Fast Ethernet  <br>100 Mbps|10,000,000,000|÷|100,000,000|100|
|Ethernet  <br>10 Mbps|10,000,000,000|÷|10,000,000|1000|

Use the `show ip ospf interface g0/0/0` command to verify the current OSPFv2 cost assigned to the R1 GigabitEthernet 0/0/0 interface. 

**Note**: The **auto-cost reference-bandwidth** command must be configured consistently on all routers in the OSPF domain to ensure accurate route calculations.

#### Manually set OSPF Cost value
OSPF cost values can be manipulated to influence the route chosen by OSPF.

**Note**: Changing the cost of link may have undesired consequences. Therefore, adjusting interface cost values should only be configured when the outcome is fully understood.

To change the cost value reported by the local OSPF router to other OSPF routers, use the interface configuration command `ip ospf cost value`.

![[Pasted image 20231002122348.png]]

**Before**: in the current configuration, R1 is load balancing to the 10.1.1.8/30 network. It will send some traffic to R2 and some traffic to R3.
```
R1# show ip route ospf | begin 10
      10.0.0.0/8 is variably subnetted, 9 subnets, 3 masks
O        10.1.1.8/30 [110/20] via 10.1.1.13, 00:54:50, GigabitEthernet0/0/1
                     [110/20] via 10.1.1.6, 00:55:14, GigabitEthernet0/0/0
(output omitted)
R1#
```
Same cost on both path (load balancing traffic).

The administrator may want traffic to go through R2 and use R3 as a backup route in case the link between R1 and R2 goes down. The following example is the command to change the configuration for R1. We need to change cost of the loopback interfaces to 10 to simulate Gigabit Ethernet speeds. In addition, we will change the cost of the link between R2 and R3 to 30 so that this link is used as a backup link.
```
R1(config)# interface g0/0/1
R1(config-if)# ip ospf cost 30
R1(config-if)# interface lo0
R1(config-if)# ip ospf cost 10
R1(config-if)# end
R1#
```
**After**
```
R1# show ip route ospf | begin 10
      10.0.0.0/8 is variably subnetted, 9 subnets, 3 masks
O        10.1.1.8/30 [110/20] via 10.1.1.6, 01:18:25, GigabitEthernet0/0/0
O        10.10.2.0/24 [110/20] via 10.1.1.6, 00:04:31, GigabitEthernet0/0/0
O        10.10.3.0/24 [110/30] via 10.1.1.6, 00:03:21, GigabitEthernet0/0/0
R1#
```

## Timers

### Hello Interval
 OSPFv2 Hello packets are transmitted to multicast address 224.0.0.5 (all OSPF routers) every 10 seconds. This is the default timer value on multiaccess and point-to-point networks.
### Dead Interval
The Dead interval is the period that the router waits to receive a Hello packet before declaring the neighbor down. If the Dead interval expires before the routers receive a Hello packet, OSPF removes that neighbor from its link-state database (LSDB). The router floods the LSDB with information about the down neighbor out all OSPF-enabled interfaces. Cisco uses a default of 4 times the Hello interval. This is 40 seconds on multiaccess and point-to-point networks.

#verification**Hello** and **Dead** intervals are set to the default **10 seconds** and **40 seconds** respectively has shown in the output of the `show ip ospf interface g0/0/0` command.

```
R1# show ip ospf interface g0/0/0
GigabitEthernet0/0/0 is up, line protocol is up 
  Internet Address 10.1.1.5/30, Area 0, Attached via Interface Enable
  Process ID 10, Router ID 1.1.1.1, Network Type POINT_TO_POINT, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Enabled by interface config, including secondary ip addresses
  Transmit Delay is 1 sec, State POINT_TO_POINT
  
  ---> Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5 <---
	oob-resync timeout 40
    Hello due in 00:00:06
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 1/2/2, flood queue length 0
  Next 0x0(0)/0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1 
    Adjacent with neighbor 2.2.2.2
  Suppress hello for 0 neighbor(s)
R1#
```

#verification Use the **show ip ospf neighbor** command to see the Dead Time counting down from 40 seconds, as shown in the following example. By default, this value is refreshed every 10 seconds when R1 receives a Hello from the neighbor.

```
R1# show ip ospf neighbor 
Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           0   FULL/  -        00:00:35    10.1.1.13       GigabitEthernet0/0/1
2.2.2.2           0   FULL/  -        00:00:31    10.1.1.6        GigabitEthernet0/0/0
R1#
```



OSPFv2 Hello and Dead intervals can be modified manually using the following interface configuration mode commands:

`Router(config-if)# ip ospf hello-interval seconds`

`Router(config-if)# ip ospf dead-interval seconds`

Use the `no ip ospf hello-interval` and `no ip ospf dead-interval` commands to reset the intervals to their default.

## Default Route Propagation
#gateway #edge-router

Your network users will need to send packets out of your network to non-OSPF networks, such as the internet. This is where you will need to have a default static route that they can use. In the topology in the figure, R2 is connected to the internet and should propagate a default route to R1 and R3. The router connected to the internet is sometimes called the edge router or the gateway router. However, in OSPF terminology, the router located between an OSPF routing domain and a non-OSPF network is called the autonomous system boundary router (ASBR).

![[Pasted image 20231002165452.png]]
All that is required for R2 to reach the internet is a default static route to the service provider.

**Note**: In this example, a loopback interface with IPv4 address 64.100.0.1 is used to simulate the connection to the service provider.

To propagate a default route, the edge router (R2) must be configured with the following:

- A default static route using the `ip route 0.0.0.0 0.0.0.0 [next-hop-address | exit-intf]` command.
- The `default-information originate` router configuration command. <mark>This instructs R2 to be the source of the default route information and propagate the default static rute in OSPF updates.</mark>

In the following example, R2 is configured with a loopback to simulate a connection to the internet. **Then a default route is configured and propagated to all other OSPF routers in the routing domain.**

**Note**: When configuring static routes, best practice is to use the next-hop IP address. However, when simulating a connection to the internet, there is no next-hop IP address. Therefore, we use the _exit-intf_ argument

```
R2(config)# interface lo1
R2(config-if)# ip address 64.100.0.1 255.255.255.252 
R2(config-if)# exit
R2(config)# ip route 0.0.0.0 0.0.0.0 loopback 1
%Default route without gateway, if not a point-to-point interface, may impact performance
R2(config)# router ospf 10
R2(config-router)# default-information originate
R2(config-router)# end
R2#
```
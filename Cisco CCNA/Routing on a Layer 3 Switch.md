#cisco #routing #switch 

If VLANs are to be reachable by other Layer 3 devices, then they must be advertised using static or dynamic routing. To enable routing on a Layer 3 switch, a routed port must be configured.

**IMPORTANT**: A routed port is created on a Layer 3 switch by disabling the switchport feature on a Layer 2 port that is connected to another Layer 3 device. Specifically, configuring the <mark>no switchport</mark> interface configuration command on a Layer 2 port converts it into a Layer 3 interface. Then the interface can be configured with an IPv4 configuration to connect to a router or another Layer 3 switch.

![[Pasted image 20230503154115.png]]

#### Routing Configuration on a Layer 3 Switch (D1)
1. **Configure the routed port** - Notiche the use of `no switchport` command to disable switchport feature.
```
D1(config)# interface GigabitEthernet0/0/1
D1(config-if)# description routed Port Link to R1
D1(config-if)# no switchport
D1(config-if)# ip address 10.10.10.2 255.255.255.0
D1(config-if)# no shut
D1(config-if)# exit
D1(config)#
```
2. **Enable routing**
```
D1(config)# ip routing
D1(config)#
```
3. **Configure routing** - Configure routes advertisement via statis routes or dynamic routing protocol such OSPF in the example. (_Configure the OSPF routing protocol to advertise the VLAN 10 and VLAN 20 networks, along with the network that is connected to R1. Notice the message informing you that an adjacency has been established with R1.__)
```
D1(config)# router ospf 10
D1(config-router)# network 192.168.10.0 0.0.0.255 area 0
D1(config-router)# network 192.168.20.0 0.0.0.255 area 0
D1(config-router)# network 10.10.10.0 0.0.0.3 area 0
D1(config-router)# ^Z
D1#
*Sep 17 13:52:51.163: %OSPF-5-ADJCHG: Process 10, Nbr 10.20.20.1 on GigabitEthernet0/0/1 from LOADING to FULL, Loading Done
D1#
```
4. **Verify routing.** - Verify the routing table on D1. Notice that D1 now has a route to the 10.20.20.0/24 network.
```
D1# show ip route | begin Gateway
Gateway of last resort is not set
      10.0.0.0/8 is variably subnetted, 3 subnets, 3 masks
C        10.10.10.0/30 is directly connected, GigabitEthernet0/0/1
L        10.10.10.2/32 is directly connected, GigabitEthernet0/0/1
O        10.10.20.0/24 [110/2] via 10.10.10.1, 00:00:06, GigabitEthernet0/0/1
      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.10.0/24 is directly connected, Vlan10
L        192.168.10.1/32 is directly connected, Vlan10
      192.168.20.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.20.0/24 is directly connected, Vlan20
L        192.168.20.1/32 is directly connected, Vlan20
D1#
```

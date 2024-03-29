#cisco #vlan #trunk #intervlan #routing #dot1q #8021q 

**IEEE 802.1Q** (shorten *dot1q*) is the **main trunking protocol** that replace older ISL (*Inter-Switch Link* Cisco proprietary protocol).
# Router-on-a-Stick
![[Pasted image 20230503130519.png]]

Concept is to create subinterfaces (logical interfaces associated with the real G0/0/1 interface) and use them to route through different VLANs.

**Router R1 Subinterfaces**

| **Subinterface** | **VLAN** | **IP Address** |
| --- | --- | --- |
| G0/0/1.10 | 10 | 192.168.10.1/24 |
| G0/0/1.20 | 20 | 192.168.20.1/24 |
| G0/0/1.99 | 99 | 192.168.99.1/24 |

#### Configuration on S1
1. Create and name the VLANs
```
S1(config)# vlan 10
S1(config-vlan)# name LAN10
S1(config-vlan)# exit
S1(config)# vlan 20
S1(config-vlan)# name LAN20
S1(config-vlan)# exit
S1(config)# vlan 99
S1(config-vlan)# name Management
S1(config-vlan)# exit
S1(config)#
```
2. Create the management interface - Gateway point to R1 subinterface **G0/0/1.99**
```
S1(config)# interface vlan 99
S1(config-if)# ip add 192.168.99.2 255.255.255.0
S1(config-if)# no shut
S1(config-if)# exit
S1(config)# ip default-gateway 192.168.99.1
S1(config)#
```
3. Configure access ports
```
S1(config)# interface fa0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10
S1(config-if)# no shut
S1(config-if)# exit
S1(config)#
```
4. Configure trunking ports
- Link between two switches has to allow tagged traffic --> Trunk link (same config also on S2)
- Link between S1 and R1 has to allow tagged traffic --> Trunk link
```
S1(config)# interface fa0/1
S1(config-if)# switchport mode trunk
S1(config-if)# no shut
S1(config-if)# exit
S1(config)# interface fa0/5
S1(config-if)# switchport mode trunk
S1(config-if)# no shut
S1(config-if)# end
*Mar  1 00:23:43.093: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
*Mar  1 00:23:44.511: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to up
```

#### Configuration on S2
```
S2(config)# vlan 10
S2(config-vlan)# name LAN10
S2(config-vlan)# exit
S2(config)# vlan 20
S2(config-vlan)# name LAN20
S2(config-vlan)# exit
S2(config)# vlan 99
S2(config-vlan)# name Management
S2(config-vlan)# exit
S2(config)#
S2(config)# interface vlan 99
S2(config-if)# ip add 192.168.99.3 255.255.255.0
S2(config-if)# no shut
S2(config-if)# exit
S2(config)# ip default-gateway 192.168.99.1
S2(config)# interface fa0/18
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 20
S2(config-if)# no shut
S2(config-if)# exit
S2(config)# interface fa0/1
S2(config-if)# switchport mode trunk
S2(config-if)# no shut
S2(config-if)# exit
S2(config-if)# end
*Mar  1 00:23:52.137: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
```

#### Configuration on R1
1. The router-on-a-stick method requires you to create a subinterface for each VLAN to be routed.
**Note**: A subinterface is created using the `interface interface_id.subinterface_id` **global configuration** mode command
2. `encapsulation dot1q _vlan_id_ [native]` - This command configures the subinterface to respond to 802.1Q encapsulated traffic from the specified _vlan-id_. The **native** keyword option is only appended to set the native VLAN to something other than VLAN 1.
3. `ip address ip-address subnet-mask` - This command configures the IPv4 address of the subinterface. This address typically serves as the **default gateway** for the identified VLAN

**Note**: it is customary to match the subinterface number with the VLAN number.
```
R1(config)# interface G0/0/1.10
R1(config-subif)# description Default Gateway for VLAN 10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip add 192.168.10.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1.20
R1(config-subif)# description Default Gateway for VLAN 20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip add 192.168.20.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1.99
R1(config-subif)# description Default Gateway for VLAN 99
R1(config-subif)# encapsulation dot1Q 99
R1(config-subif)# ip add 192.168.99.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1
R1(config-if)# description Trunk link to S1
R1(config-if)# no shut
R1(config-if)# end
R1#
*Sep 15 19:08:47.015: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/1, changed state to down
*Sep 15 19:08:50.071: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/1, changed state to up
*Sep 15 19:08:51.071: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
R1#
```

#### Verification commands
- `ping
- `show ip route` - verify that the subinterfaces are appearing in the routing table of R1 (C - Connected)
- `show ip interface brief` - confirms that the subinterfaces have the correct IPv4 address configured, and that they are operational
- `show interfaces`
- `show interfaces trunk` - misconfiguration could also be on the trunking port of the switch therefore, it is also useful to verify the active trunk links on a Layer 2 switch

# Layer 3 Switches

Capabilities of a Layer 3 switch include the ability to do the following:
-   Route from one VLAN to another using multiple **switched virtual interfaces** (SVIs).
-   Convert a Layer 2 switchport to a Layer 3 interface (i.e., a routed port). A routed port is similar to a physical interface on a Cisco IOS router.
![[Pasted image 20230503153011.png]]


#### D1 VLAN IP Addresses
|VLAN Interface|IP Address|
| --- | --- |
|10|192.168.10.1/24|
|20|192.168.20.1/24|

#### Layer 3 Switch Configuration

1. **Create the VLANs**
```
D1(config)# vlan 10
D1(config-vlan)# name LAN10
D1(config-vlan)# vlan 20
D1(config-vlan)# name LAN20
D1(config-vlan)# exit
D1(config)#
```
2. **Create the SVI VLAN interfaces**
```
D1(config)# interface vlan 10
D1(config-if)# description Default Gateway SVI for 192.168.10.0/24
D1(config-if)# ip add 192.168.10.1 255.255.255.0
D1(config-if)# no shut
D1(config-if)# exit
D1(config)#
D1(config)# int vlan 20
D1(config-if)# description Default Gateway SVI for 192.168.20.0/24
D1(config-if)# ip add 192.168.20.1 255.255.255.0
D1(config-if)# no shut
D1(config-if)# exit
D1(config)#
*Sep 17 13:52:16.053: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up
*Sep 17 13:52:16.160: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan20, changed state to up
```
3. **Configure access ports**
```
D1(config)# interface GigabitEthernet1/0/6
D1(config-if)# description Access port to PC1
D1(config-if)# switchport mode access
D1(config-if)# switchport access vlan 10
D1(config-if)# exit
D1(config)#
D1(config)# interface GigabitEthernet1/0/18
D1(config-if)# description Access port to PC2
D1(config-if)# switchport mode access
D1(config-if)# switchport access vlan 20
D1(config-if)# exit
```
4. **Enable IP routing** - Finally, enable IPv4 routing with the **ip routing** global configuration command to allow traffic to be exchanged between VLANs 10 and 20. This commmand ust be configured to enable inter-VAN routing on a Layer 3 switch for IPv4.
```
D1(config)# ip routing
D1(config)#
```

## Inter-VLAN Routing Issues & Fixes

![[Pasted image 20230503161405.png]]
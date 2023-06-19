#router #cisco
Cisco routers and Cisco switches have many similarities. They support a similar modal operating system, similar command structures, and many of the same commands. In addition, both devices have similar initial configuration steps. For example, the following configuration tasks should always be performed. Name the device to distinguish it from other routers and configure passwords, as shown in the example.

```
Router> enable
Router# configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)# hostname R1
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# logging synchronous
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit
R1(config)# service password-encryption
R1(config)#
```

Configure a banner to provide legal notification of unauthorized access, as shown in the example.

```
R1(config)# banner motd 
# Enter TEXT message. End with a new line and the # *************************************************** 
WARNING: Unauthorized access is prohibited! *************************************************** 
#
```

### Interface Configuration

To be available, an interface must be:

-   **Configured with at least one IP address** - Use the `ip address ip-address subnet-mask` and the `ipv6 address ipv6-address/prefix` interface configuration commands.
-   **Activated** - By default, LAN and WAN interfaces are not activated (**shutdown**). To enable an interface, it must be activated using the **no shutdown** command. (This is similar to powering on the interface.) The interface must also be connected to another device (a hub, a switch, or another router) for the physical layer to be active.
-   **Description** - Optionally, the interface could also be configured with a short description of up to 240 characters. It is good practice to configure a description on each interface. On production networks, the benefits of interface descriptions are quickly realized as they are helpful in troubleshooting and in identifying a third-party connection and contact information.

### IPv4 Loopback Interfaces

Another common configuration of Cisco IOS routers is enabling a loopback interface.

**The loopback interface is a logical interface that is internal to the router. It is not assigned to a physical port and can never be connected to any other device.** It is considered a software interface that is automatically placed in an “up” state, as long as the router is functioning.

The loopback interface is useful in testing and managing a Cisco IOS device because it ensures that at least one interface will always be available. For example, it can be used for testing purposes, such as testing internal routing processes, by emulating networks behind the router.

Loopback interfaces are also commonly used in lab environments to create additional interfaces. For example, you can create multiple loopback interfaces on a router to simulate more networks for configuration practice and testing purposes. In this curriculum, we often use a loopback interface to simulate a link to the internet.

Enabling and assigning a loopback address is simple:

```Router(config)# interface loopback number ```

```Router(config-if)# ip address ip-address subnet-mask ```

Multiple loopback interfaces can be enabled on a router. The IPv4 address for each loopback interface must be unique and unused by any other interface, as shown in the example configuration of loopback interface 0 on R1.

```
R1(config)# interface loopback 0
R1(config-if)# ip address 10.0.0.1 255.255.255.0
R1(config-if)# exit
R1(config)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback0, changed state to up
```

### Verification of Directly Connected Networks

The following commands are especially useful to quickly identify the status of an interface:

-   `show ip interface brief` and `show ipv6 interface brief` - These display a summary for all interfaces including the IPv4 or IPv6 address of the interface and current operational status.
-   `show running-config interface interface-type number` - This displays the commands applied to the specified interface
- `show ip interface` and `show interfaces`
- `show ip route` and `show ipv6 route` - These display the contents of the IPv4 or IPv6 routing table stored in RAM. In Cisco IOS 15, active interfaces should appear in the routing table with two related entries identified by the code ‘**C**’ (Connected) or ‘**L**’ (Local). In previous IOS versions, only a single entry with the code ‘**C**’ will appear.
 
 Notice that the sources for each route are identified by a code. The code identifies how the route was learned. For instance, common codes include the following:

- **L** - Identifies the address assigned to a router interface. This allows the router to efficiently determine when it receives a packet for the interface instead of being forwarded.
- **C** - Identifies a directly connected network.
- **S** - Identifies a static route created to reach a specific network.
- **O** - Identifies a dynamically learned network from another router using the OSPF routing protocol.
- * - This route is a candidate for a default route.

**Filter Show Command Output**:
- **section** - Shows the entire section that starts with the filtering expression
- **include** - Includes all output lines that match the filtering expression
- **exclude** - Excludes all output lines that match the filtering expression
- **begin** - Shows all the output lines from a certain point, starting with the line that matches the filtering expression


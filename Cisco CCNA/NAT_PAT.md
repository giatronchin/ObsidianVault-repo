# NAT // PAT

**NAT** - Network Address Translation (translate source IPv4 address)
**PAT** - Port Address Translation (translate bot source IPv4 address + source port number)

NAT includes four types of addresses:

- *==Inside local address==* - The address of the source as seen from inside the network. This is typically a private IPv4 address. In the figure, the IPv4 address 192.168.10.10 is assigned to PC1. This is the inside local address of PC1.
- *==Inside global address==* - The address of source as seen from the outside network. This is typically a globally routable IPv4 address. In the figure, when traffic from PC1 is sent to the web server at 209.165.201.1, R2 translates the inside local address to an inside global address. In this case, R2 changes the IPv4 source address from 192.168.10.10 to 209.165.200.226. In NAT terminology, the inside local address of 192.168.10.10 is translated to the inside global address of 209.165.200.226.
- ==*Outside local address*== - (**Almost never used***) The address of the destination as seen from the inside network. In this example, PC1 sends traffic to the web server at the IPv4 address 209.165.201.1. While uncommon, this address could be different than the globally routable address of the destination.
- ==*Outside global address*== - The address of the destination as seen from the outside network. It is a globally routable IPv4 address assigned to a host on the internet. For example, the web server is reachable at IPv4 address 209.165.201.1. Most often the outside local and outside global addresses are the same.

When determining which type of address is used, it is important to remember that NAT terminology is always applied from the perspective of the device with the translated address:

- **Inside address** - The address of the device which is being translated by NAT.
- **Outside address** - The address of the destination device.

NAT also uses the concept of local or global with respect to addresses:

- **Local address** - A local address is any address that appears on the inside portion of the network.
- **Global address** - A global address is any address that appears on the outside portion of the network.

![[Pasted image 20240112114548.png]]
# Static NAT
The figure shows an inside network containing a web server with a private IPv4 address. Router R2 is configured with static NAT to allow devices on the outside network (internet) to access the web server. The client on the outside network accesses the web server using a public IPv4 address. Static NAT translates the public IPv4 address to the private IPv4 address.

![[Pasted image 20240112114302.png]]

**Step 1**. The first task is to ==create a mapping between the ***inside local address*** and the ***inside global addresses***.== For example, the 192.168.10.254 inside local address and the 209.165.201.5 inside global address in the figure are configured as a static NAT translation.

```
R2(config)# ip nat inside source static 192.168.10.254 209.165.201.5
```

**Step 2**. After the mapping is configured, the ==interfaces participating in the translation are configured as inside or outside relative to NAT==. In the example, the R2 Serial 0/1/0 interface is an inside interface and Serial 0/1/1 is an outside interface.
*This configuration is enough to make NAT operative in both direction (sending and receiving from webserver)*

```
R2(config)# interface serial 0/1/0
R2(config-if)# ip address 192.168.1.2 255.255.255.252
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface serial 0/1/1
R2(config-if)# ip address 209.165.200.1 255.255.255.252
R2(config-if)# ip nat outside
```

With this configuration in place, packets arriving on the inside interface of R2 (Serial 0/1/0) from the configured inside local IPv4 address (192.168.10.254) are translated and then forwarded towards the outside network. Packets arriving on the outside interface of R2 (Serial 0/1/1), that are addressed to the configured inside global IPv4 address (209.165.201.5), are translated to the inside local address (192.168.10.254) and then forwarded to the inside network.

### Verify Static NAT Configuration

==Verification==: This command shows active NAT translations
```
R2# show ip nat translations
Pro Inside global Inside local Outside local Outside global --- 209.165.201.5 192.168.10.254 --- --- Total number of translations: 1
```

During an active session the output may resemble the following:
```
R2# show ip nat translations
Pro  Inside global       Inside local        Outside local         Outside global
tcp  209.165.201.5       192.168.10.254      209.165.200.254       209.165.200.254
---  209.165.201.5       192.168.10.254        ---                   ---
Total number of translations: 2
```

==Statistics==: Another command that displays information about the total number of active translations, NAT configuration parameters, the number of addresses in the pool, and the number of addresses that have been allocated.
```
R2# show ip nat statistics
```

To clear up the counters before running the command is best to issue the following:
```
R2# clear ip nat statistics
```

# Dynamic NAT
Dynamic NAT automatically maps inside local addresses to inside global addresses. These inside global addresses are typically public IPv4 addresses. Dynamic NAT, like static NAT, requires the configuration of the inside and outside interfaces participating in NAT with the **ip nat inside** and **ip nat outside** interface configuration commands. However, where static NAT creates a permanent mapping to a single address, dynamic NAT uses a **pool of addresses**.

The example topology shown in the figure has an inside network using addresses from the RFC 1918 private address space. Attached to router R1 are two LANs, 192.168.10.0/24 and 192.168.11.0/24. Router R2, the border router, is configured for dynamic NAT using a pool of public IPv4 addresses 209.165.200.226 through 209.165.200.240. The figure shows an example topology where the NAT configuration allows translation for all hosts on the 192.168.0.0/16 network. This includes the 192.168.10.0 and 192.168.11.0 LANs when the hosts generate traffic that enters interface S0/1/0 and exits S0/1/1. The host inside local addresses are translated to an available pool address in the range of 209.165.200.226 to209.165.200.240.

![[Pasted image 20240112120212.png]]The pool of public IPv4 addresses (inside global address pool) is available to any device on the inside network on a first-come first-served basis. With dynamic NAT, a single inside address is translated to a single outside address. With this type of translation ==there must be enough addresses in the pool to accommodate all the inside devices needing concurrent access to the outside network==. If all addresses in the pool are in use, a device must wait for an available address before it can access the outside network.

**Note**: Translating between public and private IPv4 addresses is by far the most common use of NAT. However, NAT translations can occur between pair of IPv4 addresses.

**Step 1.** ==Define Pool of addresses==: Define the pool of addresses that will be used for translation using the **ip nat pool** command. This pool of addresses is typically a group of public addresses. The addresses are defined by indicating the starting IPv4 address and the ending IPv4 address of the pool. The **netmask** or **prefix-length** keyword indicates which address bits belong to the network and which bits belong to the host for that range of addresses.

In the scenario, define a pool of public IPv4 addresses under the pool name NAT-POOL1.

```
R2(config)# ip nat pool NAT-POOL1 209.165.200.226 209.165.200.240 netmask 255.255.255.224
```

**Step 2.** ==Define ACL==: Configure a standard ACL to identify (permit) only those addresses that are to be translated. An ACL that is too permissive can lead to unpredictable results. Remember there is an implicit **deny all** statement at the end of each ACL.

In the scenario, define which addresses are eligible to be translated.

```
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
```

*Note*: ACL ID is **1** and this will be referenced by the next binding command. 

**Step 3.** ==Bind ACL to the pool==: Bind the ACL to the pool, using the following command syntax:

==Router(config)# **ip nat inside source list** {_access-list-number_ | _access-list-name_} **pool** _pool-name_==

This configuration is used by the router to identify which devices (**list**) receive which addresses (**pool**). In the scenario, bind NAT-POOL1 with ACL 1.

```
R2(config)# ip nat inside source list 1 pool NAT-POOL1
```

**Step 4.** ==Identify & Config Inside Interface==: Identify which interfaces are inside, in relation to NAT; this will be any interface that connects to the inside network.

In the scenario, identify interface serial 0/1/0 as an inside NAT interface.

```
R2(config)# interface serial 0/1/0
R2(config-if)# ip nat inside
```

**Step 5.** ==Identify & Config Outside Interface==: Identify which interfaces are outside, in relation to NAT; this will be any interface that connects to the outside network.

In the scenario, identify interface serial 0/1/1 as the outside NAT interface.

```
R2(config)# interface serial 0/1/1
R2(config-if)# ip nat outside
```

### Verify Dynamic NAT Configuration
The output of the **show ip nat translations** command displays all static translations that have been configured and any dynamic translations that have been created by traffic.

```
R2# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 209.165.200.228    192.168.10.10      ---                ---
--- 209.165.200.229    192.168.11.10      ---                ---
R2#
```

Adding the **verbose** keyword displays additional information about each translation, including how long ago the entry was created and used.

```
R2# show ip nat translation verbose
Pro Inside global      Inside local       Outside local      Outside global
tcp 209.165.200.228    192.168.10.10      ---                ---
    create 00:02:11, use 00:02:11 timeout:86400000, left 23:57:48, Map-Id(In): 1, 
    flags: 
none, use_count: 0, entry-id: 10, lc_entries: 0
tcp 209.165.200.229    192.168.11.10      ---                ---
    create 00:02:10, use 00:02:10 timeout:86400000, left 23:57:49, Map-Id(In): 1, 
    flags: 
none, use_count: 0, entry-id: 12, lc_entries: 0
R2#
```

By default, ==translation entries time out after 24 hours==, unless the **timers have been reconfigured** with the **ip nat translation timeout** _timeout-seconds_ command in global configuration mode.

#### Clear timer of translation
To clear dynamic entries before the timeout has expired, use the **clear ip nat translation** privileged EXEC mode command as shown.

```
R2# clear ip nat translation *
R2# show ip nat translation
```

It is useful to clear the dynamic entries when testing the NAT configuration. The **clear ip nat translation** command can be used with keywords and variables to control which entries are cleared, as shown in the table. Specific entries can be cleared to avoid disrupting active sessions. Use the **clear ip nat translation *** privileged EXEC command to clear all translations from the table.

|**Command**|**Description**|
|---|---|
|**clear ip nat translation ***|Clears all dynamic address translation entries from the NAT translation table.|
|**clear ip nat translation inside**_global-ip local-ip_ [**outside** _local-ip global-ip_]|Clears a simple dynamic translation entry containing an inside translation or both inside and outside translation.|
|**clear ip nat translation** _protocol_**inside**_global-ip global-port local-ip local-port_ [ **outside**_local-ip local-port global-ip global-port_]|Clears an extended dynamic translation entry.|

**Note**: ==Only the dynamic translations are cleared from the table. Static translations cannot be cleared from the translation table.==

Another useful command, **show ip nat statistics**, displays information about the total number of active translations, NAT configuration parameters, the number of addresses in the pool, and how many of the addresses have been allocated.

```
R2# show ip nat statistics 
Total active translations: 4 (0 static, 4 dynamic; 0 extended)
Peak translations: 4, occurred 00:31:43 ago
Outside interfaces:
  Serial0/1/1
Inside interfaces: 
  Serial0/1/0
Hits: 47  Misses: 0
CEF Translated packets: 47, CEF Punted packets: 0
Expired translations: 5
Dynamic mappings:
-- Inside Source
[Id: 1] access-list 1 pool NAT-POOL1 refcount 4
 pool NAT-POOL1: netmask 255.255.255.224
	start 209.165.200.226 end 209.165.200.240
	type generic, total addresses 15, allocated 2 (13%), misses 0
(output omitted)
R2#
```

Alternatively, you can use the **show running-config** command and look for NAT, ACL, interface, or pool commands with the required values. Examine these carefully and correct any errors discovered. The example shows the NAT pool configuration.

```
R2# show running-config | include NAT
ip nat pool NAT-POOL1 209.165.200.226 209.165.200.240 netmask 255.255.255.224
ip nat inside source list 1 pool NAT-POOL1
```

# PAT
With PAT, multiple addresses can be mapped to one or to a few addresses, because each private address is also tracked by a port number. When a device initiates a TCP/IP session, it generates a TCP or UDP source port value, or a specially assigned query ID for ICMP, to uniquely identify the session. When the NAT router receives a packet from the client, it uses its source port number to uniquely identify the specific NAT translation. PAT ensures that devices use a different TCP port number for each session with a server on the internet. When a response comes back from the server, the source port number, which becomes the destination port number on the return trip, determines to which device the router forwards the packets. The PAT process also validates that the incoming packets were requested, thus adding a degree of security to the session.

![[Pasted image 20240112123938.png]]

## Scenario 1 - ISP assign a single public IP address

To configure PAT to use a single IPv4 address, ==simply add the keyword **overload** to the **ip nat inside source** command==. The rest of the configuration is the similar to static and dynamic NAT configuration except that with PAT, multiple hosts can use the same public IPv4 address to access the internet.

In the example, all hosts from network 192.168.0.0/16 (matching ACL 1) that send traffic through router R2 to the internet will be translated to IPv4 address 209.165.200.225 (IPv4 address of interface S0/1/1). The traffic flows will be identified by port numbers in the NAT table because the **overload** keyword is configured.

```
R2(config)# ip nat inside source list 1 interface serial 0/1/1 overload
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
R2(config)# interface serial0/1/0
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface Serial0/1/1
R2(config-if)# ip nat outside
```

## Scenario 2 - ISP assign multiple public IP addresses
An ISP may allocate more than one public IPv4 address to an organization. In this scenario the organization can configure PAT to use a pool of IPv4 public addresses for translation.

If a site has been issued more than one public IPv4 address, these addresses can be part of a pool that is used by PAT. The small pool of addresses is shared among a larger number of devices, with multiple hosts using the same public IPv4 address to access the internet. To configure PAT for a dynamic NAT address pool, simply add the keyword **overload** to the **ip nat inside source** command.

In the example, NAT-POOL2 is bound to an ACL to permit 192.168.0.0/16 to be translated. These hosts can share an IPv4 address from the pool because PAT is enabled with the keyword **overload**.

```
R2(config)# ip nat pool NAT-POOL2 209.165.200.226 209.165.200.240 netmask 255.255.255.224
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
R2(config)# ip nat inside source list 1 pool NAT-POOL2 overload
R2(config)# 
R2(config)# interface serial0/1/0
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface serial0/1/1
R2(config-if)# ip nat outside
R2(config-if)# end
R2#
```

Routers make routing decisions based on information in the packet header. Traffic entering a router interface is routed solely based on information within the routing table. The router compares the destination IP address with routes in the routing table to find the best match and then forwards the packet based on the best match route. That same process can be used to filter traffic using an access control list (ACL).

An ACL is a series of IOS commands that are used to filter packets based on information found in the packet header. By default, a router does not have any ACLs configured. However, when an ACL is applied to an interface, the router performs the additional task of evaluating all network packets as they pass through the interface to determine if the packet can be forwarded.

An ACL uses a sequential list of permit or deny statements, known as access control entries (ACEs).

**Note:** ACEs are also commonly called ACL statements.

When network traffic passes through an interface configured with an ACL, the router compares the information within the packet against each ACE, in sequential order, to determine if the packet matches one of the ACEs. This process is called packet filtering

Several tasks performed by routers require the use of ACLs to identify traffic.


|Task|Example|
|---|---|
|Limit network traffic to increase network performance|- A corporate policy prohibits video traffic on the network to reduce the network load.<br>- A policy can be enforced using ACLs to block video traffic.|
|Provide traffic flow control|- A corporate policy requires that routing protocol traffic be limited to certain links only.<br>- A policy can be implemented using ACLs to restrict the delivery of routing updates to only those that come from a known source.|
|Provide a basic level of security for network access|- Corporate policy demands that access to the Human Resources network be restricted to authorized users only.<br>- A policy can be enforced using ACLs to limit access to specified networks.|
|Filter traffic based on traffic type|- Corporate policy requires that email traffic be permitted into a network, but that Telnet access be denied.<br>- A policy can be implemented using ACLs to filter traffic by type.|
|Screen hosts to permit or deny access to network services|- Corporate policy requires that access to some file types (e.g., FTP or HTTP) be limited to user groups.<br>- A policy can be implemented using ACLs to filter user access to services.|
|Provide priority to certain classes of network traffic|- Corporate traffic specifies that voice traffic be forwarded as fast as possible to avoid any interruption.<br>- A policy can be implemented using ACLs and QoS services to identify voice traffic and process it immediately.|


**Packet filtering** controls access to a network by analyzing the incoming and/or outgoing packets and forwarding them or discarding them based on given criteria. Packet filtering can occur at Layer 3 or Layer 4.

Cisco routers support two types of ACLs:

- **Standard ACLs** - ACLs only filter at Layer 3 using the source IPv4 address only.
- **Extended ACLs** - ACLs filter at Layer 3 using the source and / or destination IPv4 address. They can also filter at Layer 4 using TCP, UDP ports, and optional protocol type information for finer control.

ACLs define the set of rules that give added control for packets that enter inbound interfaces, packets that relay through the router, and packets that exit outbound interfaces of the router.

**Note**: ACLs do not act on packets that originate from the router itself.

*Inbound ACL filtering*

An inbound ACL filters packets before they are routed to the outbound interface. An inbound ACL is efficient because it saves the overhead of routing lookups if the packet is discarded. If the packet is permitted by the ACL, it is then processed for routing. Inbound ACLs are best used to filter packets when the network attached to an inbound interface is the only source of packets that need to be examined.

*Outbound ACL filtering*

An outbound ACL filters packets after being routed, regardless of the inbound interface. Incoming packets are routed to the outbound interface and then they are processed through the outbound ACL. Outbound ACLs are best used when the same filter will be applied to packets coming from multiple inbound interfaces before exiting the same outbound interface.

When an ACL is applied to an interface, it follows a specific operating procedure. For example, here are the operational steps used when traffic has entered a router interface with an inbound standard IPv4 ACL configured.

1. The router extracts the source IPv4 address from the packet header.
2. The router starts at the top of the ACL and compares the source IPv4 address to each ACE in a sequential order.
3. When a match is made, the router carries out the instruction, either permitting or denying the packet, and the remaining ACEs in the ACL, if any, are not analyzed.
4. If the source IPv4 address does not match any ACEs in the ACL, the packet is discarded because there is an implicit deny ACE automatically applied to all ACLs.

The last ACE statement of an ACL is always an implicit deny that blocks all traffic. By default, this statement is automatically implied at the end of an ACL even though it is hidden and not displayed in the configuration.

**Note**: An ACL must have at least one permit statement otherwise all traffic will be denied due to the implicit deny ACE statement.

### Wildcard Mask

An IPv4 ACE uses a 32-bit wildcard mask to determine which bits of the address to examine for a match. Wildcard masks are also used by the Open Shortest Path First (OSPF) routing protocol.

A wildcard mask is similar to a subnet mask in that it uses the ANDing process to identify which bits in an IPv4 address to match. However, they differ in the way they match binary 1s and 0s. Unlike a subnet mask, in which binary 1 is equal to a match and binary 0 is not a match, in a wildcard mask, the reverse is true.

Wildcard masks use the following rules to match binary 1s and 0s:

- **Wildcard mask bit 0** - Match the corresponding bit value in the address
- **Wildcard mask bit 1** - Ignore the corresponding bit value in the address

The table lists some examples of wildcard masks and what they would identify.

|**Wildcard Mask**|**Last Octet (in Binary)**|**Meaning (0 - match, 1 - ignore)**|
|---|---|---|
|**0.0.0.0**|**00000000**|Match all octets.|
|**0.0.0.63**|**00111111**|- Match the first three octets<br>- Match the two left most bits of the last octet<br>- Ignore the last 6 bits|
|**0.0.0.15**|**00001111**|- Match the first three octets<br>- Match the four left most bits of the last octet<br>- Ignore the last 4 bits of the last octet|
|**0.0.0.252**|**11111100**|- Match the first three octets<br>- Ignore the six left most bits of the last octet<br>- Match the last two bits|
|**0.0.0.255**|**11111111**|- Match the first three octet<br>- Ignore the last octet|

**Wildcard to Match a Host** - the wildcard mask is used to match a specific host IPv4 address

Assume ACL 10 needs an ACE that only permits the host with IPv4 address 192.168.1.1. Recall that “0” equals a match and “1” equals ignore. To match a specific host IPv4 address, a wildcard mask consisting of all zeroes (i.e., 0.0.0.0) is required.

The table lists in binary, the host IPv4 address, the wildcard mask, and the permitted IPv4 address.

The 0.0.0.0 wildcard mask stipulates that every bit must match exactly. Therefore, when the ACE is processed, the wildcard mask will permit only the 192.168.1.1 address. The resulting ACE in ACL 10 would be **access-list 10 permit 192.168.1.1 0.0.0.0.**

---

**Wildcard Mask to Match an IPv4 Subnet** 

ACL 10 needs an ACE that permits all hosts in the 192.168.1.0/24 network. The wildcard mask 0.0.0.255 stipulates that the very first three octets must match exactly but the fourth octet does not.

The table lists in binary, the host IPv4 address, the wildcard mask, and the permitted IPv4 addresses.

When processed, the wildcard mask 0.0.0.255 permits all hosts in the 192.168.1.0/24 network. The resulting ACE in ACL 10 would be **access-list 10 permit 192.168.1.0 0.0.0.255.**

---

**Wildcard Mask to Match an IPv4 Address Range**

ACL 10 needs an ACE that permits all hosts in the 192.168.16.0/24, 192.168.17.0/24, …, 192.168.31.0/24 networks. The wildcard mask 0.0.15.255 would correctly filter that range of addresses.

The table lists in binary the host IPv4 address, the wildcard mask, and the permitted IPv4 addresses.

The highlighted wildcard mask bits identify which bits of the IPv4 address must match. When processed, the wildcard mask 0.0.15.255 permits all hosts in the 192.168.16.0/24 to 192.168.31.0/24 networks. The resulting ACE in ACL 10 would be **access-list 10 permit 192.168.16.0 0.0.15.255.**

---

Cisco IOS provides two keywords to identify the most common uses of wildcard masking.

The two keywords are:
- **host** - This keyword substitutes for the 0.0.0.0 mask. This mask states that all IPv4 address bits must match to filter just one host address.
- **any** - This keyword substitutes for the 255.255.255.255 mask. This mask says to ignore the entire IPv4 address or to accept any addresses.

IMPORTANT

Arouter interface can have at maximum:
- one outbound IPv4 ACL
- one inbound IPv4 ACL
- one inbound IPv6 ACL
- one outbound IPv6 ACL


#### Best practices

The table presents guidelines that form the basis of an ACL best practices list.

|**Guideline**|**Benefit**|
|---|---|
|Base ACLs on the organizational security policies.|This will ensure you implement organizational security guidelines.|
|Write out what you want the ACL to do.|This will help you avoid inadvertently creating potential access problems.|
|Use a text editor to create, edit, and save all of your ACLs.|This will help you create a library of reusable ACLs.|
|Document the ACLs using the **remark** command.|This will help you (and others) understand the purpose of an ACE.|
|Test the ACLs on a development network before implementing them on a production network.|This will help you avoid costly errors.|

## Standard vs Extended ACLs

There are two types of IPv4 ACLs:

- **Standard ACLs** - These permit or deny packets based only on the source IPv4 address.
- **Extended ACLs** - These permit or deny packets based on the source IPv4 address and destination IPv4 address, protocol type, source and destination TCP or UDP ports and more


E.g **Standard** ACL 10 permits hosts on the source network 192.168.10.0/24. Because of the implied "deny any" at the end, all traffic except for traffic coming from the 192.168.10.0/24 network is blocked with this ACL
```
R1(config)# access-list 10 permit 192.168.10.0 0.0.0.255
R1(config)#
```

E.g **Extended** ACL 100 permits traffic originating from any host on the 192.168.10.0/24 network to any IPv4 network if the destination host port is 80 (HTTP).

```
R1(config)# access-list 100 permit tcp 192.168.10.0 0.0.0.255 any eq www
R1(config)#
```

**Numbered ACLs**

ACLs number 1 to 99, or 1300 to 1999 are standard ACLs while ACLs number 100 to 199, or 2000 to 2699 are extended ACLs, as shown in the output.

```
R1(config)# access-list ?
  <1-99>       IP standard access list
  <100-199>    IP extended access list
  <1100-1199>  Extended 48-bit MAC address access list
  <1300-1999>  IP standard access list (expanded range)
  <200-299>    Protocol type-code access list
  <2000-2699>  IP extended access list (expanded range)
  <700-799>    48-bit MAC address access list
  rate-limit   Simple rate-limit specific access list
  template     Enable IP template acls
R1(config)# access-list
```

**Named ACLs**

Named ACLs is the preferred method to use when configuring ACLs. Specifically, standard and extended ACLs can be named to provide information about the purpose of the ACL. For example, naming an extended ACL FTP-FILTER is far better than having a numbered ACL 100.

The **ip** **access-list** global configuration command is used to create a named ACL, as shown in the following example.

```
R1(config)# ip access-list extended FTP-FILTER
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq ftp
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq ftp-data
R1(config-ext-nacl)#
```

The following summarizes the rules to follow for named ACLs.

- Assign a name to identify the purpose of the ACL.
- Names can contain alphanumeric characters.
- Names cannot contain spaces or punctuation.
- It is suggested that the name be written in CAPITAL LETTERS.
- Entries can be ad

## Where to Place ACLs

Every ACL should be placed where it has the greatest impact on efficiency.
Assume the objective to prevent **traffic originating in the 192.168.10.0/24 network from reaching the 192.168.30.0/24 network**.
![[Pasted image 20231212170115.png]]**Extended ACLs should be located as close as possible to the source of the traffic to be filtered**. This way, undesirable traffic is denied close to the source network without crossing the network infrastructure.

**Standard ACLs should be located as close to the destination as possible**. If a standard ACL was placed at the source of the traffic, the "permit" or "deny" will occur based on the given source address no matter where the traffic is destined.

## Standard ACL Placement Example
In the figure, the administrator wants to prevent traffic originating in the 192.168.10.0/24 network from reaching the 192.168.30.0/24 network.
![[Pasted image 20231212170456.png]]
Following the basic placement guidelines, the administrator would place a standard ACL on router R3. There are two possible interfaces on R3 to apply the standard ACL:
- **R3 S0/1/1 interface** **(inbound)** - The standard ACL can be applied inbound on the R3 S0/1/1 interface to deny traffic from .10 network. However, it would also filter .10 traffic to the 192.168.31.0/24 (.31 in this example) network. Therefore, the standard ACL should not be applied to this interface.
- **R3 G0/0 interface** **(outbound)** - The standard ACL can be applied outbound on the R3 G0/0/0 interface. This will not affect other networks that are reachable by R3. Packets from .10 network will still be able to reach the .31 network. This is the best interface to place the standard ACL to meet the traffic requirements.


## Extended ACL Placement Example

Extended ACL should be located as close to the source as possible. This prevents unwanted traffic from being sent across multiple networks only to be denied when it reaches its destination.

However, the organization can only place ACLs on devices that they control. Therefore, the extended ACL placement must be determined in the context of where organizational control extends.

In the figure, for example, Company A wants to deny Telnet and FTP traffic to Company B’s 192.168.30.0/24 network from their 192.168.11.0/24 network while permitting all other traffic.
![[Pasted image 20231212170948.png]]


There are several ways to accomplish these goals. An extended ACL on R3 would accomplish the task, but the administrator does not control R3. In addition, this solution allows unwanted traffic to cross the entire network, only to be blocked at the destination. This affects overall network efficiency.

The solution is to place an extended ACL on R1 that specifies both source and destination addresses.

There are two possible interfaces on R1 to apply the extended ACL:

- **R1 S0/1/0 interface (outbound)** - The extended ACL can be applied outbound on the S0/1/0 interface. However, this solution will process all packets leaving R1 including packets from 192.168.10.0/24.
- **R1 G0/0/1 interface (inbound)** - The extended ACL can be applied inbound on the G0/0/1 and only packets from the 192.168.11.0/24 network are subject to ACL processing on R1. Because the filter is to be limited to only those packets leaving the 192.168.11.0/24 network, applying the extended ACL to G0/1 is the best solution
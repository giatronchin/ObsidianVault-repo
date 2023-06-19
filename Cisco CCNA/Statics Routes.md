#cisco #routing #router #static-routing

IPv4 static routes are configured using the following global configuration command:

```
Router(config)# ip route network-address subnet-mask { ip-address | exit-intf [ip-address]} [distance]
```

|**Parameter**|**Description**|
|---|---|
|_network-address_|Identifies the destination IPv4 network address of the remote network to add to the routing table.|
|_subnet-mask_|- Identifies the subnet mask of the remote network.<br>- The subnet mask can be modified to summarize a group of networks and create a summary static route.|
|_ip-address_|- Identifies the next-hop router IPv4 address.<br>- Typically used with broadcast networks (i.e., Ethernet).<br>- Could create a recursive static route where the router performs an additional lookup to find the exit interface.|
|_exit-intf_|- Identifies the exit interface to forward packets.<br>- Creates a directly connected static route.<br>- Typically used in a point-to-point configuration.|
|_exit-intf ip-address_|Creates a fully specified static route because it specifies the exit interface and next-hop IPv4 address.|
|_distance_|- Optional command that can be used to assign an administrative distance value between 1 and 255.<br>- Typically used to configure a floating static route by setting an administrative distance that is higher than a dynamically learned route.|

---

IPv6 static routes are configured using the following global configuration command:

```
Router(config)# ipv6 route ipv6-prefix/prefix-length {ipv6-address | exit-intf [ipv6-address]} [distance]
```

|**Parameter**|**Description**|
|---|---|
|_ipv6-prefix_|Identifies the destination IPv6 network address of the remote network to add to the routing table.|
|_/prefix-length_|Identifies the prefix length of the remote network.|
|_ipv6-address_|- Identifies the next-hop router IPv6 address.<br>- Typically used with broadcast networks (i.e., Ethernet)<br>- Could create a recursive static route where the router performs an additional lookup to find the exit interface.|
|_exit-intf_|- Identifies the exit interface to forward packets.<br>- Creates a directly connected static route.<br>- Typically used in a point-to-point configuration.|
|_exit-intf ipv6-address_|Creates a fully specified static route because it specifies the exit interface and next-hop IPv6 address.|
|_distance_|- Optional command that can be used to assign an administrative distance value between 1 and 255.<br>- Typically used to configure a floating static route by setting an administrative distance that is higher than a dynamically learned route.|

**Note**: The **ipv6 unicast-routing** global configuration command must be configured to enable the router to forward IPv6 packets.

## Verify a Static Route

Along with `show ip route`, `show ipv6 route`, `ping` and `traceroute`, other useful commands to verify static routes include the following:

- `show ip route static`
- `show ip route network`
- `show running-config | section ip route`

Replace **ip** with **ipv6** for the IPv6 versions of the command.
#cisco #router #dhcp #ipv4 

# DHCPv4 CISCO IOS Server Config
#dhcp-server
**Step 1. Exclude IPv4 Addresses**

The router functioning as the DHCPv4 server assigns all IPv4 addresses in a DHCPv4 address pool unless it is configured to exclude specific addresses. Typically, some IPv4 addresses in a pool are assigned to network devices that require static address assignments. Therefore, these IPv4 addresses should not be assigned to other devices. The command syntax to exclude IPv4 addresses is the following:

`Router(config)# ip dhcp excluded-address low-address [ high-address ]`

A single address or a range of addresses can be excluded by specifying the _low-address_ and _high-address_ of the range. Excluded addresses should be those addresses that are assigned to routers, servers, printers, and other devices that have been, or will be, manually configured. You can also enter the command multiple times.

**Step 2. Define a DHCPv4 Pool Name**

Configuring a DHCPv4 server involves defining a pool of addresses to assign.

As shown in the example, the **ip dhcp pool** _pool-name_ command creates a pool with the specified name and puts the router in DHCPv4 configuration mode, which is identified by the prompt Router(dhcp-config)#.

The command syntax to define the pool is the following:

```
Router(config)# ip dhcp pool pool-name 
Router(dhcp-config)# 
```

**Step 3. Configure the DHCPv4 Pool**

The table lists the tasks to complete the DHCPv4 pool configuration.

The address pool and default gateway router must be configured. Use the **network** statement to define the range of available addresses. Use the **default-router** command to define the default gateway router. Typically, the gateway is the LAN interface of the router closest to the client devices. One gateway is required, but you can list up to eight addresses if there are multiple gateways.

Other DHCPv4 pool commands are optional. For example, the IPv4 address of the DNS server that is available to a DHCPv4 client is configured using the **dns-server** command. The **domain-name** command is used to define the domain name. The duration of the DHCPv4 lease can be changed using the **lease** command. The default lease value is one day. The **netbios-name-server** command is used to define the NetBIOS WINS server.

1. Define the address pool:  `network network-number [mask | /prefix-length ]`
2. Define the default router or gateway: `default-router address [  address2….address8 ]`
3. Define a DNS server: `dns-server address [ address2…address8]`
4. Define the domain name: `domain-name domain`
5. Define the duration of the DHCP lease: `lease {days [ hours [ minutes ]] | infinite }`
6. Define the NetBIOS WINS server: `netbios-name-server  address [  address2…address8 ]`

#### Example configuration on Router
![[Pasted image 20230508180734.png]]
The example shows the configuration to make R1 a DHCPv4 server for the 192.168.10.0/24 LAN.
```
R1(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.9
R1(config)# ip dhcp excluded-address 192.168.10.254
R1(config)# ip dhcp pool LAN-POOL-1
R1(dhcp-config)# network 192.168.10.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.10.1
R1(dhcp-config)# dns-server 192.168.11.5
R1(dhcp-config)# domain-name example.com
R1(dhcp-config)# end
R1#
```


### Verification Configuration

- Displays the DHCPv4 commands configured on the router.

	**show running-config | section dhcp**

- Displays a list of all IPv4 address to MAC address bindings provided by the DHCPv4 service.

	**show ip dhcp binding**

- Displays count information regarding the number of DHCPv4 messages that have been sent and received.

	**show ip dhcp server statistics**

## Disable DHCP 
The DHCPv4 service is enabled by default. To disable the service, use the **no service dhcp** global configuration mode command

**Note**: Clearing the DHCP bindings or stopping and restarting the DHCP service may result in duplicate IP addresses being temporarily assigned on the network.

```
R1(config)# no service dhcp
R1(config)# service dhcp
R1(config)# 
```

## Relay DHCP
#dhcp-relay

In the figure, PC1 is attempting to acquire an IPv4 address from a DHCPv4 server using a broadcast message. In this scenario, R1 is not configured as a DHCPv4 server and does not forward the broadcast. Because the DHCPv4 server is located on a different network, PC1 cannot receive an IP address using DHCP. R1 must be configured to relay DHCPv4 messages to the DHCPv4 server.

![[Pasted image 20230511223647.png]]
The solution is to configure R1 with the **ip helper-address** _address_ interface configuration command. This will cause R1 to relay DHCPv4 broadcasts to the DHCPv4 server. In this particular configuration the interface on R1 receiving the broadcast from PC1 is configured to relay DHCPv4 address to the DHCPv4 server at 192.168.11.6. 

```
R1(config)# interface g0/0/0
R1(config-if)# ip helper-address 192.168.11.6
R1(config-if)# end
R1#
```
<mark>Note</mark>: ip helper-address enable also the relay features for a lot of different services like the following 
- Port 37: Time
- Port 49: TACACS
- Port 53: DNS
- Port 67: DHCP/BOOTP server
- Port 68: DHCP/BOOTP client
- Port 69: TFTP
- Port 137: NetBIOS name service
- Port 138: NetBIOS datagram service

## Configure CISCO Router as DHCPv4 Client
#dhcp-client
There are scenarios where you might have access to a DHCP server through your ISP. In these instances, you can configure a Cisco IOS router as a DHCPv4 client. In order to do so you have to issue the interface command `ip address dhcp`.
```
SOHO(config)# interface G0/0/1
SOHO(config-if)# ip address dhcp 
SOHO(config-if)# no shutdown
Sep 12 10:01:25.773: %DHCP-6-ADDRESS_ASSIGN: Interface GigabitEthernet0/0/1 assigned DHCP address 209.165.201.12, mask 255.255.255.224, hostname SOHO
```
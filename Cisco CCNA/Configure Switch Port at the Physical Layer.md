#cisco #switch
Switch ports can be manually configured with specific duplex and speed settings. Use the **duplex** interface configuration mode command to manually specify the duplex mode for a switch port. Use the **speed** interface configuration mode command to manually specify the speed. For example, both switches in the topology should always operate in full-duplex at 100 Mbps.

![[Pasted image 20230426150005.png]]
The table shows the commands for S1. The same commands can be applied to S2.

|**Task**|**IOS Commands**|
| --- | --- |
|Enter global configuration mode.|S1# **configure terminal**|
|Enter interface configuration mode.|S1(config)# **interface FastEthernet 0/1**|
|Configure the interface duplex.|S1(config-if)# **duplex full**|
|Configure the interface speed.|S1(config-if)# **speed 100**|
|Return to the privileged EXEC mode.|S1(config-if)# **end**|
|Save the running config to the startup config.|S1# **copy running-config startup-config**|
| Enable auto-MDIX | S1(config-if)# mdix auto |

The default setting for both duplex and speed for switch ports on _Cisco Catalyst 2960_ and _3560_ switches is auto. The 10/100/1000 ports operate in either half- or full-duplex mode when they are set to 10 or 100 Mbps and operate only in full-duplex mode when it is set to 1000 Mbps (1 Gbps). **Autonegotiation** is useful when the speed and duplex settings of the device connecting to the port are unknown or may change. When connecting to known devices such as servers, dedicated workstations, or network devices, a best practice is to manually set the speed and duplex settings.

<mark>When troubleshooting switch port issues, it is important that the duplex and speed settings should be checked.</mark>

**Note**: Mismatched settings for the duplex mode and speed of switch ports can cause connectivity issues. Autonegotiation failure creates mismatched settings.

All fiber-optic ports, such as 1000BASE-SX ports, operate only at one preset speed and are always full-duplex.

### MDIX

Until recently, certain cable types (straight-through or crossover) were required when connecting devices. Switch-to-switch or switch-to-router connections required using different Ethernet cables. Using the automatic medium-dependent interface crossover (auto-MDIX) feature on an interface eliminates this problem. When auto-MDIX is enabled, the interface automatically detects the required cable connection type (straight-through or crossover) and configures the connection appropriately. When connecting to switches without the auto-MDIX feature, straight-through cables must be used to connect to devices such as servers, workstations, or routers. Crossover cables must be used to connect to other switches or repeaters.

With auto-MDIX enabled, either type of cable can be used to connect to other devices, and the interface automatically adjusts to communicate successfully. On newer Cisco switches, the **mdix auto** interface configuration mode command enables the feature. When using auto-MDIX on an interface, the interface speed and duplex must be set to **auto** so that the feature operates correctly.

**Note**: The auto-MDIX feature is enabled by default on Catalyst 2960 and Catalyst 3560 switches but is not available on the older Catalyst 2950 and Catalyst 3550 switches.

To examine the auto-MDIX setting for a specific interface, use the **show controllers ethernet-controller** command with the **phy** keyword.
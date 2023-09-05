#cisco #vlan #trunk #switch #dtp
A VLAN trunk is a Layer 2 link between two switches that carries traffic for all VLANs (unless the allowed VLAN list is restricted manually or dynamically).

To enable trunk links, configure the interconnecting ports with the set of interface configuration commands shown in the table.

| **Task** | **IOS Command** |
| --- | --- |
| Enter global configuration mode. | Switch# **configure terminal** |
| Enter interface configuration mode. | Switch(config)# **interface** _interface-id_ |
| If Switch supports both dot1q and ISL we need to choose one | Switch(config-if)# **switchport encapsulation dot1q** |
| Set the port to permanent trunking mode. | Switch(config-if)# **switchport mode trunk** |
| Sets the native VLAN to something other than VLAN 1. | Switch(config-if)# **switchport trunk native vlan** _vlan-id_ |
| Specify the list of VLANs to be allowed on the trunk link. | Switch(config-if)# **switchport trunk allowed vlan** _vlan-list_ |
| Return to the privileged EXEC mode. | Switch(config-if)# **end** |

**Note**: This configuration assumes the use of Cisco Catalyst 2960 switches which automatically use 802.1Q encapsulation on trunk links. Other switches may require manual configuration of the encapsulation. Always configure both ends of a trunk link with the same native VLAN. If 802.1Q trunk configuration is not the same on both ends, Cisco IOS Software reports errors.

<mark>Verify configuration</mark>:Another useful command for veryfing trunk interfaces is the **show interface trunk** command.

## Reset the Trunk to the Default State

Use the `no switchport trunk allowed vlan` and the `no switchport trunk native vlan` commands to remove the allowed VLANs and reset the native VLAN of the trunk. When it is reset to the default state, the trunk allows all VLANs and uses VLAN 1 as the native VLAN.

## <mark>Dynamic Trunking Protocol</mark> #dtp

An interface can be set to trunking or nontrunking, or to negotiate trunking with the neighbor interface. Trunk negotiation is managed by DTP, which operates on a point-to-point basis only, between network devices.

DTP is a Cisco proprietary protocol that is automatically enabled on Catalyst 2960 and Catalyst 3650 Series switches. DTP manages trunk negotiation only if the port on the neighbor switch is configured in a trunk mode that supports DTP. Switches from other vendors do not support DTP.
To disable DTP or to enable static trunking from a Cisco switch **to a device that does not support DTP**, use the **switchport mode trunk** and **switchport nonegotiate** interface configuration mode commands. This causes the interface to become a trunk, but it will not generate DTP frames.

```
S1(config-if)# switchport mode trunk
S1(config-if)# switchport nonegotiate
```

To re-enable dynamic trunking protocol use the **switchport mode dynamic** **auto** command.

```
S1(config-if)# switchport mode dynamic auto
```

If the ports connecting two switches are configured to ignore all DTP advertisements with the **switchport mode trunk** and the **switchport nonegotiate** commands, the ports will stay in trunk port mode. If the connecting ports are set to dynamic auto, they will not negotiate a trunk and will stay in the access mode state, creating an inactive trunk link.

When configuring a port to be in trunk mode, use the **switchport mode trunk** command. Then there is no ambiguity about which state the trunk is in; it is always on.

The **switchport mode** command has additional options for negotiating the interface mode. The full command syntax is the following:

Switch(config-if)# **switchport mode** { **access** | **dynamic** { **auto** | **desirable** } | **trunk** }

The options are described in the table.
![[Pasted image 20230429155225.png]]
Use the **switchport nonegotiate** interface configuration command to stop DTP negotiation. The switch does not engage in DTP negotiation on this interface. You can use this command only when the interface switchport mode is **access** or **trunk**. You must manually configure the neighboring interface as a trunk interface to establish a trunk link.

![[Pasted image 20230429155303.png]]
The default DTP mode is dependent on the Cisco IOS Software version and on the 
platform. To determine the current DTP mode, issue the `show dtp interface` command.

**Note**: A general best practice is to set the interface to **trunk** and **nonegotiate** when a trunk link is required. On links where trunking is not intended, DTP should be turned off.
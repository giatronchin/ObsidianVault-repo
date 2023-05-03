#switch #cisco #vlan #voice #voip

## VLAN creation
Cisco IOS command syntax used to add a VLAN to a switch and give it a name
|**Task**|**IOS Command**|
| --- | --- |
|Enter global configuration mode.|Switch# **configure terminal**|
|Create a VLAN with a valid ID number.|Switch(config)# **vlan** _vlan-id_|
|Specify a unique name to identify the VLAN.|Switch(config-vlan)# **name** _vlan-name_|
|Return to the privileged EXEC mode.|Switch(config-vlan)# **end**|

**Note**: remember to save the **running-config** to the **startup-config** in order to make the changes persistent to the switch.

```
S1# configure terminal
S1(config)# vlan 20
S1(config-vlan)# name student
S1(config-vlan)# end
```

## VLAN Port Assignment Commands

After creating a VLAN, the next step is to assign ports to the VLAN.

The table displays the syntax for defining a port to be an access port and assigning it to a VLAN. The **switchport mode access** command is optional, but strongly recommended as a security best practice. With this command, the interface changes to strictly access mode. Access mode indicates that the port belongs to a single VLAN and will not negotiate to become a trunk link.

|**Task**|**IOS Command**|
| --- | --- |
|Enter global configuration mode.|Switch# **configure terminal**|
|Enter interface configuration mode.|Switch(config)# **interface** _interface-id_|
|Set the port to access mode.|Switch(config-if)# **switchport mode access**|
|Assign the port to a VLAN.|Switch(config-if)# **switchport access vlan** _vlan-id_|
|Return to the privileged EXEC mode.|Switch(config-if)# **end**|

**Note**: Use the **interface range** command to simultaneously configure multiple interfaces.

In the figure, port F0/6 on switch S1 is configured as an access port and assigned to VLAN 20. Any device connected to that port will be associated with VLAN 20. Therefore, in our example, PC2 is in VLAN 20.
![[Pasted image 20230429134033.png]]

The example shows the configuration for S1 to assign F0/6 to VLAN 20.

```
S1# configure terminal
S1(config)# interface fa0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20
S1(config-if)# end
```

**Note**: VLANs are configured on the switch port and **not on the end device**. 

PC2 is configured with an IPv4 address and subnet mask that is associated with the VLAN, which is configured on the switch port. In this example, it is VLAN 20. When VLAN 20 is configured on other switches, the network administrator must configure the other student computers to be in the same subnet as PC2 (172.17.20.0/24).

## Data and Voice VLANs

Scenario like the one following require Switch S3 port F0/18 to be configured with a **data and a voice VLAN**.
![[Pasted image 20230429134620.png]]
LANs supporting voice traffic typically also have quality of service (QoS) enabled. Voice traffic must be labeled as trusted as soon as it enters the network. Use the `mls qos trust [cos | device cisco-phone | dscp | ip-precedence]` interface configuration command to set the trusted state of an interface, and to indicate which fields of the packet are used to classify traffic.

The configuration in the example creates the two VLANs (i.e., VLAN 20 and VLAN 150) and then assigns the F0/18 interface of S3 as a switchport in VLAN 20. It also assigns voice traffic to VLAN 150 and enables QoS classification based on the class of service (CoS) assigned by the IP phone.

```
S3(config)# vlan 20
S3(config-vlan)# name student
S3(config-vlan)# vlan 150
S3(config-vlan)# name VOICE
S3(config-vlan)# exit
S3(config)# interface fa0/18
S3(config-if)# switchport mode access
S3(config-if)# switchport access vlan 20
S3(config-if)# mls qos trust cos
S3(config-if)# switchport voice vlan 150
S3(config-if)# end
```

**Note:** The `switchport access vlan` command forces the creation of a VLAN if it does not already exist on the switch.

The **show vlan** command displays a list of all configured VLANs. The **show vlan** command can also be used with options. The complete syntax is `show vlan [brief | id vlan-id | name vlan-name | summary]`.

The table describes the **show vlan** command options.

|Task|Command Option|
| --- | --- |
|Display VLAN name, status, and its ports one VLAN per line.|**brief**|
|Display information about the identified VLAN ID number. For _vlan-id_, the range is 1 to 4094.|**id** _vlan-id_|
|Display information about the identified VLAN name. The _vlan-name_ is an ASCII string from 1 to 32 characters.|**name** _vlan-name_|
|Display VLAN summary information.|**summary**|

**Note**: The **show vlan summary** command displays the count of all configured VLANs.

<mark>Verify configuration</mark>:Other useful commands are the `show interfaces _interface-id_ switchport` and the `show interfaces vlan vlan-id` command.

## Change VLAN Port Membership & Delete VLANs

**Change Assigned VLAN**: If the switch access port has been incorrectly assigned to a VLAN, then simply re-enter the `switchport access vlan vlan-id` interface configuration command with the correct VLAN ID.

**Back to default VLAN**: To change the membership of a port back to the default VLAN 1, use the **no switchport access vlan** interface configuration mode command as shown.

DELETE VLAN: The `no vlan vlan-id` **global** configuration mode command is used to remove a VLAN from the switch vlan.dat file. This can be done also erasing the vlan.dat file and rebooting the switch.

**Caution**: Before deleting a VLAN, reassign all member ports to a different VLAN first. Any ports that are not moved to an active VLAN are unable to communicate with other hosts after the VLAN is deleted and until they are assigned to an active VLAN.

**Note**: To restore a Catalyst switch to its factory default condition, unplug all cables except the console and power cable from the switch. Then enter the **erase startup-config** privileged EXEC mode command followed by the **delete vlan.dat** command.


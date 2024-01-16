# Spanning Tree Protocol
#cisco #switch #stp #rstp #mstp

```
S1# show spanning-tree [ vlan <vlan-id> ]
S1# configure terminal
S1(config)# spanning-tree mode [ mst | pvst | rapid-pvst ]
```

Default is `pvst` Per-VLAN Spanning Tree. MST Multiple Spanning Tree is another option.


### Original STP

| STP Port State | Send/Receive BPDUs | Frame forwarding (regular traffic) | MAC address Learning | Stable/Transitional |
| --- | --- | --- | --- | --- |
| BLOCKING | NO/YES | NO | NO | Stable |
| LISTENING | YES/YES | NO | NO | Transitional |
| LEARNING | YES/YES | NO | YES | Transitional |
| FORWARDING | YES/YES | YES | YES | Stable |
| DISABLED | NO/NO | NO | NO | Stable |

**Note**: Switch will forward BDPU only via their Designated port never using Root or Non-Designated ports.

#### TIMER

| STP Timer | Purpose | Duration |
| --- | --- | --- |
| Hello | How often the root bridge sends hello BPDUs | 2 sec |
| Forward delay | How long the switch will stay in the **listening** and **learning** states (each is 15 sec = 30 sec total ) | 15 sec |
| Max Age | How long an interface will wait after ceasing to receive Hello BPDUs to change the STP topology | 20 sec (10 x Hello timer) |

## Basic STP configuration

The following are the mode available on a switch:
```
SW1(config)# spanning-tree mode ?
	mst  Multiple spanning tree mode
	pvst  Per-Vlan spanning tree mode
	rapid-pvst  Per-Vlan rapid spanning tree mode
```

Manually configure RootBridge Priority to force root election:
```
SW1(config)# spanning-tree vlan 1 root primary
```

Manually configure secondary Root Bridge with priority:
```
SW1(config)# spanning-tree vlan 1 root secondary
```

Note: These commands play behind the scene with priority value incrementaly.

Basically on each interface and per vlan we can configure: cost and port-priority
```
SW1(config-if)# spanning-tree vlan 1 cost <1-200000000>
SW1(config-if)# spanning-tree vlan 1 port-priority <0-224>
```
---

### Optional features of STP (STP toolkit)

### PortFast 
Allow port that comes online to go directy from **blocking** to **forwarding** state as long as it can be used only with port connected to end-devices.

```
SW1(config)# interface g0/2
SWI(config-if)# spanning-tree portfast
```

#### BPDU Guard
If an interface with BPDU Guard enabled receives a BPDU from another switch, the interface will be **shut down to prevent a loop** from forming.

```
SW1(config)# interface g0/2
SWI(config-if)# spanning-tree bpduguard enable
```

or you can also use the following command t**o enable it on all interfaces**:

```
SW1(config)# spanning-tree portfast bpdu default
```

#### Root Guard
If you enable **root guard** on an interface, even if it receives a superior BPDU (lower bridge ID) on that interface, the switch **will not accept the new switch as the root bridge**. The interface will be disabled.

#### Loop Guard
If you enable **loop guard** on an interface, even if the interface stops receiving BPDUs, it **will not start forwarding**. The interface will be **disabled**.


---

##  Discovery Protocol

#CDP - Cisco Discovery Protocol
```
S1(config)# cdp run <-- Enable CDP
S1(config)# exit
S1# show cdp neighbors <-- Display neighbors
S1# show cdp traffic <-- Statistics & Counters
S1# configure terminal
S1(config)# no cdp run <-- Stop CDP
```

#LLDP - Link Layer Discovery Protcol
```
S1(config)# lldp run <-- Enable CDP
S1(config)# exit
S1# show lldp neighbors <-- Display neighbors
S1# show cdp traffic <-- Statistics & Counters
S1# configure terminal
S1(config)# no cdp run <-- Stop CDP
```

Note: use them on spot when needed otherwise disable them for security reason.

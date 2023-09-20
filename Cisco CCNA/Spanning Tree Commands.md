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

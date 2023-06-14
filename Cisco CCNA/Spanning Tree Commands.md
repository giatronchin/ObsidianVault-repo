#cisco #switch #stp #rstp #mstp

```
S1# show spanning-tree [ vlan <vlan-id> ]
S1# configure terminal
S1(config)# spanning-tree mode [ mst | pvst | rapid-pvst ]
```

Default is `pvst` Per-VLAN Spanning Tree. MST Multiple Spanning Tree is another option.

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
# Traffic Analysis
#tryhackme #wireshark #nmap 

Common Wireshark filter and associated NMAP to be discovered:
![[Pasted image 20221013220438.png]]

#### TCP Connect Scan in a nutshell:
-   Relies on the three-way handshake (needs to finish the handshake process).
-   Usually conducted with `nmap -sT` command.
-   Used by non-privileged users (only option for a non-root user).
-   Usually has a windows size larger than 1024 bytes as the request expects some data due to the nature of the protocol.
```
tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024
```

#### TCP SYN Scan in a nutshell:
-   Doesn't rely on the three-way handshake (no need to finish the handshake process).
-   Usually conducted with `nmap -sS` command.
-   Used by privileged users.
-   Usually have a size less than or equal to 1024 bytes as the request is not finished and it doesn't expect to receive data.
```
tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024
```

#### UDP Scan in a nutshell:
-   Doesn't require a handshake process
-   No prompt for open ports
-   ICMP error message for close ports
-   Usually conducted with `nmap -sU` command.
```
icmp.type==3 and icmp.code==3
```
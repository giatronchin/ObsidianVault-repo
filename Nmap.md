#nmap #security 
We present the different approaches that Nmap uses to <mark>discover live hosts</mark>. In particular, we cover:

- **ARP scan**: This scan uses ARP requests to discover live hosts
- **ICMP scan**: This scan uses ICMP requests to identify live hosts
- **TCP/UDP ping scan**: This scan sends packets to TCP ports and UDP ports to determine live hosts.

If you are connected to the same subnet, you would expect your scanner to rely on **ARP** (Address Resolution Protocol) queries to discover live hosts.
An ARP query aims to get the hardware address (MAC address) so that communication over the link-layer becomes possible; however, we can use this to infer that the host is online.

------------------------------------------------------------------------------

We also introduce two scanners, `arp-scan` and `masscan`, and explain how they overlap with part of Nmap’s host discovery.

-------------------------------------------------------------------------------

If you are in Network A, you can use ARP only to discover the devices within that subnet (10.1.100.0/24). Suppose you are connected to a subnet different from the subnet of the target system(s). In that case, all packets generated by your scanner will be routed via the default gateway (router) to reach the systems on another subnet; however, the ARP queries won’t be routed and hence cannot cross the subnet router. ARP is a link-layer protocol, and ARP packets are bound to their subnet.

## How to provide target address to Nmap

1. **list**: this will scan for the targets
       MACHINE_IP scanme.nmap.org example.com

2. **range**: will scan 6 IP addresses: 10.11.12.15, 10.11.12.16,… and 10.11.12.20.
       10.11.12.15-20 

3. **subnet**: will scan 4 IP addresses.
     MACHINE_IP/30 

4. **providing target in a file**:
    nmap -iL list_of_hosts.txt

__Note__: <mark>"nmap -sL TARGETS"</mark> this  will list the target aquired by Nmap without scanning (just DNS lookup so far). Adding the **"-n"** option we will avoid the DNS lookup.


Nmap will follow this strategy to enumerate the host live on the network:

1. When a **privileged user** tries to scan **targets on a local network** (Ethernet), Nmap uses:

 <mark>ARP requests<mark>

2. When a **privileged user** tries to scan **targets outside the local network**, Nmap uses:
- <mark>ICMP echo requests</mark>
- <mark>TCP ACK (Acknowledge) to port 80</mark>
- <mark>TCP SYN (Synchronize) to port 443</mark>
- <mark>ICMP timestamp request</mark>
3. When an **unprivileged user** tries to scan **targets outside the local network**, Nmap resorts to:
- <mark>TCP 3-way handshake by sending SYN packets to ports 80 and 443</mark>.


If you want to use Nmap to <ins>discover online hosts without port-scanning the live systems</ins>, you can issue 

    nmap -sn TARGETS

If you want Nmap only to perform an ARP scan without port-scanning, you can use

     nmap -PR -sn TARGETS

where <mark>-PR</mark> indicates that you <ins>only want an ARP scan</ins>

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

**Alternative** to "nmap -PR -sn TARGETS" there is another popular tool:

    arp-scan --localnet -I interface

--localnet send an ARP request to all host on the same subnet.

-I allow to specify which physical interface to use.

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

Host-Discovery using Nmap-ICMP

Use the ping ICMP procedure to discover host on a network based on the response to the Echo-Request ICMP message received.

ICMP Message Type 8: ICMP Ping-Echo (request)
ICMP Message Type 0: ICMP Ping-Reply (response)
ICMP Message Type 13: ICMP Timestamp request
ICMP Message Type 14: ICMP Timestamp reply
ICMP Message Type 17: ICMP Mask request
ICMP Message Type 18: ICMP Mask reply

	nmap -PE -sn MACHINE_IP/24

-PE ping echo
-sn do not scan for ports open on host once we find one

Generally speaking, we don’t expect to learn the MAC addresses of the targets unless they are on the same subnet as our system. Having them within the output indicates that Nmap didn’t need to send ICMP packets as it confirmed that these hosts are up based on the ARP responses it received.

Note: behind the scene an ARP request may be sent out to discover unknown MAC address association


Because ICMP echo requests tend to be blocked, you might also consider **ICMP Timestamp** or **ICMP Address Mask requests** to tell if a system is online. Nmap uses timestamp request (ICMP Type 13) and checks whether it will get a Timestamp reply (ICMP Type 14). Adding the -PP option tells Nmap to use ICMP timestamp requests. As shown in the figure below, you expect live hosts to reply.

	nmap -PP -sn MACHINE_IP/24

Similarly, Nmap uses address mask queries (ICMP Type 17) and checks whether it gets an address mask reply (ICMP Type 18). This scan can be enabled with the option -PM. As shown in the figure below, live hosts are expected to reply to ICMP address mask requests.

	nmap -PM -sn MACHINE_IP/24

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

Host-Discovery using TCP & UDP

1. TCP-SYN

We can send a packet with the SYN (Synchronize) flag set to a TCP port, 80 by default, and wait for a response. An open port should reply with a SYN/ACK (Acknowledge); a closed port would result in an RST (Reset). In this case, we only check whether we will get any response to infer whether the host is up. The specific state of the port is not significant here. 

	nmap -PS -sn MACHINE_IP/24
	nmap -PS21 -sn MACHINE_IP/24 			//SYN packet on port 21
	nmap -PS21-25 -sn MACHINE_IP/24			//SYN packet on port 21,22,23,24,25
	nmap -PS80,443,8080 -sn MACHINE_IP/24	//SYN packet on port 80,443,8080

Note: run this command without root priviledge would mean that we have to close the 3-way handshake. Sudoers don't have to.

2. TCP-ACK

This sends a packet with an ACK flag set. You must be running Nmap as a privileged user to be able to accomplish this. If you try it as an unprivileged user, Nmap will attempt a 3-way handshake.

By default, port 80 is used. The syntax is similar to TCP SYN ping. -PA should be followed by a port number, range, list, or a combination of them. For example, consider -PA21, -PA21-25 and -PA80,443,8080. If no port is specified, port 80 will be used.

The following figure shows that any TCP packet with an ACK flag should get a TCP packet back with an RST flag set. <mark>The target responds with the RST flag set because the TCP packet with the ACK flag is not part of any ongoing connection.</mark> The expected response is used to detect if the target host is up.

	sudo nmap -PA -sn MACHINE_IP/24

3. UDP-Ping

We can use UDP to discover if the host is online. Contrary to TCP SYN ping, sending a UDP packet to an open port is not expected to lead to any reply. However, if we send a UDP packet to a closed UDP port, we expect to get an ICMP port unreachable packet; this indicates that the target system is up and available.

	nmap -PU -sn MACHINE_IP/24

4. MASSSCAN

Masscan uses a similar approach to discover the available systems. However, to finish its network scan quickly, Masscan is quite aggressive with the rate of packets it generates. The syntax is quite similar: -p can be followed by a port number, list, or range. Consider the following examples:

	masscan MACHINE_IP/24 -p443
	masscan MACHINE_IP/24 -p80,443
	masscan MACHINE_IP/24 -p22-25
	masscan MACHINE_IP/24 ‐‐top-ports 100

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

Nmap’s default behaviour is to use reverse-DNS online hosts. Because the hostnames can reveal a lot, this can be a helpful step. However, if you don’t want to send such DNS queries, you use -n to skip this step.

By default, Nmap will look up online hosts; however, you can use the option -R to query the DNS server even for offline hosts. If you want to use a specific DNS server, you can add the --dns-servers DNS_SERVER option.

| Scan Type |	Example Command |
| ----- | ----- |
| ARP Scan	| sudo nmap -PR -sn MACHINE_IP/24 |
| ICMP Echo Scan |	sudo nmap -PE -sn MACHINE_IP/24 |
| ICMP Timestamp Scan |	sudo nmap -PP -sn MACHINE_IP/24 |
| ICMP Address Mask Scan |	sudo nmap -PM -sn MACHINE_IP/24 |
| TCP SYN Ping Scan	| sudo nmap -PS22,80,443 -sn MACHINE_IP/30 |
| TCP ACK Ping Scan |	sudo nmap -PA22,80,443 -sn MACHINE_IP/30 |
| UDP Ping Scan	| sudo nmap -PU53,161,162 -sn MACHINE_IP/30 |

Option	Purpose
-n	no DNS lookup
-R	reverse-DNS lookup for all hosts
-sn	host discovery only

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

In the section above we have got covered:
1. Enumeration of targets
2. Discover live hosts
3. Reverse-DNS lookup

Now we want to go forward with **PORT SCANNING**.

In particular there are three kind of techniques we can leverage:
- TCP connect port scan
- TCP SYN port scan
- UDP port scan

In practical situations, we need to consider the impact of firewalls. For instance, a port might be open, but a firewall might be blocking the packets. Therefore, Nmap considers the following six states:

- **Open**: indicates that a service is listening on the specified port.
- **Closed**: indicates that no service is listening on the specified port, although the port is accessible. By accessible, we mean that it is reachable and is not blocked by a firewall or other security appliances/programs.
- **Filtered**: means that Nmap cannot determine if the port is open or closed because the port is not accessible. This state is usually due to a firewall preventing Nmap from reaching that port. Nmap’s packets may be blocked from reaching the port; alternatively, the responses are blocked from reaching Nmap’s host.
- **Unfiltered**: means that Nmap cannot determine if the port is open or closed, although the port is accessible. This state is encountered when using an ACK scan -sA.
- **Open|Filtered**: This means that Nmap cannot determine whether the port is open or filtered.
- **Closed|Filtered**: This means that Nmap cannot decide whether a port is closed or filtered.

Of course all those state depends heavly on what kind of TCP segment has been sent by NMAP. At the hearth of NMAP TCP Scan there are the use of TCP Header Flags. Those flags are list below (Setting a flag bit means setting its value to 1):

+ URG: Urgent flag indicates that the urgent pointer filed is significant. The urgent pointer indicates that the incoming data is urgent, and that a TCP segment with the URG flag set is processed immediately without consideration of having to wait on previously sent TCP segments.
+ ACK: Acknowledgement flag indicates that the acknowledgement number is significant. It is used to acknowledge the receipt of a TCP segment.
+ PSH: Push flag asking TCP to pass the data to the application promptly.
+ RST: Reset flag is used to reset the connection. Another device, such as a firewall, might send it to tear a TCP connection. This flag is also used when data is sent to a host and there is no service on the receiving end to answer.
+ SYN: Synchronize flag is used to initiate a TCP 3-way handshake and synchronize sequence numbers with the other host. The sequence number should be set randomly during TCP connection establishment.
+ FIN: The sender has no more data to send.

----------------------------------------------------------------------------------------------------------

- TCP connect port scan: this technique involve the NMAP client sending an TCP SYN segment to initiate a 3-way handshake on every single port on the target host. By default the behaviour is to attempt randomly (to do it consecutivly use the -r option) the first 1000 most common port, but this can be narrow down to 100 using the <mark>-F</mark> (fast) option.

The port is **open** as long as the target answer with the TCP SYN/ACK segment and without root priviledge the client complete the hand-shake and tear it down immediatly after with a RST/ACK.

A closed TCP port responds to a SYN packet with RST/ACK to indicate that it is not open.

	[sudo]	nmap -sT TARGET_IP_ADDRESS

!IMPORTANT: unpriviledge users are limited to this kind of scan!

----------------------------------------------------------------------------------------------------------

- TCP SYN port scan

The default scan mode is SYN scan, and it requires a privileged (root or sudoer) user to run it. SYN scan does not need to complete the TCP 3-way handshake; instead, it tears down the connection once it receives a response from the server. Because we didn’t establish a TCP connection, this decreases the chances of the scan being logged. We can select this scan type by using the -sS option.

!IMPORTANT: SYN scan -sS does not need to complete the TCP 3-way handshake; instead, Nmap sends an RST packet once a SYN/ACK packet is received!

	sudo nmap -sS TARGET_IP_ADDRESS

----------------------------------------------------------------------------------------------------------

- UDP port scan

UDP is a connectionless protocol, and hence it does not require any handshake for connection establishment. We cannot guarantee that a service listening on a UDP port would respond to our packets. However, if a UDP packet is sent to a closed port, an **ICMP port unreachable error (type 3, code 3) is returned**. You can select UDP scan using the -sU option.

	sudo nmap -sU TARGET_IP_ADDRESS

----------------------------------------------------------------------------------------------------------
 Options & Tuning

-p: to choose the port to scan
-T<0-5>: to choose the timing of the scanning control. T0 (paranoid) scan one port and wait 5 minutes before attempting the next one.
--min-rate <#packet>: Used to control the packet rate used
--max-rate <#packet>

Nmap probes the targets to discover which hosts are live and which ports are open; probing parallelization specifies the number of such probes that can be run in parallel
--min-parallelism <numprobes>: Used to control probing parallelization
--max-parallelism <numprobes>

| Port Scan Type	| Example Command |
| ----- | ----- |
| TCP Connect Scan |	nmap -sT 10.10.25.236 |
|TCP SYN Scan |	sudo nmap -sS 10.10.25.236 |
| UDP Scan |	sudo nmap -sU 10.10.25.236 |


| Option | Purpose |
| -p- |	all ports|
|-p1-1023	| scan ports 1 to 1023	|
|-F	| 100 most common ports	|
|-r	| scan ports in consecutive order	|
|-T<0-5>	| -T0 being the slowest and T5 the fastest	|
|--max-rate 50	| rate <= 50 packets/sec |
|--min-rate 15 |	rate >= 15 packets/sec |
|--min-parallelism 100 | at least 100 probes in parallel |

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

## Advanced Type of Port Scanning

- Null Scan
- FIN Scan
- Xmas Scan
- Maimon Scan
- ACK Scan
- Window Scan
- Custom Scan

----------------------------------------------------------------------------------------------------------
- Null Scan

TCP Scan that set all TCP Flag to 0.
+ No response = the port might be **open** or **filtered** by the firewall
+ RST/ACK = the port is closed

Require Priviledges: YES
Option: -sN

	sudo nmap -sN MACHINE_IP

Useless against Stateful Firewall!

----------------------------------------------------------------------------------------------------------
- FINScan

TCP Scan that set FIN flag to 1 and the other to 0.
+ No response = the port might be **open** or **filtered** by the firewall
+ RST/ACK = the port is closed

Note: some firewall might silently drop the packet silenty without RST response

Require Priviledges: YES
Option: -sF

	sudo nmap -sF MACHINE_IP

Useless against Stateful Firewall!

----------------------------------------------------------------------------------------------------------
- XMAS Scan

TCP Scan that set FIN, PUSH, URG to 1, the others to 0.
+ No response = the port might be **open** or **filtered** by the firewall
+ RST/ACK = the port is closed

Require Priviledges: YES
Option: -sX

	sudo nmap -sX MACHINE_IP

Useless against Stateful Firewall!

----------------------------------------------------------------------------------------------------------
- Maimon Scan (more or less useless)

TCP Scan that set FIN, ACK to 1, the others to 0.
+ RST response = the port might be **open** or **closed**

Require Priviledges: YES
Option: -sM

	sudo nmap -sM MACHINE_IP

----------------------------------------------------------------------------------------------------------
- ACK Scan: Use not for capturing which ports is open on the host whether how the front-end firewall has been configured

TCP Scan that set ACK flag to 1
+ RST = the port on the host is closed or open but is not filtered by the firewall
+ No Response = Port is blocked/filtered by the firewall 

Note: ACK flag should be set only in relation the acknowledgement of a received packet. In other case it would just get the host to reply with a RST.

Require Priviledges: YES
Option: -sA

	sudo nmap -sA MACHINE_IP

----------------------------------------------------------------------------------------------------------
- Window Scan: Use not for capturing which ports is open on the host whether how the front-end firewall has been configured

TCP Scan that set ACK flag to 1
+ RST = the port on the host is closed or open but is not filtered by the firewall
+ No Response = Port is blocked/filtered by the firewall 

Note: ACK flag should be set only in relation the acknowledgement of a received packet. In other case it would just get the host to reply with a RST.

Require Priviledges: YES
Option: -sW

	sudo nmap -sW MACHINE_IP

The main difference with ACK Scan is that ports that will not be blocked by the firewall are listed as "closed" whether the ACK Scan would labeled them as "unfiltered"
----------------------------------------------------------------------------------------------------------
- Custom Scan

User decide which flag to set by specifing it.

Require Priviledges: YES
Option: --scanflags <URGACKPSHRSTSYNFIN>

	sudo nmap --scanflags <flags> MACHINE_IP

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

- Spoofing & Decoys

In some network setups, you will be able to scan a target system using a spoofed IP address and even a spoofed MAC address. Such a scan is only beneficial in a situation where you can guarantee to capture the response.

### Spoofing IP Address:
1. Attacker sends a packet with a spoofed source IP address to the target machine.
2. Target machine replies to the spoofed IP address as the destination.
3. Attacker captures the replies to figure out open ports.
	
	nmap -S SPOOFED_IP MACHINE_IP

Note: In general, you expect to specify the network interface using -e and to explicitly disable ping scan -Pn. Therefore, instead of nmap -S SPOOFED_IP MACHINE_IP, you will need to issue nmap -e NET_INTERFACE -Pn -S SPOOFED_IP MACHINE_IP to tell Nmap explicitly which network interface to use and not to expect to receive a ping reply.

--spoof-mac SPOOFED_MAC

### Decoy: make the scan appears to be coming from many IP addresses so that the attacker’s IP address would be lost among them

You can launch a decoy scan by specifying a specific or random IP address after <mark>-D</mark>. 

	nmap -D 10.10.0.1,10.10.0.2,ME MACHINE_IP 

will make the scan of MACHINE_IP appear as coming from the IP addresses 10.10.0.1, 10.10.0.2, and then ME to indicate that your IP address should appear in the third order. 

	nmap -D 10.10.0.1,10.10.0.2,RND,RND,ME MACHINE_IP

where the third and fourth source IP addresses are assigned randomly, while the fifth source is going to be the attacker’s IP address.

----------------------------------------------------------------------------------------------------------
- Fragmented Packets: using IP payload fragmentation to avoid firewall and IDS content inspection and detecion

Fragmentation in Nmap can be achieved using the option -f which split the IP payload (and therefore the TCP header) in chuncks of 8 bytes.

-ff will split the payload in chunks of 16 bytes
--mtu to change the default value of 8
--data-length NUM: if you prefer to increase the size of your packets to make them look innocuous, you can use the option, where num specifies the number of bytes you want to append to your packets.

----------------------------------------------------------------------------------------------------------

- Idle/Zombie Scan

The idle scan, or zombie scan, requires an idle system connected to the network that you can communicate with (such a printer).

	nmap -sI ZOMBIE_IP MACHINE_IP

The idle (zombie) scan requires the following three steps to discover whether a port is open:

1. Trigger the idle host to respond so that you can record the current IP ID on the idle host.
"The attacker system probing an idle machine, a multi-function printer. By sending a SYN/ACK, it responds with an RST packet containing its newly incremented IP ID."

2. Send a SYN packet to a TCP port on the target. The packet should be spoofed to appear as if it was coming from the idle host (zombie) IP address.

"The attacker will send a SYN packet to the TCP port they want to check on the target machine in the next step. However, this packet will use the idle host (zombie) IP address as the source. Three scenarios would arise:
+ TCP port is closed therefore, the target machine responds to the idle host with an RST packet. The idle host does not respond; hence its IP ID is not incremented.
+  TCP port is open, so the target machine responds with a SYN/ACK to the idle host (zombie). The idle host responds to this unexpected packet with an RST packet, thus incrementing its IP ID.
+ Target machine does not respond at all due to firewall rules. This lack of response will lead to the same result as with the closed port; the idle host won’t increase the IP ID."

3. Trigger the idle machine again to respond so that you can compare the new IP ID with the one received earlier.

"Attacker sends another SYN/ACK to the idle host. The idle host responds with an RST packet, incrementing the IP ID by one again. The attacker needs to compare the IP ID of the RST packet received in the first step with the IP ID of the RST packet received in this third step. If the difference is 1, it means the port on the target machine was closed or filtered. However, if the difference is 2, it means that the port on the target was open."


Other Extra Options:
--reason: based on what NMAP got to that conclusion
-v or -vv: verbose
-d or -dd: debug mode with output report

| Port Scan Type |	Example Command |
| ----- | ----- |
| TCP Null Scan	|sudo nmap -sN 10.10.206.188 |
| TCP FIN Scan	|sudo nmap -sF 10.10.206.188 |
| TCP Xmas Scan	|sudo nmap -sX 10.10.206.188 |
| TCP Maimon Scan |	sudo nmap -sM 10.10.206.188 |
| TCP ACK Scan	| sudo nmap -sA 10.10.206.188 |
| TCP Window Scan |	sudo nmap -sW 10.10.206.188 |
| Custom TCP Scan |	sudo nmap --scanflags URGACKPSHRSTSYNFIN 10.10.206.188 |
| Spoofed Source IP |	sudo nmap -S SPOOFED_IP 10.10.206.188 |
| Spoofed MAC Address |	--spoof-mac SPOOFED_MAC |
| Decoy Scan |	nmap -D DECOY_IP,ME 10.10.206.188 |
| Idle (Zombie) Scan |	sudo nmap -sI ZOMBIE_IP 10.10.206.188 |
| Fragment IP data into 8 bytes	| -f |
| Fragment IP data into 16 bytes	| -ff |

| Option	| Purpose |
| ----- | ----- |
| --source-port PORT_NUM	| specify source port number |
| --data-length NUM | append random data to reach given length |
| --reason	 | explains how Nmap made its conclusion |
| -v	| verbose |
| -vv	| very verbose |
| -d |	debugging |
| -dd |	more details for debugging |

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

1. Detect versions of the running services (on all open ports)

Adding <mark>-sV</mark> to your Nmap command will collect and determine service and version information for the open ports. You can control the intensity with --version-intensity LEVEL where the level ranges between 0, the lightest, and 9, the most complete. -sV --version-light has an intensity of 2, while -sV --version-all has an intensity of 9.

!IMPORTANT: It is important to note that using -sV will force Nmap to proceed with the TCP 3-way handshake and establish the connection.!

2. Detect the OS based on any signs revealed by the target

Nmap need to find at least one open port

-O option


3. Run Nmap’s traceroute

--traceroute Send packet with high TTL and keep decreasing it

4. Run select Nmap scripts

Current installation include more than 600 scripts for Nmap (folder /usr/share/nmap/scripts).
To use the default scripts -sC or --script=default
To use a specific script --script "Script Name"

| Script Category	| Description |
| ----- | ----- |
| auth |	Authentication related scripts |
| broadcast |	Discover hosts by sending broadcast messages |
| brute |	Performs brute-force password auditing against logins |
| default |	Default scripts, same as -sC |
| discovery |	Retrieve accessible information, such as database tables and DNS names |
| dos |	Detects servers vulnerable to Denial of Service (DoS )|
| exploit |	Attempts to exploit various vulnerable services |
| external |	Checks using a third-party service, such as Geoplugin and Virustotal |
| fuzzer |	Launch fuzzing attacks |
| intrusive |	Intrusive scripts such as brute-force attacks and exploitation |
| malware |	Scans for backdoors |
| safe |	Safe scripts that won’t crash the target |
| version |	Retrieve service versions |
| vuln |	Checks for vulnerabilities or exploit vulnerable services |

5. Save the scan results in various formats

Normal - as the CLI output
-oN
----------------------------------------------------------------------------------------------------------
Grepable - so that we can grep on the filename based on [keyword]
-oG FILENAME 
----------------------------------------------------------------------------------------------------------
XML
-oX FILENAME
----------------------------------------------------------------------------------------------------------


| Option |	Meaning |
| ----- | ----- |
| -sV	| determine service/version info on open ports |
| -sV --version-light |	try the most likely probes (2) |
| -sV --version-all |	try all available probes (9) |
| -O	|detect OS |
| --traceroute |	run traceroute to target |
| --script=SCRIPTS |	Nmap scripts to run |
| -sC or --script=default |	run default scripts |
| -A	| equivalent to -sV -O -sC --traceroute |
| -oN |	save output in normal format |
| -oG |	save output in grepable format |
| -oX |	save output in XML format |
| -oA |	save output in normal, XML and Grepable formats |



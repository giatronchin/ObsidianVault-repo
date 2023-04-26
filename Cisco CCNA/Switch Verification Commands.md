#switch #cisco

The table summarizes some of the more useful switch verification commands.

|**Task**|**IOS Commands**|
| --- | --- |
|Display interface status and configuration.|S1# **show interfaces** [_interface-id_]|
|Display current startup configuration.|S1# **show startup-config**|
|Display current running configuration.|S1# **show running-config**|
|Display information about flash file system.|S1# **show flash**|
|Display system hardware and software status.|S1# **show version**|
|Display history of command entered.|S1# **show history**|
|Display IP information about an interface.|S1# **show ip interface** [_interface-id_] OR S1# **show ipv6 interface** [_interface-id_]|
|Display the MAC address table.|S1# **show mac-address-table** OR S1# **show mac address-table**|

### Network Access Layer Issues

Based on the output of the **show interfaces** command, possible problems can be fixed as follows:

-   If the interface is up and the line protocol is down, a problem exists. There could be an encapsulation type mismatch, the interface on the other end could be error-disabled, or there could be a hardware problem.
-   If the line protocol and the interface are both down, a cable is not attached, or some other interface problem exists. For example, in a back-to-back connection, the other end of the connection may be administratively down.
-   If the interface is administratively down, it has been manually disabled (the **shutdown** command has been issued) in the active configuration.

The same command output a bunch of statistics about the interfaces. Some are collected and define as below:
|**Error Type**| **Description**|
| --- | --- |
|**Input Errors**|Total number of errors. It includes runts, giants, no buffer, CRC, frame, overrun, and ignored counts.|
|**Runts**|Frames that are discarded because they are smaller than the minimum frame size for the medium. For instance, any Ethernet frame that is less than 64 bytes is considered a runt.|
|**Giants**|Frames that are discarded because they exceed the maximum frame size for the medium. For example, any Ethernet frame that is greater than 1,518 bytes is considered a giant.|
|**CRC**|CRC errors are generated when the calculated checksum is not the same as the checksum received.|
|**Output Errors**|Sum of all errors that prevented the final transmission of datagrams out of the interface that is being examined.|
|**Collisions**|Number of messages retransmitted because of an Ethernet collision.|
|**Late Collisions**|A collision that occurs after 512 bits of the frame have been transmitted.|

-   **Runt Frames** - Ethernet frames that are shorter than the 64-byte minimum allowed length are called runts. Malfunctioning NICs are the usual cause of excessive runt frames, but they can also be caused by collisions.
-   **Giants** - Ethernet frames that are larger than the maximum allowed size are called giants.
-   **CRC errors** - On Ethernet and serial interfaces, CRC errors usually indicate a media or cable error. Common causes include electrical interference, loose or damaged connections, or incorrect cabling. If you see many CRC errors, there is too much noise on the link and you should inspect the cable. You should also search for and eliminate noise sources.
-  **Collisions** - Collisions in half-duplex operations are normal. However, you should never see collisions on an interface configured for full-duplex communication.
-   **Late collisions** - A late collision refers to a collision that occurs after 512 bits of the frame have been transmitted. Excessive cable lengths are the most common cause of late collisions. Another common cause is duplex misconfiguration. For example, you could have one end of a connection configured for full-duplex and the other for half-duplex. You would see late collisions on the interface that is configured for half-duplex. In that case, you must configure the same duplex setting on both ends. A properly designed and configured network should never have late collisions.
#cisco #router #switch #management
 #ssh #console

# Secure Access to Priviledge Mode

```
R1(config)# enable password CCNA
```

Note: we choose 'CCNA' as password. This field is **case-sensitive**.
Password can still be read as clear text in the configuration file of the device:
```
R1# show running-config
Building configuration...

Current configuration : 719 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
!
hostname R1
!
!
!
enable password CCNA
!
```

To hidden that content with encryption we can use the following command:

```
R1(config)# service password-encryption 
```

This command will use by default the encryption algorithm from Cisco (e.g code **7**):
```
R1# show running-config
Building configuration...

Current configuration : 719 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
!
hostname R1
!
!
!
enable password 7 08026F6028
!
```

Encryption mode 7 is not such secure protocol therefore is a best practice to use a different encryption mode, like 5 (**MD5**):

```
R1(config)# enable secret CCNA
```

This mode does not require `service password-encryption`. `enable secret` take precedence over  `enable password`.

## To remove/undone a command previously issued

```
R1(config)# no command-issued
```

# Secure Console/AUX Connection

This method provides no accountability and the password is sent in plaintext.
```
R1(config)# line vty 0 4
R1(config-line)# password ci5c0
R1(config-line)# login
```

---

# Configure SSH
Before configuring SSH, the switch must be minimally configured with a unique hostname and the correct network connectivity settings.

-   It requires a **username** and a **password**, both of which are **encrypted during transmission**.
-   The username and password can be **authenticated** by the **local database method**.
-   It provides more **accountability** because the **username is recorded when a user logs in**.

## Step 1 - Verify SSH support

Use the **show ip ssh** command to verify that the switch supports SSH. If the switch is not running an IOS that supports cryptographic features, this command is unrecognized.

```S1# show ip ssh```

## **Step 2** - **Configure the IP domain.**

Configure the IP domain name of the network using the **ip domain-name** _domain-name_ global configuration mode command. In the figure, the _domain-name_ value is **cisco.com**.

```
S1(config)# ip domain-name cisco.com
```

## **Step 3** - **Generate RSA key pairs.**

Not all versions of the IOS default to SSH version 2, and SSH version 1 has known security flaws. To configure SSH version 2, issue the **ip ssh version 2** global configuration mode command. Generating an RSA key pair automatically enables SSH. Use the **crypto key generate rsa** global configuration mode command to enable the SSH server on the switch and generate an RSA key pair. When generating RSA keys, the administrator is prompted to enter a modulus length. The sample configuration in the figure uses a modulus size of 1,024 bits. A longer modulus length is more secure, but it takes longer to generate and to use.

**Note**: To delete the RSA key pair, use the **crypto key zeroize rsa** global configuration mode command. After the RSA key pair is deleted, the SSH server is automatically disabled.

```
S1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024

--------------

S1(config)# crypto key generate rsa general-key modulus 2048
```

## **Step 4** - **Configure user authentication.**

The SSH server can authenticate users locally or using an authentication server. To use the local authentication method, create a username and password pair using the **username** _username_ **secret** _password_ global configuration mode command. In the example, the user admin is assigned the password ccna.

```
S1(config)# username Admin secret ccna
```

## **Step 5** - **Configure the vty lines.**

Enable the SSH protocol on the vty lines by using the **transport input ssh** line configuration mode command. The Catalyst 2960 has vty lines ranging from 0 to 15. This configuration prevents non-SSH (such as Telnet) connections and limits the switch to accept only SSH connections. Use the **line vty** global configuration mode command and then the **login local** line configuration mode command to require local authentication for SSH connections from the local username database.

```
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# exit
```

## **Step 6** - **Enable SSH version 2.**

By default, SSH supports both versions 1 and 2. When supporting both versions, this is shown in the **show ip ssh** output as supporting version 2. Enable SSH version using the **ip ssh version 2** global configuration command.

```
S1(config)# ip ssh version 2
```

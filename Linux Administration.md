# Linux
#linux

## Add entry to repository for updates
By default `apt-key add` adds a key to `/etc/apt/trusted.gpg` . To list all the keys:
```
 sudo apt-key list
```

To remove gpg key from it issue the command:

```
sudo apt-key del <last-2-chuncks-id>
```

To remove a repository from apt packege manager:

```bash
sudo add-apt-repository -r <package-repo-name>
```

-----------------------------------------------------------------
## Certificate generation
To generate https **certificate** and **private key**:

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout private.key -out certificate.crt
```

-   req: This subcommand enables certificate request and certificate generating utility
-   -x509: this option outputs a self signed certificate instead of an external certificate request
-   -nodes: this option requires a not encrypted private key
-   -days 356: this option sets expiration days to 365. default value is 30 days
-   -newkey rsa:2048: this option creates a new certificate request and a new private key. The argument rsa:nbits, where nbits is the number of bits, generates an RSA key nbits in size.
-   -keyout: This options set private key name (optional, using a full path you can also put key in desired folder)
-   -out: This option set certificate name (optional, using a full path you can also put certificate in desired folder)

To leverage a CA Certification Authority (let's encrypt for example use)
```bash
sudo apt install certbot

certbot -certonly --standalone
```

More [here](https://certbot.eff.org/instructions)

-----------------------------------------------------------------
## Create and install Linux services

1. Create a  `.service` file with the configuration of the service
   
   ```bash
# Remember Add x permission on script!!!

[Unit]
Description=service_name

[Service]
User=root #User that will run the service
WorkingDirectory=/home/teleBot #Working directory 
ExecStart=/home/teleBot/bot.py #Location of the startup_script
Restart=always

[Install]
WantedBy=multi-user.target #Installation for all user on the system
   ```
   
2.  Place it in `/etc/systemd/system`
3.  Restart daemons with `sudo systemctl daemon-reload`
4. Enable service for startup at boot with `sudo systemctl enable example.service`


-----------------------------------------------------------------
List all group on the system:

	/etc/group

List all users, group associated:

	/etc/passwd

Create a new group // Create new user (require sudo priv):

```bash
groupadd <group-name>

useradd <username>
```

Add a user to a specific group (require sudo priv):

```bash
usermod -aG <group-name> <username>
```

Remove a user to a specific group:
```bash
gpasswd -d <username> <group-name>
```

Remove a specific user:
```bash
userdel <username>
```

-----------------------------------------------------------------

In terminal help/docs about commands

```bash

curl cheat.sh/<command>

```

-----------------------------------------------------------------

Where to place script or link to scripts in Linux OS

```bash
sudo mv /path/to/script /usr/local/bin/

or
  
sudo ln -s /path/to/script /usr/local/bin/
```

-----------------------------------------------------------------
To print personalized message at ssh login to a server you need to edit two files:

1. `/etc/motd` (Message of the Day)

2. `/etc/ssh/sshd_config`: Change the setting `PrintLastLog` to "no", this will disable the "Last login" message. `PrintMotd` to "yes".
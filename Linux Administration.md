# Linux
#linux


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


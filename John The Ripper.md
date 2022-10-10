# John
#john-the-ripper

John is used to crack hashes. Althought there are numerous format of hash, there is a python script that can help detect the right one for us.

### Hash-Id  
Here the repository [Git](https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py)
```python
	python3 hash-id.py
```

Common hash-cracking use case:
```bash
	john --format=[format] --wordlist=[path to wordlist] [path to file]
```

Single-Mode hash-cracking use case:
```bash
	john --single --format=[format] [path to file]
```

In this case we are not providing any wordlist to John, which will then leverage only **Word Mangling** on the info inside the hashed file (like _username_).

We can also leverage on our own rules for cracking hashes based on common criteria/positioning/info on the target we have collected. [[Customer Rules]]

#### Unshadowing

To crack /etc/shadow passwords, we must combine it with the /etc/passwd file in order for John to understand the data it's being given. To do this, we use a tool built into the John suite of tools called unshadow. The basic syntax of unshadow is as follows:

`unshadow [path to passwd] [path to shadow] >> [output_file]`

`unshadow` - Invokes the unshadow tool  

`[path to passwd]` - The file that contains the copy of the /etc/passwd file you've taken from the target machine  

`[path to shadow]` - The file that contains the copy of the /etc/shadow file you've taken from the target machine

#### Zip2John

The zip2john tool convert  zip file into a hash format that John is able to understand, and hopefully crack. The basic usage is like this:

```bash
	 zip2john [options [zip file] > [output file]
```

`[options]` - Allows you to pass specific checksum options to zip2john, this shouldn't often be necessary  

`[zip file]` - The path to the zip file you wish to get the hash of

`>` - This is the output director, we're using this to send the output from this file to the...  

`[output file]` - This is the file that will store the output from

#### Rar2John

Almost identical to the zip2john tool that we just used, we're going to use the rar2john tool to convert the rar file into a hash format that John is able to understand. The basic syntax is as follows:  

`rar2john [rar file] > [output file]   `

`rar2john` - Invokes the rar2john tool  

`[rar file]` - The path to the rar file you wish to get the hash of

`>` - This is the output director, we're using this to send the output from this file to the...  

`[output file]` - This is the file that will store the output from

#### SSH2John

To crack the SSH _private key password_ of **id_rsa file** we need to converts the id_rsa private key that you use to login to the SSH session into hash format that john can work with.

`ssh2john [id_rsa private key file] > [output file]   `

`ssh2john` - Invokes the ssh2john tool  

`[id_rsa private key file]` - The path to the id_rsa file you wish to get the hash of

`>` - This is the output director, we're using this to send the output from this file to the...  

`[output file]` - This is the file that will store the output from
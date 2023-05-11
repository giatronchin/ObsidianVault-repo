#linux
#bash is a shell that is present in most Linux based distros.

Check which shell you are actually using in your terminal:

	echo $SHELL ---> Return location for that shell

Usually binary is inside **/usr/bin/bash**.

--------------------------------------------------
## How to build a script:

1. Create a file were you will place the commands you want to execute
2. Change the permission over the file so that your user can execute it
```bash
chmod +x filename.sh
```
3. ShaBang - @top of the file, tell the terminal with binary of the interpreter to be used in the following (find out the absolute path with _which_ command)
```bash
#!/bin/bash
#!/usr/bin/python
```
4. List all commads
5. Execute the script
```bash
./script-filename.sh
```
--------------------------------------------------
## Variables

```bash
variableName="Something" #Declaration

echo $variableName #Print(Recall) variable content
```

```bash
myname="Gigi"

echo "Ciao il mio nome è $myname" #Will print "Ciao il mio nome è Gigi"
echo 'Ciao il mio nome è $myname' #Will print "Ciao il mio nome è $myname"
```
**Note**: single-quotes vs double-quotes makes all the difference when it comes to variable evaluation!

```bash
echo 'Questo è come stampare l\'apostrofo ' #The escape character \ allow us to use single-quotes string 
```

**Note**: Variable declaration is tied with the specific session. They don't persist once the specific shell they where created initially is closed.

### Grab output of a command

Throw the output of the 'ls' command inside the files variable. This levarage a **sub-shell** where the list storage command is executed (in the background) and the output piped as the variable content.

```bash
files=$(ls)
```
--------------------------------------------------

## Math
Need to specify bash that we want to 'evaluate' an expression.

```bash
expr 30 + 10 #30
expr 30 / 10 #3
expr 30 \* 10 #300

mynum=100

expr mynum + 50 #150
```
--------------------------------------------------

## If-Else

Note: _Spacing is very important for the syntax of the if-else statement_
```bash
#!/bin/bash

mynum=200

if [ $mynum -eq 200 ] # -eq expression evaluation "equal to"
then
	echo "The condition is true."
else
	echo "The variable does not equal 200"
fi

```
Check for the presence of a file in the filesystem:
```bash
#!/bin/bash

if [ -f ~/myfile ]
then
	echo "The file exists."
else
	echo "The file does not exists."
fi

```
Check if htop is installed, otherwise install it, and run it:
```bash
#!/bin/bash

command=/usr/bin/htop

if [ -f $command ]
then
	echo "$command is available, let's run it."
else
	echo "$command is NOT available, installing it..."
	sudo apt update && sudo apt install -y htop 
	# && awaits for the succesful execution of the first portion and then execute the second part
fi

$command
```
Using the [ ] bracket, bash assume that you want to run the '_test_' command over what is inside the brackets.

Better version:
```bash
#!/bin/bash

command=htop

if command -v $command #check for the exist of the command within the $command
then
	echo "$command is available, let's run it."
else
	echo "$command is NOT available, installing it..."
	sudo apt update && sudo apt install -y htop 
	# && awaits for the succesful execution of the first portion and then execute the second part
fi

$command

```

List of flags useful for expression evaluation:
| Flag | Description |
| ----- |----- |
| eq | Equal to |
| ne | Not equal to |
| gt | Greater than |
| f | Check if File exist on the filesystem |
| d | Check if Directory exist on the filesystem |

Some external reference [here]([Introduction to if (tldp.org)](https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html))

--------------------------------------------------
## Exit Code
Help determine wether or not a command was successfully executed.

```bash
ls -l

echo $? #Print the status exit code of the last executed command
```
Commonly exit code of 0 means the command was successful, while different code means that something went wrong.

Map of the **common exit code**:

| Code | Description |
| ----- |----- |
| 0 | Success |
| 127 | If a command is not found, the child process created to execute it returns 127.|
| 126 | If a command is found but is not executable, the return status is 126. |

### Redirections

| Code | Description |
| ----- |----- |
| >> | Redirect output to (append mode) |

--------------------------------------------------

## Loops

#### While-loops

Example 1:
```bash
#!/bin/bash

myvar=1

while [ $myvar -le 10 ] # Test condition between []
do
	echo $myvar
	myvar=$(( $myvar + 1 )) #Replace old myvar variable
	sleep 0.5
done
```

Example 2: check for file presence
```bash
#!/bin/bash

myvar=1

while [ -f ~/testfile ] #Flag -f check for file existance
do
	echo "The test file exists."
done

echo "The file no longer exists."
```

#### for-loops
For loops need a list of item to iterate over like in the example below:
```bash
#!/bin/bash

for current_number in 1 2 3 4 5 6 7 8 9 10
do
	echo $current_number
	sleep 1
done

echo "This is outside of the for loop"

```

Another example with a more efficient way to represent a list of consecutive number:
```bash
#!/bin/bash

for current_number in {1..10}
do
	echo $current_number
	sleep 1
done

echo "This is outside of the for loop"
```

Another example:

```bash
#!/bin/bash

#Iterate over every file that ends in ".log" in logfiles/ directory:
for file in logfiles/*.log 
do
	tar -czvf $file.tar.gz $file #Create a tarball archive for each file
	rm $file #Remove uncompressed file
done

```

--------------------------------------------------

## Data Streams

### Stdin
When we are accepting input from the user.
```bash
#!/bin/bash

echo "Please enter your name:"
read name #read input from keyboard and capture it in the name variable
echo "Your name is: $name"
```

### Stdout
Every output from a command printed on the screen/prompt (except error, **exit code equals 0**)

### Stderr
Every output from a command printed on the screen/prompt **with exit code different from 0**

```bash
find /etc -type f 2>/dev/null   #/dev/null is a black hole in the linux fs
```

| Redirect-Cmd | Description |
| ----- |----- |
| > | Redirect Stdout(default implicit) to somewhere |
| 1> |  Redirect Stdout to somewhere |
| 2> |  Redirect Stderr to somewhere |
| &> |  Redirect Stdout and Stderr to somewhere |


--------------------------------------------------

## Universal Update Script

```bash
#!/bin/bash

if [ -d /etc/pacman.d ] #Check if a directory exists
then
	#The host is based on Arch, run the pacman update command
	sudo pacman -Syu
fi

if [ -d /etc/apt ]
then
	#The host is based on Debian or Ubuntu
	#Run the apt version of the command
	sudo apt update #Check for repository updates and refresh package index
	sudo apt dist-upgrade
fi

```

Improved version-1
```bash
#!/bin/bash

release_file=/etc/os-release #Common on most distribution 

#Flag -q stands for "quiet mode" which avoid printing anything on the prompt
if grep -q "Arch" $release_file 
then
	#The host is based on Arch, run the pacman update command
		sudo pacman -Syu
fi

#Evualate to true if either file contains "Ubuntu" or "Debian"
if grep -q "Ubuntu" $release_file || grep -q "Debian" $release_file
then
	#The host is based on Debian or Ubuntu
	#Run the apt version of the command
	sudo apt update #Check for repository updates and refresh package index
	sudo apt dist-upgrade
fi
```

Improved with data streams:

```bash
#!/bin/bash

release_file=/etc/os-release #Common on most distribution 
logfile=/var/log/updater.log
errorlog=/var/log/updater_errors.log


#Flag -q stands for "quiet mode" which avoid printing anything on the prompt
if grep -q "Arch" $release_file  
then
	#The host is based on Arch, run the pacman update command
	sudo pacman -Syu 1>>$logfile 2>>$errorlog
	if [ $? -ne 0 ]
	then
		echo "An error occurred, please check the $errorlog file."
	fi
fi

#Evualate to true if either file contains "Ubuntu" or "Debian"
if grep -q "Ubuntu" $release_file || grep -q "Debian" $release_file
then
	#The host is based on Debian or Ubuntu
	#Run the apt version of the command
	sudo apt update 1>>$logfile 2>>$errorlog
	if [ $? -ne 0 ]
	then
		echo "An error occurred, please check the $errorlog file."
	fi
	sudo apt dist-upgrade -y 1>>$logfile 2>>$errorlog
	if [ $? -ne 0 ]
	then
		echo "An error occurred, please check the $errorlog file."
	fi
fi
```

--------------------------------------------------
List of **command available**:
| Command | Description |
| ----- |----- |
| ls _path_ | list storage |
| which _program_name_ | which/where is the absolute path to the binary of this program (if it exist on the OS) |
| chown _file/folder_ _new_owner_username_  | Change owner of a file/folder |
| chmod _file/folder_ | change permission on a file/folder |
| pwd | return current absolute path |
| date | return current date and time |
| find | Allow to find file/directory on fs with provided criteria. (File: -type f, Directory: -type d) |
| grep | Search for a specific string within something |
| env | print all environment variable accessible by the current user |
| fg | bring up background shell |
| tail | Print out in almost realtime the end of the file |
| apt update | Synchronize with package repository (List Available Updated Package) |
| command _command_ | Wether or not a command is available |
| test | Evaluate a test expression |
| exit _integer_ | Force the exit for the subshell and the exit code to be a specific value given by the user. (Further line of the script wont be executed) |

List of useful **system variable**:
"_Typically all uppercase variable names are used for system-variable or environment variable (env)_"

| Variable | Description |
| ----- |----- |
| USER | Current user logged in |
| ? | Exit status code of the last executed command |
| PATH | All path directory that the shell will look when asked to execute a command |


List of useful **shortcuts key**:
| Shortcuts | Description |
| ----- |----- |
| Ctrl+Z | Sends to background current shell (suspension) |

## FileSystem Hierarchy Standard

[lsb:fhs [Wiki] (linuxfoundation.org)](https://wiki.linuxfoundation.org/lsb/fhs)

**/usr/local/bin** would be a perfect directory to put our scripts.

```bash
sudo mv script.sh /usr/local/bin/script #Removing extension since it is not needed

sudo chown root:root /usr/local/bin/script #Nono that is not root cannot change its content
```
The content of this directory will make the script accessible from wherever you are on in the filesystem (_because $PATH contains /usr/local/bin/_).

Note: add a new location to the Path variable use the following command:
```bash
export PATH=/new/path:$PATH
```

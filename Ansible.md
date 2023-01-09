#ansible #linux 

## SSH configuration

```bash
# On each Worker node
sudo apt update && sudo apt install openssh-server

# On the Control node
ssh-keygen -t ed25519 -C "$USER"
ssh-keygen -t ed25519 -C "ansible"

#From the Control node execute first ssh-connection, accepting the worker signature

ssh-copy-id -i user-ublickey.pub worker.ip.address 

#You may need to specify 'username@worker.ip.address' if default user does not match your current(logged) user on Control node

ssh-copy-id -i ansible-ublickey.pub worker.ip.address
```

## Git
1. Install git on control node
```bash
sudo apt install git
```
2. Create a remote repository (Github, Gitlab, etc)
3. Clone the remote repository
```bash
git clone URL.to.remote.rep
```
4. Config local setting for git on the control node
```bash
git config --global user.name "Name"
git config --global user.email "Name"
```
5.Config remote, adding SSH public key of the Control Node (on Github)
6.Eventually push changes to the remote repo
```bash
git push origin <remote-branch-name>
```

## Ansible

#### Installation
```bash
sudo apt install ansible
```
#### Inventory
Create a file that contains the list of each of the host (worker) that our Controller needs to interact with.
```bash
$ nano inventory
ip.address.1
ip.address.2
ip.address.3
ip.address.4
```

### First run
Given the ssh-key, the list with all the worker host, it will execute the _module_ **ping** on all of them.
```bash
ansible all --key-file ~/.ssh/ansible -i inventory -m ping
```
Ping module would rather execute an attempt to ssh-connect with each and every single host listed.

#### Create a config file
This file is read by ansible each time it runs. Local _ansible.cfg_ will overwrite the one present in the **/etc/ansible**
```bash
$ nano ansible.cfg
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible


$ ansible all -m ping #Will work directly on the settings provided by the cfg file
```

#### Other Available Command
```bash
ansible all --list-hosts #Print list of hosts

ansible all -m gather_facts #Print all the info ansible is able to pull from the worker server

ansible all -m gather_facts --limit ip.address.1 #Same as before but target a single host

#Following the same as sudo apt update
ansible all -m apt -a update_cache=true --become --ask-become-pass 
#Option -a stands for arguments. Following Become is asking to escalate priviledges to run the module (will prompt asking password)
```
**Note**: the last command assume that every single host has the same root password

[Ansible Documentation — Ansible Documentation](https://docs.ansible.com/ansible/latest/)

```bash
#To install on all worker the vim-nox package(if it is not already)
ansible all -m apt -a name=vim-nox --become --ask-become-pass

#Same as apt upgrade on vim-nox
ansible all -m apt -a "name=vim-nox state=latest" --become --ask-become-pass

#Same as apt dist-upgrade
ansible all -m apt -a "upgrade=dist" --become --ask-become-pass

```
Multiple arguments( -a ) has to be with " ". [ansible.builtin.apt module - Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)

#### Playbooks

```yaml
---

- hosts: all
  become: true
  tasks:

  - name: install apache2 package
    apt:
      name: apache2

```
Now to run the above playbook:
```bash
ansible-playbook --ask-become-pass install_apache.yml
```
To limit the playbook execution on a single host we can use the option `--limit host.ip.address`
```bash
ansible-playbook --ask-become-pass install_apache.yml --limit 192.168.1.9
```

For example to remove the package, change the "desidered-state" to **absent**:
```yaml
---

- hosts: all
  become: true
  tasks:

  - name: apt update
    apt:
      update_cache: yes

  - name: install apache2 package
    apt:
      name: apache2
      state: latest

  - name: remove apache2 package
    apt:
      name: apache2
      state: absent

```

### When
Differentiate playbook execution based on host distribution for example.

```yaml
---

- hosts: all
  become: true
  tasks:

  - name: apt update
    apt:
      update_cache: yes
    when: ansible_distribution == "ubuntu"

  - name: install apache2 package
    apt:
      name: apache2
      state: latest
	when: ansible_distribution == "ubuntu"

```
The example will run the task only if it finds out the host distro is "ubuntu". We can leverage parameters fetch from ansible in the `gather_facts` to compose the _when_ condition (just use the parameter keyword).

To check against multiple parameter:
```yaml
---

- hosts: all
  become: true
  tasks:

  - name: apt update
    apt:
      update_cache: yes
    when: ansible_distribution in ["Debian", "Ubuntu"]

  - name: install apache2 package
    apt:
      name: apache2
      state: latest
	when: ansible_distribution in ["Debian", "Ubuntu"]  and ansible_distribution_version == "8.2"

```
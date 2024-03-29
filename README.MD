# Ansible in Windows 10

* Run Ansible in Windows 10

## Install the Windows Subsytem for Linux

* [Installation Guide for Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
* [Install Ubuntu 18.04 LTS](https://www.microsoft.com/en-sg/p/ubuntu-1804-lts/9n9tngvndl3q?rtc=1) 

### Check Ubuntu Version

```bash
$ lsb_release -a
```

## Install Ansible in Windows Subsystem

* [Installation Guide for Ubuntu](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-releases-via-apt-ubuntu)

### Ansible Installation

```bash
$ sudo apt-get update && sudo apt-get install software-properties-common -y
$ sudo apt-add-repository universe -y
$ sudo apt-add-repository ppa:ansible/ansible -y
$ sudo apt-get update && sudo apt-get install ansible python-pip -y
```

### Python library for Windows Remote Management Installation

* Ansible uses the pywinrm package to communicate with Windows servers over WinRM. It is not installed by default with the Ansible package, but can be installed by running the following:

```bash
$ pip install "pywinrm>=0.3.0" 
$ pip install cryptography
```

### Check Ansible Version

```bash
$ ansible --version
$ ansible-playbook --version
```

## Install Code Insiders - Optional

* [Download Code Insider and Install](https://code.visualstudio.com/insiders/)

## Windows Client Configuration

> PowerShell in Administrator Mode

> Settings -> Network & Internet -> Change Connection Properties -> Network Profile -> Private 

```powershell
PS > Enable-PSRemoting
PS > Get-Service | Where-Object {$_.Name -eq "WinRM"}
PS > Start-Service WinRM
PS > Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
PS > winrm quickconfig
```

## Configure a Windows host for remote management with Ansible

```powershell
PS > $PSVersionTable.PSVersion
PS > .\scripts\ConfigureRemotingForAnsible.ps1
```

## Windows Modules

### Ping

```bash
$ ansible aviservers -m win_ping
```

* If ansible.cfg is configured in non NTFS file system, no need of adding inventort parameter, can run the below command.

```bash
$ ansible aviservers -m win_ping
```

### Get System Information

```bash
$ ansible aviservers -m setup
```

### Execute plain commands

```bash
$ ansible aviservers -m raw -a "dir"
$ ansible aviservers -m raw -a "ipconfig" 
```

### Execute service related commands

```bash
$ ansible aviservers -m win_service -a name="spooler"
$ ansible aviservers -m win_service -a "name=spooler state=stopped"
$ ansible aviservers -m win_service -a "name=spooler state=started"
```

### Execute feature related commands

```bash
$ ansible aviservers -m win_feature -a "name=Telnet-Client state=present"
```

## Run Playbooks

```bash
$ ansible-playbook playbooks/local/test.yml
$ ansible-playbook playbooks/config/ipconfig.yml
$ ansible-playbook playbooks/config/stats.yml
$ ansible-playbook playbooks/config/ping.yml
$ ansible-playbook playbooks/scripts/powershell.yml
$ ansible-playbook playbooks/user/create.yml
$ ansible-playbook playbooks/iis/enable.yml
$ ansible-playbook playbooks/packages/install/rdcm.yml
$ ansible-playbook playbooks/packages/uninstall/rdcm.yml
$ ansible-playbook playbooks/packages/install/firefox.yml
$ ansible-playbook playbooks/packages/uninstall/firefox.yml
```

## Issues

* The specified module 'ServerManager' was not loaded because no valid module file was found in any module directory.
    * Install [Remote Server Administration](https://www.microsoft.com/en-au/download/details.aspx?id=45520) Tools for Windows 10.

    ```powershell
    PS > Import-Module ServerManager
    PS > Get-WindowsFeature
    ```

## Known Issues

* win_feature module will work only in Windows Server edition. 

## Ubuntu Components for Ansible Kerberos Authentication

```bash
$ sudo apt-get update && sudo apt-get install software-properties-common -y
$ sudo apt-get install gcc python-dev libkrb5-dev
$ sudo apt-get install python3-pip
$ sudo apt-get install python-pip
$ python3 -m pip install --upgrade pip
$ sudo -H pip install virtualenv
$ sudo -H pip install pywinrm[kerberos]
$ sudo apt install krb5-user
$ sudo pip install pywinrm
$ sudo pip install ansible
```

### Configuring Ubuntu for Kerberos Authentication with Active Directory

## Kerberos Server Guide

* [Kerberos Server Guide](https://help.ubuntu.com/lts/serverguide/kerberos.html)

## Ansible Vault

```bash
$ ansible-vault create group_vars/aviservers/aviservers.yml
$ ansible-vault encrypt group_vars/aviservers/aviservers.yml
```

### Edit Encypted File

```bash
$ ansible-vault edit group_vars/aviservers/aviservers.yml
```

### View Encypted File

```bash
$ ansible-vault view group_vars/aviservers/aviservers.yml
```

### Decrypt File

```bash
$ ansible-vault decrypt group_vars/aviservers/aviservers.yml
```

### Run the playbooks with ecryption

* Update vault_password_file

```bash
$ touch /secret/vault
$ ansible-vault edit group_vars/aviservers/aviservers.yml
$ ansible-playbook playbooks/config/ping.yml
```

```bash
$ ansible-playbook playbooks/config/ping.yml --vault-password-file /etc/ansible/secret/vault
$ ansible-playbook playbooks/config/ping.yml --ask-vault-pass 
```

### Setting the Ansible Vault Editor

```bash
$ export EDITOR=vi
```

## Ansible Galaxy

```bash
$ cd playbooks/roles/
$ ansible-galaxy init iis --offline
```
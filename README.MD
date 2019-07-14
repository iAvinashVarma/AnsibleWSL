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
$ ansible aviservers -i inventory -m win_ping
```

* If ansible.cfg is configured in non NTFS file system, no need of adding inventort parameter, can run the below command.

```bash
$ ansible aviservers -m win_ping
```

### Get System Information

```bash
$ ansible aviservers -i inventory -m setup
```

### Execute plain commands

```bash
$ ansible aviservers -i inventory -m raw -a "dir"
$ ansible aviservers -i inventory -m raw -a "ipconfig" 
```

### Execute service related commands

```bash
$ ansible aviservers -i inventory -m win_service -a name="spooler"
$ ansible aviservers -i inventory -m win_service -a "name=spooler state=stopped"
$ ansible aviservers -i inventory -m win_service -a "name=spooler state=started"
```

### Execute feature related commands

```bash
$ ansible aviservers -i inventory -m win_feature -a "name=Telnet-Client state=present"
```

## Run Playbooks

```bash
$ ansible-playbook -i inventory playbooks/local/test.yml
$ ansible-playbook -i inventory playbooks/config/ipconfig.yml
$ ansible-playbook -i inventory playbooks/config/stats.yml
$ ansible-playbook -i inventory playbooks/config/ping.yml
$ ansible-playbook -i inventory playbooks/scripts/powershell.yml
$ ansible-playbook -i inventory playbooks/user/create.yml
```
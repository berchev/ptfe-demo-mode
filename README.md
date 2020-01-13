# ptfe-demo-mode
This repo is guideline on:
- TFE version 4 demo install
- TFE version 4 restore from snapshot

There are two types of installation (Online or Airgapped).
We will focus on online installation.


We are going to use vagrant in order to create appropriate development environment for that.
Our VM configuration include:
- private IP address (192.168.56.33) 
- /dev/mapper/vagrant--vg-root 83GB
- /dev/mapper/vagrant--vg-var_lib 113G


Into the whole picture we will include http://xip.io/ which provides wildcard DNS for any IP address. 
Our LAN IP address is 192.168.56.33. Using xip.io, 192.168.56.33.xip.io resolves to 192.168.56.33

## Repo Content
| File                   | Description                      |
|         ---            |                ---               |
| [Vagrantfile](Vagrantfile) | Vagrant template file. TFE env is going to be cretated based on that file|
| [delete_all.sh](delete_all.sh) | Purpose of this script is to break our environment. We will use it during snapshot restore|

## Requirements
Please make sure you have fullfilled the reqirements before continue further:
- [Virtualbox](https://www.virtualbox.org/wiki/Downloads) installed
- Hashicorp [Vagrant](https://www.vagrantup.com/) installed
- [Basic Vagrant skills](https://www.vagrantup.com/intro/getting-started/) 
- PTFE license (You can contact Sales Team of HashiCorp - sales@hashicorp.com in order to purchase one)

## Getting started
- Clone this repo locally
```
git clone https://github.com/berchev/ptfe-demo-mode.git
```
- Change into downloaded repo directory
```
cd ptfe-demo-mode
```

## PTFE version 4 demo install
- Start provision vagrant development environment
```
vagrant up
```
- connect to newly provisioned Vagrant machine
```
vagrant ssh
```
- Become `root`
```
sudo su -
```
- change to /vagrant directory (This is shared directory between the Host and the VM. It is better to keep the important stuff there.)
```
cd /vagrant
```
- Run the TFE installer from the shell of your instance, in order to start interractive installation
```
curl https://install.terraform.io/ptfe/stable | sudo bash
```
- Choose interface for TFE
```bash
vagrant@ptfe:/vagrant# curl https://install.terraform.io/ptfe/stable | sudo bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  134k  100  134k    0     0  46190      0  0:00:02  0:00:02 --:--:-- 46175
Determining local address
The installer was unable to automatically detect the private IP address of this machine.
Please choose one of the following network interfaces:
[0] enp0s3	10.0.2.15
[1] enp0s8	192.168.56.33
Enter desired number (0-1): 1
```
- Just hit enter on this step
```bash
The installer will use network interface 'enp0s8' (with IP address '192.168.56.33').
Determining service address
The installer was unable to automatically detect the service IP address of this machine.
Please enter the address or leave blank for unspecified.
Service IP address: 
```
- Type `N` or hit enter, if you do not use proxy
```
Does this machine require a proxy to access the Internet? (y/N) 
Installing docker version 18.09.2 from https://get.replicated.com/docker-install.sh
# Executing docker install script, commit: UNKNOWN
+ sh -c apt-get update -qq >/dev/null
+ sh -c apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
```
- Script will continue, installing Docker from Replicated site and pulling latest stable Replicated UI image
- Once script finish successfully, you will see following:
```bash
Created symlink /etc/systemd/system/docker.service.wants/replicated-operator.service → /etc/systemd/system/replicated-operator.service.

Operator installation successful

To continue the installation, visit the following URL in your browser:

  http://<this_server_address>:8800

vagrant@ptfe:/vagrant#
```

- Open your browser and type: `https://192.168.56.33:8800`, in oder to continue with the installation on UI
- Click on `Advanced`, since we are using self-signed certificate
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/5.png)

- Then click on `Proceed to 192.168.56.33 (unsafe)`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/6.png)

- On the next screen add the FQDN: `192.168.56.33.xip.io` and click on `Use Self-Signed Sert`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/7.png)

- Again `Proceed to 192.168.56.33 (unsafe)`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/8.png)

- Here you need to attach the TFE Licence purchased from HashiCorp
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/9.png)

- Once you provided licence, you need to choose between two types of installation (Online or Airgapped). We will proceed with `Online`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/10.png)

- Type your password, and click on `continue`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/11.png)

- After all the checks are passed, click on `Continue`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/12.png)

- On the next screen type the password you would like to use for data encryption, and choose `Demo` for installation type and click `Save` on the bottom of the page
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/13.png)

- Then click on `take me to the dashboard`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/14.png)

- Wait for the process to finish
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/15.png)

- If you see that on screen, you are ready!
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/16.png)


##  TFE version 4 restore from snapshot
Once we have installed TFE, we would like to have a snapshot of the last stable state. That's why we will create snapshot.
- In your browser type: `https://192.168.56.33.xip.io:8800/dashboard`
- Click on `Start Snapshot` and wait the process to complete
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/17.png)

- In order to verify that everything works as expected, we will break our system and then will restore it from snapshot
- Go to Terminal shell, become root
```
sudo su -
```
- Run `delete_all.sh` script located on `/vagrant` directory 
```
bash delete_all.sh 
```
- Exit from VM (most probbably you will need to type `exit` twice)
```
exit
```
- Restart your VM 
```
vagrant reload
```
- ssh to your VM again
```
vagrant ssh
```
- become root
```
sudo su -
```
- change to /vagrant directory
```
cd /vagrant
```
- Install replicated 
```
curl https://install.terraform.io/ptfe/stable | sudo bash
```
- When the installer asks for private IP of the machine, choose `1`
```
vagrant@ptfe:/vagrant# curl https://install.terraform.io/ptfe/stable | sudo bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  134k  100  134k    0     0  46190      0  0:00:02  0:00:02 --:--:-- 46175
Determining local address
The installer was unable to automatically detect the private IP address of this machine.
Please choose one of the following network interfaces:
[0] enp0s3	10.0.2.15
[1] enp0s8	192.168.56.33
[2] docker0 172.17.0.1
Enter desired number (0-2): 1
```

- Hit `enter` on the next question
```
The installer will use network interface 'enp0s8' (with IP address '192.168.56.33').
Determining service address
The installer was unable to automatically detect the service IP address of this machine.
Please enter the address or leave blank for unspecified.
Service IP address: 
```

- Choose `N` or hit `enter` about proxy option and wait the process to complete
```
Does this machine require a proxy to access the Internet? (y/N) N
Installing docker version 18.09.2 from https://get.replicated.com/docker-install.sh
# Executing docker install script, commit: UNKNOWN
+ sh -c apt-get update -qq >/dev/null
+ sh -c apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
```

- After Replicated is installed successfully, you will see these line on the bottom of the screen:
```
Operator installation successful

To continue the installation, visit the following URL in your browser:

  http://<this_server_address>:8800

vagrant@ptfe:/vagrant#
```

- Open your browser and type: `https://192.168.56.33:8800`, in oder to continue on UI
- Click on `Advanced`, in order to accept the self-signed sertificate
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/5.png)

- On the next screen add the FQDN: `192.168.56.33.xip.io` and click on `Use Self-Signed Sert`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/7.png)

- Click on `Advanced` and `Proceed to 192.168.56.33 (unsafe)`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/8.png)

- On the next screen choose `Restore from a snapshot`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/22.png)

- Click on `Browse snapshots` button 
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/23.png)

- Next hit the orange button called `Restore`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/24.png)

- Type your password on the next screen and click `Unlock`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/25.png)

- Wait for all checks to pass and click `Continue`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/26.png)

- Click `Restore`
![](https://github.com/berchev/ptfe-demo-mode/blob/master/screenshots/27.png)

## TODO

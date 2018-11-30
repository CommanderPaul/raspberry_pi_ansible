# Kubernetes on Raspberry Pi via Ansible
A process using Ansible to install Kubernetes on a Raspberry Pi 3 B+.
Some initial configuration is needed on the Raspberry Pi itself, and the script does not initialize the cluster, but may in the future.
## Getting Started
### Local Machine Prerequisites
Ansible 2.7.2 was used for this project.  Other versions may work.
```
ansible --version
```
You will need a suitable ssh key to ssh into the Raspberry Pi's.
### Raspberry Pi Preperation
Raspberry Pi 3 B+ model is used with a wi-fi connection.
* Burn **2018-11-13-raspbian-stretch-lite.img** to an sd card.  I used Etcher but other options exist.
* Boot from sd card and set the wifi and ssh server
  ```
  sudo raspi-config
  ```
	- Option 2, configure network settings, then option N2 for wi-fi.
	- Option 5, interfacing options, then P2, set SSH server to on.

* Make note of the wlan0 ip address
	```
  ip addr
  ```
### Ansible Script Configuration
After cloning the repo you will need to modify the following files:

#### Inventory
**inventory/inventory_file**

Change the ip address or addresses to those on your network.  Only one Raspberry Pi is required.  Adjust the inventory to reflect the number being used.
```
[raspberries]
192.168.1.248    new_hostname=kube-master
192.168.1.77     new_hostname=kube1
192.168.1.113    new_hostname=kube2
192.168.1.162    new_hostname=kube3
```
#### Username and SSH Key
**secure_pi.yml**

In my script I encrypted my username and ssh key location using Ansible Vault.  You don't need to do that, you can just past in your username and path to your key.

```
vars:
  username: < your ssh key username >

  user_key: "{{ lookup('file', '<path to your ssh key') }}"
```
### remote_user and username
**configure_pi.yml**

In the script, remote_user and username are encrypted.  Replace the items with your ssh key username.
```
remote_user: <ssh key username>
gather_facts: no
become: yes
vars:
  username: <ssh key username>
```
## Deployment
### Secure the Raspberry Pi's
Run the Ansible script **secure_pi.yml**.
```
ansible-play secure_pi.yml
```
The play logs in as the default user and creates an admin user, uploads the key, and turns off ssh via password.  When the ssh daemon restarts, the default user will no longer be able to ssh into the Raspberry Pi's, but local access will still work.
### Configure Raspberry Pi's
Run the Ansible script **configure_pi.yml**.
```
ansible-play configure_pi.yml
```
The play ssh's in as the admin user, installs Docker and Kubernetes for arm, and configures Raspbian Stretch operating system to run Kubernetes.

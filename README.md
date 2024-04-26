# RHCSA 9 Vagrant
# Updated on 2024-04-24
# RHCSA 9 Lab - Vagrant Practice lab using Rocky 9.

Vagrant lab for practicing for the rhcsa 9. 

# How to use this lab
- install virtualbox
- install vagrant
- Open a PowerShell Terminal Window
  ```
  mkdir ~/vagrant
  ```
  ```
  cd ~/vagrant
  ```
- Download this repo to the "vagrant" directory
- ```
  unzip rhcsa9vagrant.zip
  ```
- run ```vagrant up``` inside this directory and it will automatically build off of the Vagrantfile:
    
The vagrant script will set up 3 Rocky 9 VMs: ansible, node1, and node2. 

In three separate terminals, you want to execute the following commands to ssh into the boxes:

```
vagrant ssh ansible
```

```
vagrant ssh node1
```

```
vagrant ssh node2
```

From there, you can change the settings in /etc/ssh/sshd_config, change root password, and so forth.

TO STOP ALL RUNNING VMs, RUN
```
vagrant halt
```
TO DELETE (BLOW AWAY) ALL VMs, RUN
```
vagrant destroy
```

TODO
- This has only been tested on Windows 11. This should also work fine with Linux distros as well...but you will need to use the commands in the Vagrantfile for libvirt instead...I will soon be uploading that version, so stay tuned!


Special Thanks to: https://github.com/gitmpr for providing the original Vagrantfile.  I've optimized it to most up-to-date version of Rocky 9 for use with Virtualbox and Windows 10/11.

Please refer to the official Vagrant documentation here: https://developer.hashicorp.com/vagrant/tutorials/getting-started/getting-started-boxes

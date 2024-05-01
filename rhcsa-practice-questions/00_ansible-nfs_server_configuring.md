***On Ansible-NFS Server***

# Configure the NFS Server Share Directory (This will help you for Lab #7's autofs question):

* First, install the required packages:
```
[root@ansible ~]# yum install -y nfs* rpc*
```

* Start and Enable the service:
```
[root@ansible ~]# systemctl enable --now nfs-server
[root@ansible ~]# systemctl status nfs-server
```

* Setup the shared directory: 
```
[root@ansible ~]# mkdir /shared
[root@ansible ~]# chmod 777 /shared
```

* Configure the ```/etc/exports`` file:
```
[root@ansible ~]# vim /etc/exports

/shared 10.10.10.93(rw,sync,no_root_squash)


:wq
```

* Export the nfs-shared directory:
```
[root@ansible ~]# exportfs -avr
```

* Configure the firewall:
```
[root@ansible ~]# firewall-cmd --add-service={nfs,mountd,rpc-bind} --permanent
[root@ansible ~]# firewall-cmd --reload
```

* Create netuserx for Lab #7:
```
[root@ansible ~]# groupadd -g 1234 autofsusers
[root@ansible ~]# useradd -d /shared/userautofs01 -u 1234 -g autofsusers userautofs01 
[root@ansible ~]# passwd userautofs01
```
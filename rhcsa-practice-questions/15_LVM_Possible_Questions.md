***On Node2***

# Logical Volume Management

### QUESTION #15.1:
Create a Logical Volume named LV1 of size 8GB using the extra storage provided.  Here is what you will learn:
![image](https://github.com/RedHatRanger/rhcsa9vagrant/assets/90477448/47206a5d-73f3-483e-b9d6-e3f35aea26ab)

DexTutor's tutorial can be found <a href="https://www.youtube.com/watch?v=N3HFDvV-d-w">here</a>
***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### ANSWER #15.1:

* First, if you are running virtual machines, you will need to add an additional storage device here...6GB,5GB, and 4GB "Hard Disks"
* You can shut down the virtual machine and then add the additional storage as needed.

* Let's start by running ```lsblk``` to see what physically attached devices we have.

```
[root@node2 ~]# lsblk
NAME           MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sro              11:0    1 1024M  0 rom
vda            252:0    0   10G  0 disk
├─vda1         252:1    0    1G  0 part /boot
├─vda2         252:2    0    9G  0 part
│ ├─_rhe1_192-root 253:0 0  8G  0 lvm  /
│ └─_rhe1_192-swap 253:1 0  1G  0 lvm  [SWAP]
vdb            252:16   0    6G  0 disk
vdc            252:17   0    5G  0 disk
vdd            252:18   0    4G  0 disk
[root@node2 ~]#
```

* The first step is to create the **physical volumes**: 
```
[root@node2 ~]# pvcreate /dev/vdb
  Physical volume "/dev/vdb1" successfully created.
[root@node2 ~]# pvcreate /dev/vdc
  Physical volume "/dev/vdc" successfully created.
[root@node2 ~]# pvcreate /dev/vdd
  Physical volume "/dev/vdd" successfully created.
```

* We can verify that they are available:
```
[root@node2 ~]# pvs
PU           VG      Fmt    Attr    PSize   PFree
/dev/sdb             1um2   ---     6.00g   6.00g
/dev/sdc             1vm2   ---     5.00g   5.00g
/dev/sdd             1um2   ---     4.00g   4.00g
```

* Next, we create the Volume Group ```LV1``` using two of our extra disks (11GB) because the 1st one was too small for 8GB:
```
[root@node2 ~]# vgcreate VG1 /dev/sdb /dev/sdc
  Volume group "VG1" successfully created
[root@node2 ~]# vgs
VG   #PU  #LV #SN Attr     USize   UFree
VG1  2    0   0   wz--n-   10.99g  10.99g
```

* Now, we create the Logical Volume:
```
[root@node2 ~]# lvcreate -L 8Gb -n LV1 VG1
  Logical volume "LV1" created.
```

* We can verify that the Logical Volume has been created:
```
[root@node2 ~]# lvs
LV     VG     Attr      LSize  Pool Origin Data Metax Move Log Cy Sync Convert
LV1    VG1    wi-a--    8.00g
```

[root@node2 ~]# mkfs.ext4 /dev/wgroup/wshare
mke2fs 1.46.5 (30-Dec-2021)
Discarding device blocks: done                            
Creating filesystem with 409600 1k blocks and 102400 inodes
Filesystem UUID: dc2bedb3-e528-4c92-8ea3-a3c0619d769f
Superblock backups stored on blocks: 
  8193, 24577, 40961, 57345, 73729, 204801, 221185, 401409
Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done   
```
* Next, we will create an auto mount point in /etc/fstab:
```
[root@node2 ~]# vim /etc/fstab

# /etc/fstab
# Created by anaconda on Fri Dec 30 16:18:12 2022
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rhel_192-root /                       xfs     defaults        0 0
UUID=0efecb9e-7ecd-47e0-ad64-507f7c994e18 /boot  xfs     defaults        0 0
/dev/mapper/rhel_192-swap none                    swap    defaults        0 0
/dev/wgroup/wshare       /mnt/wshare              ext4    defaults        0 0


:wq
```
* Run ```mount -a``` to mount the /etc/fstab mounts:
```
[root@node2 ~]# mount -a
[root@node2 ~]# df -h
Filesystem                      Size  Used Avail Use% Mounted on
devtmpfs                        867M    0  867M   0% /dev
tmpfs                           007M    0  007M   0% /dev/shm
tmpfs                           355M  5.1M  350M   2% /run
/dev/mapper/rhel_192-root       8.0G  1.6G  6.5G  20% /
/dev/vda1                       1014M  220M  795M  22% /boot
tmpfs                           178M    0  178M   0% /run/user/0
/dev/mapper/wgroup-wshare       365M   14K  341M   1% /mnt/wshare
```

* SUCCESS!!
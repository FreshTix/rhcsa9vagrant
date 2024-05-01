***On Node1***
# Configure autofs on Node1

### QUESTION #7
Configure autofs to automount the home directories of remoteuserx user. Note the following: 
   - remoteuserx directory is exported via NFS, which is available on classroom.example.com (192.168.13.162) and your NFS-exports directory is /ourhome/remoteuserx for remoteuserx, 
   - remoteuserx's home directory should be automounted using autofs service. 
   - home directories must be writable by their users. 
 
***
(scroll down for an answer)

<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### ANSWER #7:

* First, you need to install ```autofs```:

```
[root@node1 ~]# yum install -y autofs
[root@node1 ~]# systemctl enable --now autofs
[root@node1 ~]# vim /etc/auto.master.d/aa.autofs

/-      /etc/auto.misc


:wq
```

* Next edit the /etc/auto.misc file:
```
[root@node1 ~]# vim /etc/auto.misc

#
# This is an automounter map and it has the following format
# key [ -mount-options-separated-by-comma ] location
# Details may be found in the autofs(5) manpage

cd                          -fstype=iso9660,ro,nosuid,nodev          :/dev/cdrom

# the following entries are samples to pique your imagination
#linux                      -ro,soft                                  ftp.example.org:/pub/linux
#boot                       -fstype=ext2                              :/dev/hda1
#floppy                     -fstype=auto                              :/dev/fd0
#1floppy                    -fstype=ext2                              :/dev/fd0
#e2floppy                   -fstype=ext2                              :/dev/fd0
#jaz                        -fstype=ext2                              :/dev/sdc1
#removable                  -fstype=ext2                              :/dev/hdd
/ourhome/remotueserx        -rw,soft,intr                             192.168.13.162:/ourhome/remotueserx


:wq
```

* Restart the ```autofs``` service and mount all partitions:
```
[root@node1 ~]# systemctl restart autofs
[root@node1 ~]# mount -a
```

* Let's test it out, but before we do, notice that the partition is NOT YET MOUNTED:
```
[root@node1 ~]# cd /ourhome
[root@node1 ourhome]# df -h
Filesystem            Size    Used    Avail   Use%    Mounted on
devtmpfs              846M    0       846M    0%      /dev
tmpfs                 875M    0       875M    0%      /dev/shm
tmpfs                 350M    9.6M    341M    3%      /run
/dev/sda2             5.6G    3.7G    2.0G    66%     /usr
/dev/mapper/myvg-home 9.5G    6.0M    8.9G    7%      /lvm1
/dev/sr0              8.0G    8.0G    0       100%    /dvd
/dev/sda7             947M    39M     908M    5%      /tmp
/dev/sda6             947M    44M     904M    5%      /home
/dev/sda1             947M    256M    692M    27%     /boot
/dev/sda5             1.9G    202M    1.7G    11%     /var
tmpfs                 175M    92K     175M    1%      /run/user/0
``` 

* Next, let's run ```ls``` and ```df -h``` within the ```/ourhome``` directory:
```
[root@node1 ourhome]# ls
[root@192 ourhome]# ls
remotueserx
[root@192 ourhome]# df -h
Filesystem            Size    Used    Avail   Use%    Mounted on
devtmpfs              846M    0       846M    0%      /dev
tmpfs                 875M    0       875M    0%      /dev/shm
tmpfs                 350M    9.6M    341M    3%      /run
/dev/sda3             1.9G    78M     1.8G    5%      /
/dev/sda2             5.6G    3.7G    2.0G    66%     /usr
/dev/mapper/myvg-home 9.5G    6.0M    8.9G    7%      /lvm1
/dev/sr0              8.0G    8.0G    0       100%    /dvd
/dev/sda7             947M    39M     908M    5%      /tmp
/dev/sda6             947M    44M     904M    5%      /home
/dev/sda1             947M    256M    692M    27%     /boot
/dev/sda5             1.9G    202M    1.7G    11%     /var
tmpfs                 175M    92K     175M    1%      /run/user/0
192.168.13.162:/ourhome/remotueserx  1.9G    179M    1.7G    10%     /ourhome/remotueserx
```


SUCCESS!!
   
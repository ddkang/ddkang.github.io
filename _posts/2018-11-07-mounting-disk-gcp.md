---
layout: post
title: Mounting a Blank Disk on Google Compute Engine
comments: true
author: Daniel Kang
---

Change `$MNT_DIR_NAME` as desired.
```
export MNT_DIR_NAME="data"
sudo mkdir -p /mnt/disks/$MNT_DIR_NAME
sudo mkfs.ext4 -m 0 -F -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdb
sudo mount -o discard,defaults /dev/sdb /mnt/disks/$MNT_DIR_NAME
sudo chmod a+w /mnt/disks/$MNT_DIR_NAME
```

This will automatically mount the disk after reboot, but will not work if you switch disks:
```
sudo cp /etc/fstab /etc/fstab.backup
echo UUID=`sudo blkid -s UUID -o value /dev/sdb` \
  /mnt/disks/$MNT_DIR_NAME ext4 discard,defaults,nofail 0 2 | sudo tee -a /etc/fstab
```

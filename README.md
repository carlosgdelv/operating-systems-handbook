# operating-systems-handbook


___


## 🪟 WSL installation 

Install Windows Subsystem for Linux (WSL) on this machine.
```bash
wsl --install
```
Restart the computer so WSL installation and kernel updates take effect.
```bash
(Restart your PC)
```
List Linux distributions available to install from the Microsoft catalog.
```bash
wsl --list --online
```
Install Ubuntu 24.04 into WSL.
```bash
wsl --install -d Ubuntu-24.04
```
Open an interactive shell inside the Ubuntu 24.04 WSL distribution.
```bash
wsl -d Ubuntu-24.04
```
or
```bash
wsl
```

___

## 🐧 Linux Live Installation

Step 1: Plug in the USB and detect its path
```bash
lsblk -fp
```

⚠️ Step 2: Unmount the USB (if mounted)
```bash
sudo umount /dev/sda1
sudo umount /dev/sda2
```

💣 Step 3: Erase the USB partition table (only the USB, not your SSD)
```bash
sudo wipefs -a /dev/sda
```

💿 Step 4: Download a Linux ISO (for example, Ubuntu 24.04)
```bash
wget https://releases.ubuntu.com/24.04/ubuntu-24.04-desktop-amd64.iso -O ubuntu.iso
```

🚀 Step 5: Write the ISO to the USB
```bash
sudo dd if=ubuntu.iso of=/dev/sda bs=4M status=progress oflag=sync
```

✅ Step 6: Sync and eject

```bash
sync
sudo eject /dev/sda
```
Now you can safely remove the USB.


💡 Final check
If you run lsblk -fp again, you should see something like:
```bash
/dev/sda iso9660 Ubuntu 24.04 amd64
```

___


## 🔧 Extending Disk Space on a Linux VM (LVM + SSD)

1. Update system and install LVM tools
Install the LVM2 utilities required to manage logical volumes.
```bash
sudo apt update
sudo apt install lvm2 -y
```

2. Wipe all data and prepare the new disk
Remove all existing filesystem/partition data and initialize the disk as a physical volume.
```bash
sudo wipefs --all /dev/nvme1n1
sudo sgdisk --zap-all /dev/nvme1n1
sudo pvcreate /dev/nvme1n1
```

3. Add the new disk to the existing volume group
Extend your volume group (ubuntu-vg) to include the new disk.
```bash
sudo vgextend ubuntu-vg /dev/nvme1n1
```

4. Confirm volume group changes
Check that the volume group now contains the new physical volume.
```bash
sudo vgs
```

5. Extend the logical volume to use all free space
Grow the logical volume (ubuntu-lv) with all available space from the volume group.
```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```
6. Resize the filesystem
Expand the filesystem to match the new size of the logical volume.
```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```
7. Verify the changes
Check the updated disk space and filesystem type.
```bash
df -h /
df -T /
```
8. Optional: View detailed LV info
Display full details of the extended logical volume.
```bash
sudo lvdisplay /dev/ubuntu-vg/ubuntu-lv
```

___

## 🔍 Monitor System

Monitor your hardware usage:
```bash
htop
```

Monitor your Disk usage:
```bash
df -h
```

Monitor Geth Disk usage:
```bash
docker exec -it geth du -sh /data
```

Monitor Prysm Disk usage:
```bash
docker exec -it prysm du -sh /data
```
Monitor Aztec Disk usage:
```bash
docker exec -it nodeaztec-node-1 du -sh /data/*
```
___

## 🐳 Simple Docker Guide

Shows all currently running Docker containers.
```bash
docker ps
```

Lists all containers, including those that are stopped.
```bash
docker ps -a
```
It shows real-time metrics of running containers, including CPU, memory, network, and disk usage.
```bash
docker stats 
```

Stops the specified running container gracefully.
```bash
docker stop <container_id_or_name>
```

Deletes the specified container permanently.
```bash
docker rm <container_id_or_name>
```

Removes all stopped containers in one go to free up space.
```bash
docker container prune
```

Uninstalls Docker packages and deletes Docker data directories to fully clean your system.
```bash
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

___


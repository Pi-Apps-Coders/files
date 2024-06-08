# This folder contains the commands required to compile the different applications stored in this repository.

theofficialgman suggests getting an always free Oracle ARM64 build server and putting the Ubuntu Bionic image on it https://www.oracle.com/cloud/free/ (4 cores, up to 24GB or ram, 200GB of storage).

for building packages for ARMhf, theofficialgman suggests building packages within a ubuntu bionic schroot (to provide maximum compatibility with supported pi-apps systems). there are some steps below (may be missing some parts).
```bash
sudo debootstrap --components=main,restricted,multiverse,universe --include=nano,sudo,git,wget,cmake,meson,python3,build-essential,libglx-mesa0,libegl-mesa0,libgles2-mesa,libgl1-mesa-glx,libegl1-mesa,nginx,libgtk2.0-0,libdbus-glib-1-2,libsdl2-2.0-0,libusb-1.0-0,xterm,ca-certificates --arch armhf --foreign bionic /srv/chroot/bionic-armhf http://ports.ubuntu.com
sudo chroot /srv/chroot/bionic-armhf /debootstrap/debootstrap --second-stage
```
prevent home directory from mounting in schroot by editing `sudo nano /etc/schroot/desktop/fstab` and comment out /home. also, make sure the following lines are present
```
/run           /run            none    rw,bind         0       0
/run/lock      /run/lock       none    rw,bind         0       0
/dev/shm       /dev/shm        none    rw,bind         0       0
/run/shm       /run/shm        none    rw,bind         0       0
```

create the schroot config file by editing `sudo nano /etc/schroot/chroot.d/bionic-armhf.conf` making sure to edit <username> to your local user
```
[bionic-armhf]
description=Bionic armhf chroot
aliases=bionic-armhf
type=directory
directory=/srv/chroot/bionic-armhf
profile=desktop
personality=linux
preserve-environment=true
root-users=<username>
users=<username>
```

you can now enter the chroot with 
`sudo schroot -c bionic-armhf`. you probably want to add your same user to the chroot by executing inside the chroot:
```bash
echo "<username> ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers
useradd -u 1000 --shell /bin/bash -rmUG wheel,video,audio,render <username>
su - <username>
```

Future apps/binaries should be uploaded to the large-files github release. You can do so via the web interface or with the github cli program
```bash
gh release upload large-files path/to/file
```
If you have releases that were generated as part of your automation but not directly used by pi-apps (eg: rpm or appimages for a release while pi-apps uses the deb), those can be uploaded as well (to benefit the non debian community).

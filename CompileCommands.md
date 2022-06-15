# This files contains the commands required to compile the different applications stored in this repository.

## OBS

#### Compiling with Chromium Embedded Framework

To compile with CEF support (and to allow browser sources and dock support), use the following instructions from https://github.com/Botspot/pi-apps/issues/1698.

1. download the minimal CEF binary from here https://cef-builds.spotifycdn.com/index.html#linuxarm64 for version 4638 which matches what OBS uses (click show all builds -> show more builds). You can download the minimal or full, either one is fine.
2. extract that tar.bz2 to a folder and move to that folder
3. create a `build` directory in that folder and move to that folder
4. execute `cmake .. -DPROJECT_ARCH="arm64"` this is to generate the libcef wrapper
4. `make -j$(nproc)`

now you have built CEF wrapper (chromium embedded framework wrapper), continue to follow the instructions below.

then go to however you normally build obs studio but enable the browser source and point to the CEF folder directory
so for example:
```
-DBUILD_BROWSER=ON -DCEF_ROOT_DIR='/home/user/obs-studio-updates/CEF/cef_binary_95.7.18+g0d6005e+chromium-95.0.4638.69_linuxarm64_minimal'
```

#### Installing Dependencies

```bash
sudo apt install build-essential checkinstall cmake git libmbedtls-dev libasound2-dev libavcodec-dev libavdevice-dev libavfilter-dev libavformat-dev libavutil-dev libcurl4-openssl-dev libfontconfig1-dev libfreetype6-dev libgl1-mesa-dev libjack-jackd2-dev libjansson-dev libluajit-5.1-dev libpulse-dev libqt5x11extras5-dev libspeexdsp-dev libswresample-dev libswscale-dev libudev-dev libv4l-dev libvlc-dev libx11-dev libx11-xcb1 libx11-xcb-dev libxcb-xinput0 libxcb-xinput-dev libxcb-randr0 libxcb-randr0-dev libxcb-xfixes0 libxcb-xfixes0-dev libx264-dev libxcb-shm0-dev libxcb-xinerama0-dev libxcomposite-dev libxinerama-dev pkg-config python3-dev qtbase5-dev libqt5svg5-dev swig libwayland-dev qtbase5-private-dev libpci-dev
wget http://ftp.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac2_2.0.1-1_armhf.deb
wget http://ftp.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac-dev_2.0.1-1_armhf.deb
sudo dpkg -i libfdk-aac2_2.0.1-1_armhf.deb
sudo dpkg -i libfdk-aac-dev_2.0.1-1_armhf.deb
```

#### Compiling OBS from source

```bash
cd
git clone --recursive https://github.com/obsproject/obs-studio.git
cd obs-studio
mkdir build
cd build
cmake -DUNIX_STRUCTURE=1 -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_PIPEWIRE=OFF -DBUILD_BROWSER=OFF ..
make -j$(nproc)
cmake --build $HOME/obs-studio/build -t package
```
------

## VMware Horizon Client

#### Download tarball and extract 

```bash
wget https://download3.vmware.com/software/view/viewclients/CART22FH2/VMware-Horizon-Client-Linux-2111-8.4.0-18957622.tar.gz
tar -xf VMware-Horizon-Client-Linux-2111-8.4.0-18957622.tar.gz
rm VMware-Horizon-Client-Linux-2111-8.4.0-18957622.tar.gz
cd VMware-Horizon-Client-Linux-2111-8.4.0-18957622/armhf
mkdir debian
for file in $(ls | grep '.tar.gz$'); do
   tar -xf $file
done
rm *tar.gz
```

#### Copy and link files

```bash
mkdir -p debian/DEBIAN debian/usr/share debian/etc/init.d debian/etc/rc5.d debian/etc/rc6.d debian/usr/include
cp -a VMware-Horizon-Client-2111-8.4.0-18957622.armhf/bin debian/usr/bin
cp -a VMware-Horizon-Client-2111-8.4.0-18957622.armhf/lib debian/usr/lib
cp -a VMware-Horizon-Client-2111-8.4.0-18957622.armhf/doc debian/usr/share/doc
cp -a VMware-Horizon-Client-2111-8.4.0-18957622.armhf/share/* debian/usr/share/
cp -a VMware-Horizon-PCoIP-2111-8.4.0-18957622.armhf/lib/* debian/usr/lib/
cp -a VMware-Horizon-USB-2111-8.4.0-18957622.armhf/bin/* debian/usr/bin/
cp -a VMware-Horizon-USB-2111-8.4.0-18957622.armhf/lib/* debian/usr/lib/
cp -a VMware-Horizon-USB-2111-8.4.0-18957622.armhf/init.d/* debian/etc/init.d/
sudo ln -sf $(pwd)/debian/etc/init.d/vmware-USBArbitrator $(pwd)/debian/etc/rc5.d/S50vmware-USBArbitrator
sudo ln -sf $(pwd)/debian/etc/init.d/vmware-USBArbitrator $(pwd)/debian/etc/rc6.d/K08vmware-USBArbitrator
sudo cp -a VMware-Horizon-USB-sdk-2111-8.4.0-18957622.armhf/armhf/include/* debian/usr/include/
sudo cp -a VMware-Horizon-USB-sdk-2111-8.4.0-18957622.armhf/armhf/lib/* debian/usr/lib/
sudo cp -a VMware-Horizon-Client-Linux-ClientSDK-2111-8.4.0-18957622.armhf/lib/* debian/usr/lib/
sudo cp -a VMware-Horizon-Client-Linux-ClientSDK-2111-8.4.0-18957622.armhf/include/* debian/usr/include/
```

#### Create DEB package

```bash
echo "Package: vmware-horizon-client
Name: VMware Horizon Client
Architecture: armhf
Description: VMware Horizon Client  allows your end users to connect to their VM from a device of choice, including Windows, macOS, iOS, Linux, Chrome, and Android.
Depends: libudev1
Author: cycool29 <cycool29@gmail.com>
Maintainer: cycool29 <cycool29@gmail.com>
Version: 1.0" > debian/DEBIAN/control

echo '#!/bin/bash

sudo cp -a "$(dpkg -S 'libudev.so.1' | grep "libudev.so.1$" | sed '"'s/.*: //g'"')" /usr/lib/libudev.so.0' > debian/DEBIAN/postinst

chmod +x debian/DEBIAN/postinst

dpkg-deb --build ./debian vmware-horizon-client_1.0_armhf.deb
```

------

## Timeshift

```
# depends to build deb packages
sudo apt install -y equivs devscripts
git clone https://github.com/teejee2008/timeshift.git
cd timeshift
# get and install build dependencies as an apt package timeshift-build-deps
sudo mk-build-deps -i
# build the deb
debuild -b -uc -us
# uninstall build deps
sudo apt-get purge --auto-remove timeshift-build-deps
# replace this with the version you built
sudo apt install ../timeshift_22.06.1_arm64.deb
```

------

## Github Desktop

https://github.com/Botspot/pi-apps/pull/1775#issuecomment-1119159519

(waiting on PR to merge for simpler instructions: https://github.com/desktop/dugite-native/pull/368 )

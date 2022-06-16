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
# debian buster 32
wget http://ftp.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac2_2.0.1-1_armhf.deb
wget http://ftp.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac-dev_2.0.1-1_armhf.deb
sudo apt install build-essential cmake git libmbedtls-dev libasound2-dev libavcodec-dev libavdevice-dev libavfilter-dev libavformat-dev libavutil-dev libcurl4-openssl-dev libfontconfig1-dev libfreetype6-dev libgl1-mesa-dev libjack-jackd2-dev libjansson-dev libluajit-5.1-dev libpulse-dev libqt5x11extras5-dev libspeexdsp-dev libswresample-dev libswscale-dev libudev-dev libv4l-dev libvlc-dev libx11-dev libx11-xcb1 libx11-xcb-dev libxcb-xinput0 libxcb-xinput-dev libxcb-randr0 libxcb-randr0-dev libxcb-xfixes0 libxcb-xfixes0-dev libx264-dev libxcb-shm0-dev libxcb-xinerama0-dev libxcomposite-dev libxinerama-dev pkg-config python3-dev qtbase5-dev libqt5svg5-dev swig libwayland-dev qtbase5-private-dev libpci-dev ./libfdk-aac-dev_2.0.1-1_armhf.deb ./libfdk-aac2_2.0.1-1_armhf.deb

# debian buster 64
wget http://ftp.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac2_2.0.1-1_arm64.deb
wget http://ftp.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac-dev_2.0.1-1_arm64.deb
sudo apt install build-essential cmake git libmbedtls-dev libasound2-dev libavcodec-dev libavdevice-dev libavfilter-dev libavformat-dev libavutil-dev libcurl4-openssl-dev libfontconfig1-dev libfreetype6-dev libgl1-mesa-dev libjack-jackd2-dev libjansson-dev libluajit-5.1-dev libpulse-dev libqt5x11extras5-dev libspeexdsp-dev libswresample-dev libswscale-dev libudev-dev libv4l-dev libvlc-dev libx11-dev libx11-xcb1 libx11-xcb-dev libxcb-xinput0 libxcb-xinput-dev libxcb-randr0 libxcb-randr0-dev libxcb-xfixes0 libxcb-xfixes0-dev libx264-dev libxcb-shm0-dev libxcb-xinerama0-dev libxcomposite-dev libxinerama-dev pkg-config python3-dev qtbase5-dev libqt5svg5-dev swig libwayland-dev qtbase5-private-dev libpci-dev ./libfdk-aac-dev_2.0.1-1_arm64.deb ./libfdk-aac2_2.0.1-1_arm64.deb

# debian bullseye
sudo apt install build-essential cmake git libmbedtls-dev libasound2-dev libavcodec-dev libavdevice-dev libavfilter-dev libavformat-dev libavutil-dev libcurl4-openssl-dev libfontconfig1-dev libfreetype6-dev libgl1-mesa-dev libjack-jackd2-dev libjansson-dev libluajit-5.1-dev libpulse-dev libqt5x11extras5-dev libspeexdsp-dev libswresample-dev libswscale-dev libudev-dev libv4l-dev libvlc-dev libx11-dev libx11-xcb1 libx11-xcb-dev libxcb-xinput0 libxcb-xinput-dev libxcb-randr0 libxcb-randr0-dev libxcb-xfixes0 libxcb-xfixes0-dev libx264-dev libxcb-shm0-dev libxcb-xinerama0-dev libxcomposite-dev libxinerama-dev pkg-config python3-dev qtbase5-dev libqt5svg5-dev swig libwayland-dev qtbase5-private-dev libpci-dev libfdk-aac2 libfdk-aac-dev
```

#### Compiling OBS from source

```bash
cd
git clone --recursive https://github.com/obsproject/obs-studio.git
cd obs-studio
mkdir build
cd build

# if you don't want cef support
cmake -DUNIX_STRUCTURE=1 -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_PIPEWIRE=OFF -DBUILD_BROWSER=OFF ..

# if you want cef support (replace cef root dir with correct path)
cmake -DUNIX_STRUCTURE=1 -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_PIPEWIRE=OFF -DBUILD_BROWSER=ON -DCEF_ROOT_DIR='/home/user/obs-studio-updates/CEF/cef_binary_95.7.18+g0d6005e+chromium-95.0.4638.69_linuxarm64_minimal' ..

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

------

## AntiMicroX

```
version=3.2.4

# install debian package scripts
sudo apt install devscripts -y

# download and prepare source
wget https://github.com/AntiMicroX/antimicrox/archive/refs/tags/$version.tar.gz -O antimicrox-$version.tar.gz
tar -xvf antimicrox-$version.tar.gz
cd antimicrox-$version

# download and prepare packaging files
wget https://github.com/ryanfortner/debianization-files/releases/download/zips/antimicrox-debianization.zip
unzip antimicrox-debianization.zip
rm antimicrox-debianization.zip
sed -i 's/REPLACEME/$version/' debian/changelog

# build the package
dpkg-buildpackage -us -uc -nc

# check parent directory for the package
cd ../
```

------

## BalenaEtcher

```
version=v1.7.9

# Install dependencies
sudo apt-get install -y git python gcc g++ ruby-dev make libx11-dev libxkbfile-dev fakeroot rpm libsecret-1-dev jq python2.7-dev python3-pip python-setuptools libudev-dev
sudo gem install fpm --no-document
# install nodesource repo (I found that 16.x works most reliably with newer etcher versions, but feel free to go higher than this if you like)
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs

# clone repo and checkout release
git clone --recursive https://github.com/balena-io/etcher
cd etcher
git checkout $version

# install requirements with pip
pip install -r requirements.txt

# limit node memory usage for older pi models
export NODE_OPTIONS="--max-old-space-size=1024"

# setup and install npm modules
make electron-develop
# at this point you should be able to run a test of Etcher with `npm start`.

# disable tiffutil (mac only app)
sed -i 's/tiffutil/#tiffutil/g' Makefile 
# restrict output to .deb package only to save build time (not necessary unless you only want .deb)
sed -i 's/TARGETS="deb rpm appimage"/TARGETS="deb"/g' scripts/resin/electron/build.sh

# build and package, forcing use of the arm fpm version
USE_SYSTEM_FPM="true" make electron-build 
```

The compiled binaries and/or packages will be in the etcher/dist folder.

------

## Remarkable

The original Remarkable package had many dependency issues that have not yet been fixed upstream. For now, we have repackaged it with correct dependencies using [these instructions](https://github.com/seiferteric/remarkable_debfix).

------

## usbimager

compile libui first
```
cd
# if this version of meson isn't new enough, get it from pip
sudo apt install meson
git clone https://github.com/andlabs/libui.git
cd libui
meson setup  build --buildtype=release --default-library=static
```
then clone usb imager
```
cd
git clone https://gitlab.com/bztsrc/usbimager.git -b 1.0.8
cd usbimager
```
copy libui.a from the ~/libui/build/meson-out folder and put it in ~/usbimager/src/libu
remame libui.a to raspbian.a (for armhf) or raspios.a (for arm64)

edit the Makefile ~/usbimager/src/Makefile and change these contents to match
```
ifeq ($(ARCH),x86_64)
LIBS += libui/linux.a -ldl
else
ifeq ($(ARCH),aarch64)
LIBS += libui/raspios.a -ldl
else
LIBS += libui/raspbian.a -ldl
endif
```
now make the usbimager deb
```
sudo apt install build-essential libgtk-3-dev libudisks2-dev libglib2.0-dev
cd ~/usbimager/src
USE_LIBUI=yes USE_UDISKS2=yes make all deb
```
if all went well, the deb is now in the ~/usbimager folder

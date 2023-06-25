# This file contains the commands required to compile the different applications stored in this repository.

theofficialgman suggests getting an always free Oracle ARM64 build server and putting the Ubuntu Bionic image on it https://www.oracle.com/cloud/free/ (4 cores, up to 24GB or ram, 200GB of storage).

for building packages for ARMhf, theofficialgman suggests building packages within a ubuntu bionic schroot (to provide maximum compatibility with supported pi-apps systems). there are some steps below (may be missing some parts).
```
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

you can now enter the chroot with 
`sudo schroot -c bionic-armhf`. you probably want to add your same user to the chroot by executing inside the chroot:
```
echo "<username> ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers
useradd -u 1000 --shell /bin/bash -rmUG wheel,video,audio,render <username>
su - <username>
```

## OBS

#### Compiling with Chromium Embedded Framework

To compile with CEF support (and to allow browser sources and dock support), use the following instructions from https://github.com/Botspot/pi-apps/issues/1698.

1. download the minimal CEF binary from here https://cef-builds.spotifycdn.com/index.html#linuxarm64 for version 4758 which is the latest with pre glibc 2.29 support (click show all builds -> show more builds). Download the minimal version.
2. extract that tar.bz2 to a folder and move to that folder
3. create a `build` directory in that folder and move to that folder
4. execute `cmake .. -DPROJECT_ARCH="arm64"` this is to generate the libcef wrapper
4. `make -j$(nproc)`

now you have built CEF wrapper (chromium embedded framework wrapper), continue to follow the instructions below.

then go to however you normally build obs studio but enable the browser source and point to the CEF folder directory
so for example:
```
-DBUILD_BROWSER=ON -DCEF_ROOT_DIR='/home/ubuntu/obs-cef/cef_binary_98.2.1+g29d6e22+chromium-98.0.4758.109_linuxarm64_minimal'
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

# generic deps from OBS wiki

# add QT6 ppa if on ubuntu and if necessary
sudo add-apt-repository ppa:okirby/qt6-backports
sudo apt install qt6-base-dev qt6-base-private-dev libqt6svg6-dev

sudo apt install libavcodec-dev libavdevice-dev libavfilter-dev libavformat-dev libavutil-dev libswresample-dev libswscale-dev libx264-dev libcurl4-openssl-dev libmbedtls-dev libgl1-mesa-dev libjansson-dev libluajit-5.1-dev python3-dev libx11-dev libxcb-randr0-dev libxcb-shm0-dev libxcb-xinerama0-dev libxcb-composite0-dev libxcomposite-dev libxinerama-dev libxcb1-dev libx11-xcb-dev libxcb-xfixes0-dev swig libcmocka-dev libxss-dev libglvnd-dev libgles2-mesa libgles2-mesa-dev libwayland-dev libpci-dev libasound2-dev libfdk-aac-dev libfontconfig-dev libfreetype6-dev libjack-jackd2-dev libpulse-dev libsndio-dev libspeexdsp-dev libudev-dev libv4l-dev libva-dev libvlc-dev libdrm-dev
```

#### Compiling OBS from source

```bash
version=28.1.2

git clone -b $version --recurse-submodules https://github.com/obsproject/obs-studio.git
cd obs-studio

mkdir build
cd build

# apply any patches to the codebase as necessary
# for tegra, we patch out the GL OES check since it produces a false negative (only fixed in recent Desktop Nvidia drivers)

# if you don't want cef support
cmake -DUNIX_STRUCTURE=1 -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_PIPEWIRE=OFF -DENABLE_AJA=0 -DENABLE_NEW_MPEGTS_OUTPUT=OFF -DBUILD_BROWSER=OFF -DOBS_BUILD_NUMBER=1 -DOBS_VERSION_OVERRIDE=$version ..

# if you want cef support (replace cef root dir with correct path)
cmake -DUNIX_STRUCTURE=1 -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_PIPEWIRE=OFF -DENABLE_AJA=0 -DBUILD_BROWSER=OFF -DENABLE_NEW_MPEGTS_OUTPUT=OFF -DBUILD_BROWSER=ON -DCEF_ROOT_DIR='/home/ubuntu/obs-cef/cef_binary_98.2.1+g29d6e22+chromium-98.0.4758.109_linuxarm64_minimal' -DOBS_BUILD_NUMBER=1 -DOBS_VERSION_OVERRIDE=$version ..

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

```bash
# depends to build deb packages
sudo apt install -y equivs devscripts
git clone https://github.com/linuxmint/timeshift.git
cd timeshift
# get and install build dependencies as an apt package timeshift-build-deps
sudo mk-build-deps -i
# build the deb
debuild -b -uc -us
# uninstall build deps
sudo apt-get purge --auto-remove timeshift-build-deps
# replace this with the version you built
sudo apt install ../timeshift_22.06.5_arm64.deb
```

------

## Github Desktop

https://github.com/Botspot/pi-apps/pull/1775#issuecomment-1119159519

(waiting on PR to merge for simpler instructions: https://github.com/desktop/dugite-native/pull/368 )

------

## AntiMicroX

```bash
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

theofficialgman: I hate building etcher. node is fully of broken packages

etcher requires manual patches to the git repo (change to electron 13.5.0+ for newer linux distro compatibility) as well as forcing the usb node module to rebuild since the precompiled version depends on glibc 2.29+ (not available on buster/bionic)

```bash
version=v1.10.0

# Install dependencies
sudo apt-get install -y git python gcc g++ ruby-dev make libx11-dev libxkbfile-dev fakeroot rpm libsecret-1-dev jq python2.7-dev python3-pip python-setuptools libudev-dev
sudo gem install fpm --no-document
# install nodesource repo (I found that 16.x works most reliably with newer etcher versions, but feel free to go higher than this if you like)
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs

# clone repo and checkout release
git clone --recurse-submodules --depth 1 --branch "$version" https://github.com/balena-io/etcher
cd etcher

# install requirements with pip
pip install -r requirements.txt

# limit node memory usage for older pi models
export NODE_OPTIONS="--max-old-space-size=1024"

# IMPORTANT: do these steps manuall
# edit the package.json
# change electron version 12.0.0 to ^13.5.0
# change the spectron version from 14.0.0 to 15.0.0
# change electron-builder to ^23.0.9 (armhf compat)

# setup and install npm modules
npm install
npm ci

# correct the broken prebuilt for the usb node module
rm ~/etcher/node_modules/usb/prebuilds/linux-arm*/node.napi.armv*.node
cd ~/etcher/node_modules/usb
npm install
npm run prebuild
# rename the file so other programs are happy
cd prebuilds/linux-arm64
# cd prebuilds/linux-arm
mv node.napi.node node.napi.armv8.node
# mv node.napi.node node.napi.armv7.node

# correct the broken lzma-native package (they ship x86_64 binaries under the ARM64 name ): )
cd ~/etcher/node_modules/lzma-native
npm install
npm run prebuild

# now start probably won't work so we will just package and then you can test the appimage
cd ~/etcher
npm run webpack


# build and package, forcing use of the system installed fpm version
ELECTRON_BUILDER_ARCHITECTURE=arm64 USE_SYSTEM_FPM=true npm exec electron-builder --linux
# ELECTRON_BUILDER_ARCHITECTURE=armv7l USE_SYSTEM_FPM=true npm exec electron-builder --linux
```

The compiled binaries and/or packages will be in the etcher/dist folder.

------

## Remarkable

The original Remarkable package had many dependency issues that have not yet been fixed upstream. For now, we have repackaged it with correct dependencies using [these instructions](https://github.com/seiferteric/remarkable_debfix).

------

## usbimager

compile libui first
```bash
cd
# if this version of meson isn't new enough, get it from pip
sudo apt install meson
git clone https://github.com/andlabs/libui.git
cd libui
meson setup  build --buildtype=release --default-library=static
```
then clone usb imager
```bash
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
```bash
sudo apt install build-essential libgtk-3-dev libudisks2-dev libglib2.0-dev
cd ~/usbimager/src
USE_LIBUI=yes USE_UDISKS2=yes make all deb
```
if all went well, the deb is now in the ~/usbimager folder

------

## Caprine

```bash
# Install dependencies
sudo apt-get install -y git gcc g++ ruby-dev make fakeroot rpm
sudo gem install fpm --no-document
# install nodesource repo
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs


git clone https://github.com/sindresorhus/caprine --depth=1 --branch v2.56.1
cd caprine
# remove package lock if we want the lastest dependencies allowed
rm package-lock.json
npm install
npm run build
# without any arguments, this will build the native host architecture
USE_SYSTEM_FPM=true npx electron-builder --linux deb rpm AppImage
```

Now the debs are in `./dist/`, which are `caprine_x.xx.x_arm64.deb` and `caprine_x.xx.x_armv7l.deb`.

------


## Deskreen

Make sure you have `git` and `yarn` installed.
If you have nodejs 16.X LTS installed (the minimum supported version), follow the script below.
If you have nodejs 17.X or greater, you will need to add `NODE_OPTIONS=--openssl-legacy-provider` before every `yarn` command. This is because electron-builder does not currently support the latest openssl included in newer nodejs.

You also need to have a system install of `fpm` available in `PATH`. This is not available the deb repos so I found the easiest way to install it is with `gem`. You can install gem with apt with `sudo apt install ruby-rubygems` or `sudo apt install ruby` (the package name changed on newer debian/focal so only one of those will work). `gem install fpm` will install fpm and make it available in `PATH`. Verfify you can access fmp by running `fpm --version` which will print out the installed version (ex: 1.14.2).

There should not be any failures with any of the following steps.

```
cd
git clone https://github.com/pavlobu/deskreen.git
cd deskreen
# checkout the latest release tag, currently v2.0.3
git checkout v2.0.3
# edit package.json at this point "package" to "package": "yarn build && electron-builder build --arm64 --publish never"
# or switch --arm64 to --armv7l for an armhf pacakge
cd ~/deskreen/app/client
yarn install --frozen-lockfile
cd ~/deskreen
yarn install --frozen-lockfile
cd ~/deskreen/app
yarn install --frozen-lockfile
cd ~/deskreen
USE_SYSTEM_FPM="true" yarn package
```

the deb, rpm, and appimage will now be available in the `release` folder

------

## Alacritty Terminal

```
git clone https://github.com/barnumbirr/alacritty-debian.git
git clone https://github.com/alacritty/alacritty.git
cd alacritty
# git checkout whatever version or commit you want, current deb is tag v0.11.0
git checkout v0.11.0

# copy the debian folder into alacritty
cp -a ../alacritty-debian/debian .

# you need to manually edit debian/changelog and debian/control to contain the version and architecture information as necessary
# arm64 and armhf are valid architectures

# install build dependencies
sudo mk-build-deps --install
sudo dpkg-source --before-build .
# compile sources (uses rustc)
debian/rules build
# create debs
sudo debian/rules binary

# optionally remove the build dependencies
sudo apt remove alacritty-build-deps -y
```
debs will be one folder up of alacritty

------

## fsnotifier

literally this folder inside a zip https://github.com/JetBrains/intellij-community/tree/master/native/fsNotifier/linux

------

## Prism Launcher

assuming you are building on Ubuntu Bionic (for compatability and PPA usage)

add these ppas:
https://launchpad.net/~theofficialgman/+archive/ubuntu/opt-qt-5.15.2-bionic-arm
https://launchpad.net/~theofficialgman/+archive/ubuntu/cmake-bionic

install depenendcies:
`sudo apt install cmake zlib1g-dev libgl1-mesa-dev extra-cmake-modules clang-10 openjdk-11-jdk qt5153d qt515base qt515declarative qt515gamepad qt515graphicaleffects qt515imageformats qt515multimedia qt515xmlpatterns qt515svg`

install makedeb (you NEED to use the alpha release or a release greater than v16.1.0-alpha10):
`bash -ci "$(wget -qO - 'https://shlink.makedeb.org/install')"`

check the makedeb config file for any X86 flags near the top of the file (for older makedeb releases):
`sudo nano /etc/makepkg.conf`

create a PKGBUILD file in a new folder
```
# Maintainer: theofficialgman <28281419+theofficialgman@users.noreply.github.com>

pkgname=prismlauncher
pkgver=6.3
pkgrel=2
pkgdesc="Minecraft launcher with ability to manage multiple instances."
arch=('arm64' 'armhf')
depends=('libatk-bridge2.0-0' 'libatk1.0-0' 'libatspi2.0-0' 'libblkid1' 'libbsd0' 'libc6' 'libcairo-gobject2' 'libcairo2' 'libdatrie1' 'libdbus-1-3' 'libdrm2' 'libegl1' 'libepoxy0' 'libexpat1' 'libfontconfig1' 'libfreetype6' 'libgcc1' 'libgcrypt20' 'libgdk-pixbuf2.0-0' 'libgl1' 'libglib2.0-0' 'libglvnd0' 'libglx0' 'libgpg-error0' 'libgraphite2-3' 'libgtk-3-0' 'libgtk2.0-0' 'libharfbuzz0b' 'libice6' 'libjbig0' 'liblcms2-2' 'liblz4-1' 'liblzma5' 'libmount1' 'libmtdev1' 'libpango-1.0-0' 'libpangocairo-1.0-0' 'libpangoft2-1.0-0' 'libpcre3' 'libpixman-1-0' 'libpng16-16' 'libselinux1' 'libsm6' 'libstdc++6' 'libsystemd0' 'libthai0' 'libudev1' 'libuuid1' 'libwayland-client0' 'libwayland-cursor0' 'libwayland-egl1' 'libx11-6' 'libx11-xcb1' 'libxau6' 'libxcb-glx0' 'libxcb-icccm4' 'libxcb-image0' 'libxcb-keysyms1' 'libxcb-randr0' 'libxcb-render-util0' 'libxcb-render0' 'libxcb-shape0' 'libxcb-shm0' 'libxcb-sync1' 'libxcb1' 'libxcb-xfixes0' 'libxcb-xinerama0' 'libxcb-xinput0' 'libxcb-xkb1' 'libxcb1' 'libxcomposite1' 'libxcursor1' 'libxdamage1' 'libxdmcp6' 'libxext6' 'libxfixes3' 'libxi6' 'libxinerama1' 'libxkbcommon-x11-0' 'libxkbcommon0' 'libxrandr2' 'libxrender1' 'zlib1g')
url="https://github.com/PrismLauncher/PrismLauncher"
license=('GPL3')

prepare() {
  rm -rf ${pkgname}
  git clone -b ${pkgver} --recursive https://github.com/PrismLauncher/PrismLauncher.git ${pkgname}
}

build() {
  cd "${pkgname}/"
  mkdir -p build
  cd build
  cmake -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX="/usr" \
    -DLauncher_BUILD_PLATFORM="debian" -DLauncher_QT_VERSION_MAJOR=5 -DCMAKE_PREFIX_PATH=/opt/qt515 -DCMAKE_C_COMPILER=clang-10 -DCMAKE_CXX_COMPILER=clang++-10 \
    ..
  cmake --build . -j$(nproc)
}

check() {
  cd "${pkgname}/build"
  ctest .
}

package() {
  cd "${pkgname}/build"
  DESTDIR="$pkgdir" cmake --install .
  DESTDIR="$pkgdir" cmake --install . --component portable
  rm "${pkgdir}/usr/portable.txt"
  sed -i 's/-d "${LAUNCHER_DIR}"//g' "${pkgdir}/usr/PrismLauncher"
  mkdir -p "${pkgdir}/usr/share/${pkgname}/plugins" "${pkgdir}/usr/share/${pkgname}/lib" "${pkgdir}/usr/share/${pkgname}/bin" "${pkgdir}/usr/share/${pkgname}/jars"
  mv "${pkgdir}/usr/PrismLauncher" "${pkgdir}/usr/share/${pkgname}"
  mv "${pkgdir}/usr/bin/${pkgname}" "${pkgdir}/usr/share/${pkgname}/bin"
  cd "${pkgdir}/usr/share/${pkgname}"
  mv ./*.jar "${pkgdir}/usr/share/${pkgname}/jars"
  cd "${pkgdir}/usr/bin"
  ln -s ../share/${pkgname}/PrismLauncher "${pkgname}"
  cd /opt/qt515/plugins
  cp -r bearer egldeviceintegrations iconengines imageformats platforminputcontexts platforms platformthemes xcbglintegrations "${pkgdir}/usr/share/${pkgname}/plugins/"
  cd /opt/qt515/lib
  cp -P libQt5Core.so libQt5Core.so.5 libQt5Core.so.5.15 libQt5Core.so.5.15.2 libQt5DBus.so libQt5DBus.so.5 libQt5DBus.so.5.15 libQt5DBus.so.5.15.2 libQt5EglFSDeviceIntegration.so libQt5EglFSDeviceIntegration.so.5 libQt5EglFSDeviceIntegration.so.5.15 libQt5EglFSDeviceIntegration.so.5.15.2 libQt5EglFsKmsSupport.so libQt5EglFsKmsSupport.so.5 libQt5EglFsKmsSupport.so.5.15 libQt5EglFsKmsSupport.so.5.15.2 libQt5Gui.so libQt5Gui.so.5 libQt5Gui.so.5.15 libQt5Gui.so.5.15.2 libQt5Network.so libQt5Network.so.5 libQt5Network.so.5.15 libQt5Network.so.5.15.2 libQt5Svg.so libQt5Svg.so.5 libQt5Svg.so.5.15 libQt5Svg.so.5.15.2 libQt5Widgets.so libQt5Widgets.so.5 libQt5Widgets.so.5.15 libQt5Widgets.so.5.15.2 libQt5XcbQpa.so libQt5XcbQpa.so.5 libQt5XcbQpa.so.5.15 libQt5XcbQpa.so.5.15.2 libQt5Xml.so libQt5Xml.so.5 libQt5Xml.so.5.15 libQt5Xml.so.5.15.2 libQt5Concurrent.so libQt5Concurrent.so.5 libQt5Concurrent.so.5.15 libQt5Concurrent.so.5.15.2 "${pkgdir}/usr/share/${pkgname}/lib/"
  
  # QT5 built by this PPA has external dependencies that can not be satisified by all desired distros. So we copy the required libs from the host (as well as any of their dependencies) and place them in the lib folder to be used by prism launcher
  
  # dpkg_architecture is the default userspace cpu architecture (arm64, amd64, armhf, i386, etc)
  dpkg_architecture="$(dpkg --print-architecture)"
  case "$dpkg_architecture" in
  "arm64") cd /usr/lib/aarch64-linux-gnu; deb_url="http://ftp.us.debian.org/debian/pool/main/q/qtstyleplugins-src/qt5-gtk2-platformtheme_5.0.0+git23.g335dbec-4+b3_arm64.deb" ;;
  "armhf") cd /usr/lib/arm-linux-gnueabihf; deb_url="http://ftp.us.debian.org/debian/pool/main/q/qtstyleplugins-src/qt5-gtk2-platformtheme_5.0.0+git23.g335dbec-4+b3_armhf.deb" ;;
  esac
  
  cp -P ./libcrypto.so.1.1 ./libssl.so.1.1 ./libxcb-util.so.1 ./libxcb-util.so.1.0.0 ./libjpeg.so.8 ./libjpeg.so.8.1.2 ./libicudata.so.60 ./libicudata.so.60.2 ./libicui18n.so.60 ./libicui18n.so.60.2 ./libicuuc.so.60 ./libicuuc.so.60.2 ./libmng.so.2 ./libmng.so.2.0.2 ./libffi.so.6 ./libffi.so.6.0.4 ./libtiff.so.5.3.0 ./libtiff.so.5 "${pkgdir}/usr/share/${pkgname}/lib/"
  
  # The version of QT used from the PPA is QT 5.15.2. In order to get automatic gtk+ themeing support, the QT5 gtk+ plugin needs to be provided in the plugins folder built against QT 5.15.2. The simplest location to obtain this would be ubuntu hirsute (repos taken down) or debian bullseye repos.
  
  cd /tmp
  rm -rf temp-deb
  mkdir temp-deb
  cd temp-deb
  wget "$deb_url"
  dpkg-deb -R qt5-gtk2-platformtheme_5.0.0+git23.g335dbec-4+b3_*.deb ./
  cd ./usr/lib/*/qt5/plugins
  cp -r platformthemes styles "${pkgdir}/usr/share/${pkgname}/plugins/"
  rm -rf /tmp/temp-deb
}
```

run makedeb from the folder that contains your PKGBUILD
`makedeb -s`

helpful final check for what libraries are getting loaded at runtime when the application is already running

`cat /proc/$(pgrep prismlauncher)/maps | awk '{print $(NF)}' | sort -u`

------

## Wine

Install all dependencies as following https://wiki.winehq.org/Building_Wine

`Wine (x64)`
should be built on a bionic amd64 chroot
```bash
# download and extract sources
version=8.11
mkdir -p ~/wine
cd ~/wine
wget https://dl.winehq.org/wine/source/8.x/wine-${version}.tar.xz
tar -xvf wine-${version}.tar.xz
# build
mkdir wine-${version}-build
cd wine-${version}-build
../wine-${version}/configure --enable-archs=i386,x86_64 --prefix=/opt/wine-${version}
make -j12
# install
sudo make install
# package
cd /opt
sudo tar -czvf ~/wine/wine-${version}.tar.gz wine-${version}
# upload to github releases
gh release --repo Pi-Apps-Coders/files upload large-files ~/wine/wine-${version}.tar.gz
```

`Wine (x86)`
should be built on a bionic i386 chroot
```bash
# download and extract sources
version=8.11
mkdir -p ~/wine
cd ~/wine
wget https://dl.winehq.org/wine/source/8.x/wine-${version}.tar.xz
tar -xvf wine-${version}.tar.xz
# build
mkdir wine-${version}-build
cd wine-${version}-build
../wine-${version}/configure --prefix=/opt/wine-${version}
make -j12
# install
sudo make install
# package
cd /opt
sudo tar -czvf ~/wine/wine-i386-${version}.tar.gz wine-${version}
# upload to github releases
gh release --repo Pi-Apps-Coders/files upload large-files ~/wine/wine-i386-${version}.tar.gz
```

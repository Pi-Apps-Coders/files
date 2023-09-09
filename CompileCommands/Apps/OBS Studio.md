## OBS

#### Compiling with Chromium Embedded Framework

To compile with CEF support (and to allow browser sources and dock support), use the following instructions from https://github.com/Botspot/pi-apps/issues/1698.

1. download the minimal CEF binary from here https://cef-builds.spotifycdn.com/index.html#linuxarm64 for version 4758 which is the latest with pre glibc 2.29 support or anything newer that uses chromium 104+ (click show all builds -> show more builds). Download the minimal version. CEF 102+ currently broken on ARM64 Linux https://github.com/chromiumembedded/cef/issues/3565
2. extract that tar.bz2 to a folder and move to that folder
3. create a `build` directory in that folder and move to that folder
4. execute `cmake .. -DPROJECT_ARCH="arm64"` this is to generate the libcef wrapper
4. `make -j$(nproc)`

now you have built CEF wrapper (chromium embedded framework wrapper), continue to follow the instructions below.

then go to however you normally build obs studio but enable the browser source and point to the CEF folder directory
so for example:
```
-DBUILD_BROWSER=ON -DCEF_ROOT_DIR='/home/ubuntu/obs-cef/cef_binary_101.0.18+g367b4a0+chromium-101.0.4951.67_linuxarm64_minimal'
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
version=29.1.3

git clone --recurse-submodules --shallow-submodules --depth=1 -j$(nproc) -b $version https://github.com/obsproject/obs-studio.git
cd obs-studio

mkdir build
cd build

# apply any patches to the codebase as necessary
# for tegra, we patch out the GL OES check since it produces a false negative (only fixed in recent Desktop Nvidia drivers)

# if you don't want cef support
# enable PIPEWIRE on Ubuntu 22.04+ Builds
# cmake -DUNIX_STRUCTURE=1 -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_JACK=ON -DENABLE_PULSEAUDIO=ON -DENABLE_PIPEWIRE=OFF -DENABLE_AJA=OFF -DENABLE_NEW_MPEGTS_OUTPUT=OFF -DBUILD_BROWSER=OFF -DOBS_BUILD_NUMBER=1 -DOBS_VERSION_OVERRIDE=$version ..

# if you want cef support (replace cef root dir with correct path)
# enable PIPEWIRE on Ubuntu 22.04+ Builds
cmake -DUNIX_STRUCTURE=1 -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_JACK=ON -DENABLE_PULSEAUDIO=ON -DENABLE_PIPEWIRE=OFF -DENABLE_AJA=OFF -DENABLE_QSV11=OFF -DENABLE_WEBRTC=OFF -DENABLE_NEW_MPEGTS_OUTPUT=OFF -DBUILD_BROWSER=ON -DCEF_ROOT_DIR=$HOME'/obs-cef/cef_binary_101.0.18+g367b4a0+chromium-101.0.4951.67_linuxarm64_minimal' -DOBS_BUILD_NUMBER=1 -DOBS_VERSION_OVERRIDE=$version ..

make -j$(nproc)
cmake --build $HOME/obs-studio/build -t package
```

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
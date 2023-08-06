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
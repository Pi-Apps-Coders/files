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

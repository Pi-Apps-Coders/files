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
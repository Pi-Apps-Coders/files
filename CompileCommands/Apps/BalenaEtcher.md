## BalenaEtcher

theofficialgman: I hate building etcher. node is fully of broken packages
etcher now requires Ubuntu Focal+ to builds since it needs nodejs 20+

etcher requires manual patches to the git repo (change to electron 13.5.0+ for newer linux distro compatibility) as well as forcing the usb node module to rebuild since the precompiled version depends on glibc 2.29+ (not available on buster/bionic)

```bash
version=v1.19.25

# Install dependencies
sudo apt-get install -y git python gcc g++ ruby-dev make libx11-dev libxkbfile-dev fakeroot rpm libsecret-1-dev jq python2.7-dev python3-pip python-setuptools libudev-dev
# install nodesource repo 
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# clone repo and checkout release
git clone --recurse-submodules --depth 1 --branch "$version" https://github.com/balena-io/etcher
cd etcher

# limit node memory usage for older pi models
export NODE_OPTIONS="--max-old-space-size=1024"

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

cd ~/etcher

# remove sidecar as it is not used in etcher and causes armhf build failures
# apply the patch from here https://github.com/balena-io/etcher/issues/4256#issuecomment-2183830284

# actually package the application
npm run make
```

The compiled binaries and/or packages will be in the out/make folder.

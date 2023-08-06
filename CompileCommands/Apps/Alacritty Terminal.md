## Alacritty Terminal

```bash
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
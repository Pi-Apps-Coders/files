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
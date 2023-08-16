## Timeshift

```bash
# depends to build deb packages
sudo apt install -y equivs devscripts
git clone https://github.com/linuxmint/timeshift.git -b 23.07.1
cd timeshift
# get and install build dependencies as an apt package timeshift-build-deps
sudo mk-build-deps -i
# install newer meson and symlink it to /usr/bin due to debhelper requirements
sudo apt install python3.8 python3.8-venv
sudo -H python3.8 -m pip install --upgrade pipx
sudo PIPX_HOME=/usr/local/pipx PIPX_BIN_DIR=/usr/local/bin pipx install meson
sudo mv /usr/bin/meson /usr/bin/meson.backup
sudo ln -s /usr/local/bin/meson /usr/bin/meson
# revert some commits that relate to changing away from deprecated APIs where the new API is not available on our older distros
git revert 4607bcc908be3b2f5d3cea1de8cf93b1a8ea3c55
git revert e18377e33e3e31521bbddf9cfff08e384e7b8939
git revert 240225d5471a186e21ef9f80666a9db53b06bd13
# build the deb
debuild -b -uc -us
# uninstall build deps
sudo apt-get purge --auto-remove timeshift-build-deps
# replace this with the version you built
sudo apt install ../timeshift_23.07.1_arm64.deb
```

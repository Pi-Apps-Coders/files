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
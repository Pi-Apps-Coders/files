## usbimager

compile libui first
```bash
cd
# if this version of meson isn't new enough, get it from pip
sudo apt install meson
git clone https://github.com/andlabs/libui.git
cd libui
meson setup  build --buildtype=release --default-library=static
```
then clone usb imager
```bash
cd
git clone https://gitlab.com/bztsrc/usbimager.git -b 1.0.8
cd usbimager
```
copy libui.a from the ~/libui/build/meson-out folder and put it in ~/usbimager/src/libu
remame libui.a to raspbian.a (for armhf) or raspios.a (for arm64)

edit the Makefile ~/usbimager/src/Makefile and change these contents to match
```
ifeq ($(ARCH),x86_64)
LIBS += libui/linux.a -ldl
else
ifeq ($(ARCH),aarch64)
LIBS += libui/raspios.a -ldl
else
LIBS += libui/raspbian.a -ldl
endif
```
now make the usbimager deb
```bash
sudo apt install build-essential libgtk-3-dev libudisks2-dev libglib2.0-dev
cd ~/usbimager/src
USE_LIBUI=yes USE_UDISKS2=yes make all deb
```
if all went well, the deb is now in the ~/usbimager folder
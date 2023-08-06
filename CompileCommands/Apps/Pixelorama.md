## Pixelorama

to save time we will only build the armhf export template and will use the arm64 godot server binary

from armhf bionic chroot
first build godot from source
```bash
cd ~
git clone --depth=1 -b 3.5.2-stable https://github.com/godotengine/godot.git
cd godot
# build export template (making sure to pass flags to both CC and LINK as LTO needs these)
scons platform=x11 tools=no target=release use_lto=yes bits=32 arch=arm32 CCFLAGS="-mfpu=neon-fp-armv8 -mfloat-abi=hard" LINKFLAGS="-mfpu=neon-fp-armv8 -mfloat-abi=hard"
# we will copy this export template build later into the bionic arm64 chroot
```

from arm64 bionic chroot
first build godot from source
```bash
cd ~
git clone --depth=1 -b 3.5.2-stable https://github.com/godotengine/godot.git
cd godot
# build server
scons platform=server tools=yes target=release_debug use_lto=yes bits=64 arch=arm64
# build export template
scons platform=x11 tools=no target=release use_lto=yes bits=64 arch=arm64
# copy export template to godot directory
mkdir -p ~/.local/share/godot/templates/3.5.2.stable
cp bin/godot.x11.opt.arm64 ~/.local/share/godot/templates/3.5.2.stable/linux_x11_64_release
# copy the armhf export template, change source path as necessary
cp /srv/chroot/bionic-armhf/home/ubuntu/godot/bin/godot.x11.opt.arm32 ~/.local/share/godot/templates/3.5.2.stable/linux_x11_32_release
```
now build pixelorama
```bash
cd ~
git clone --depth=1 -b v0.11 https://github.com/Orama-Interactive/Pixelorama.git
cd Pixelorama
mkdir -p build/linux-arm64 build/linux-arm32
../godot/bin/godot_server.x11.opt.tools.arm64 -v --export  "Linux/X11 64-bit" ./build/linux-arm64/Pixelorama.arm64
../godot/bin/godot_server.x11.opt.tools.arm64 -v --export  "Linux/X11 32-bit" ./build/linux-arm32/Pixelorama.arm32
cp -R ./pixelorama_data ./build/linux-arm64
cp -R ./pixelorama_data ./build/linux-arm32
cd build/linux-arm64/
zip Pixelorama_v0.11_arm64.zip  ./*

cd ../../
cd build/linux-arm32/
zip Pixelorama_v0.11_arm32.zip  ./*

# upload to github releases
cd ~/files
gh release upload large-files ~/Pixelorama/build/linux-arm64/Pixelorama_v0.11_arm64.zip ~/Pixelorama/build/linux-arm32/Pixelorama_v0.11_arm32.zip
```
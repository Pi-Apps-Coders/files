## Caprine

```bash
# Install dependencies
sudo apt-get install -y git gcc g++ ruby-dev make fakeroot rpm
sudo gem install fpm --no-document
# install nodesource repo
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs


git clone https://github.com/sindresorhus/caprine --depth=1 --branch v2.56.1
cd caprine
# remove package lock if we want the lastest dependencies allowed
rm package-lock.json
npm install
npm run build
# without any arguments, this will build the native host architecture
USE_SYSTEM_FPM=true npx electron-builder --linux deb rpm AppImage
```

Now the debs are in `./dist/`, which are `caprine_x.xx.x_arm64.deb` and `caprine_x.xx.x_armv7l.deb`.

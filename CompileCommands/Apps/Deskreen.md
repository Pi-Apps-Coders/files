## Deskreen

Make sure you have `git` and `yarn` installed.
If you have nodejs 16.X LTS installed (the minimum supported version), follow the script below.
If you have nodejs 17.X or greater, you will need to add `NODE_OPTIONS=--openssl-legacy-provider` before every `yarn` command. This is because electron-builder does not currently support the latest openssl included in newer nodejs.

You also need to have a system install of `fpm` available in `PATH`. This is not available the deb repos so I found the easiest way to install it is with `gem`. You can install gem with apt with `sudo apt install ruby-rubygems` or `sudo apt install ruby` (the package name changed on newer debian/focal so only one of those will work). `gem install fpm` will install fpm and make it available in `PATH`. Verfify you can access fmp by running `fpm --version` which will print out the installed version (ex: 1.14.2).

There should not be any failures with any of the following steps.

```bash
cd
git clone https://github.com/pavlobu/deskreen.git
cd deskreen
# checkout the latest release tag, currently v2.0.3
git checkout v2.0.3
# edit package.json at this point "package" to "package": "yarn build && electron-builder build --arm64 --publish never"
# or switch --arm64 to --armv7l for an armhf pacakge
cd ~/deskreen/app/client
yarn install --frozen-lockfile
cd ~/deskreen
yarn install --frozen-lockfile
cd ~/deskreen/app
yarn install --frozen-lockfile
cd ~/deskreen
USE_SYSTEM_FPM="true" yarn package
```

the deb, rpm, and appimage will now be available in the `release` folder
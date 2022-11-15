Users:

These files are downloaded and used by scripts in [Pi-Apps](https://github.com/Botspot/pi-apps)
Most files are found in the github release which allows us to store large files: https://github.com/Pi-Apps-Coders/files/releases/tag/large-files

For developers:

Future apps/binaries should be uploaded to the large-files github release. You can do so via the web interface or with the github cli program
`gh release upload large-files path/to/file`

If you have releases that were generated as part of your automation but not directly used by pi-apps (eg: rpm or appimages for a release while pi-apps uses the deb), those can be uploaded as well (to benefit the non debian community).

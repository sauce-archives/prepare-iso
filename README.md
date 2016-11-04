This tool will create a disk image for OS X, given the downloaded installer OS .app (or the InstallESD.dmg inside the .app). Once built, the new disk image can be used along with packer to create a bootable disk image. Hooray!

The `-l` flag (described below) should be chosen based on the intended environment, either VirtualBox or QEMU.

All builds should run locally, even for QEMU - just transfer the finished image over to vmbuilder, etc.

# Getting Started

First, we need a copy of the Sierra app. You might already have it - check the location below. If not, it's easiest to download a copy from the App store (unfortunately). Link: https://itunes.apple.com/us/app/macos-sierra/id1127487414?mt=12

The file will be located here:
```
/Applications/Install macOS Sierra.app
```

Now use `prepare_iso.sh` with the downloaded Sierra app by running:
```
sudo prepare_iso/prepare_iso.sh -D DISABLE_REMOTE_MANAGEMENT -D DISABLE_SCREEN_SHARING -D DISABLE_SIP -l <SPUD|GPTSPUD> "/Applications/Install macOS Sierra.app" ~/iso/
```

About the command:
- `-D` flags are all necessary.
- `-l` flag is the partition layout. Use `SPUD` for VirtualBox, `GPTSPUD` for QEMU.
- First parameter after the flags is the location of either the Sierra app, or the path to `InstallESD.dmg` inside the app (`/Applications/Install macOS Sierra.app/Contents/SharedSupport/InstallESD.dmg`).
- Second parameter is the output directory for the newly built image.

The command should finish with output like this:
```
-- Done. Built image is located at ~/iso/OSX_InstallESD_10.12_16A323.dmg.cdr. Add this iso and its checksum to your template.
```

# Next Steps

Now you're ready to do a `rake build` in one of the saucelabs-images/chef-* repos.

Example command (VirtualBox):
```
bundle exec rake build BUILDER=virtualbox-iso ISO_PATH=~/iso/OSX_InstallESD_10.12_16A323.dmg.cdr
```

This will probably take about 20-30 minutes locally.

Afterward, you can load the image in VirtualBox using a command like this:
```
VBoxManage import output-virtualbox-iso/packer-virtualbox-iso-1478286404.ovf --vsys 0 --vmname sierra
```

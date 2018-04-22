# ZeroTier-OpenWrt

*This package has been merged into https://github.com/openwrt/packages and can be selected building OpenWrt/LEDE.*

[ZeroTier One](https://www.zerotier.com) is a programm to create a global provider-independent virtual private cloud.
This project offers OpenWrt packages for ZeroTier.

## Installing package

Download the [prebuild package](https://github.com/mwarning/zerotier-openwrt/releases) and copy it onto your OpenWrt installation, preferably into the /tmp folder.
Then install the ipk package file:
```
opkg install zerotier_*.ipk
```

Now start ZeroTier:
```
/etc/init.d/zerotier start
```

## Compiling from Sources

To inlcude ZeroTier One into your OpenWrt image or to create
an .ipk package (equivalent to Debians .deb files),
you have to build an OpenWrt image.

For building OpenWrt on Debian, you need to install these packages:
```
sudo apt-get install subversion g++ zlib1g-dev build-essential git python
sudo apt-get install libncurses5-dev gawk gettext unzip file libssl-dev wget
```

Now prepare OpenWrt:
```
git clone https://github.com/openwrt/openwrt
cd openwrt

./scripts/feeds update -a
./scripts/feeds install -a
```

Now you can insert the zerotier package using a package feed or add the package manually.

### Add package by feed

A feed is the standard way packages are made available to the OpenWrt build system.

Put this line in your feeds list file (e.g. feeds.conf.default)
```
src-git zerotier https://github.com/mwarning/zerotier-openwrt.git
```

Update and install the new feed
```
./scripts/feeds update zerotier
./scripts/feeds install zerotier
```

Now continue with the building packages section.

### Add package by hand

```
git clone https://github.com/mwarning/zerotier-openwrt.git
cp -rf zerotier-openwrt/zerotier package/
rm -rf zerotier-openwrt/
```

Now continue with the building packages section.

### Building Packages

Configure packages:

```
make menuconfig
```

Now select the appropiate "Target System" and "Target Profile"
depending on what target chipset/router you want to build for.
Also mark the ZeroTier package under Network ---> VPN ---> <\*> zerotier.

Now compile/build everything:

```
make
```

The images and all *.ipk packages are now inside the bin/ folder, including the zerotier package.
You can install the ZeroTier .ipk on the target device using "opkg install \<ipkg-file\>".

For details please check the OpenWrt documentation.

#### Build bulk packages

For a release, it is useful the build packages at a bulk for multiple targets:

```
#!/bin/sh

targets='
	CONFIG_TARGET_arm64=y
	CONFIG_TARGET_ath25=y
'

for target in $targets; do
	echo "$target" > .config
	echo "CONFIG_PACKAGE_zerotier=y" >> .config

	# Debug output
	echo "Build: $target"

	# Build toolchain and package
	make defconfig
	make tools/install
	make toolchain/install
	make package/zerotier/{clean,compile} V=s

	# Free space
	rm -rf build_dir/target-*
	rm -rf build_dir/toolchain-*
done
```

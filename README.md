# Testing
testing the implemented functionality


the IPsec ui doesnt show even when selected during the image config process.


after a couple of minutes of being on the device shuts down suddenly with only the power sources leds remaining on. the reset button does not work either. -> Seems like the authentication session was set to 10 minutes in the uci configs, changed to an hour. still doesnt explain why it shutsdown.

serial console output before shutdown
```
root@NR200:/# [ 1741.034763] br-lan: port 1(eth0) entered disabled state
[ 1741.050617] device eth0 left promiscuous mode
[ 1741.055215] br-lan: port 1(eth0) entered disabled state
[ 1745.969061] reboot: Restarting system
```


# Image Flashing
after generating a NR200 image we get the following error from onion:
```
Device omega2 not supported by this image
Supported devices: saa,nr-200 nr-200
Image check 'fwtool_check_image' failed.
```
perhaps a .bin image for should be generated specific for first time flash in order to get the proper device name? 

## Make issues

## loading settings into the build tool
when loading in the config file for the project the targeted profile becomes ***ALFA NETWORK AWUSFREE1***  Instead of the **DEVICE_saa_nr-200** written in the given config.in file.
## build issues
If compiled for the default Awusfree1 it results in an image called
```
openwrt-ramips-mt76x8-alfa-network_awusfree1-squashfs-sysupgrade.bin
```
which cannot be flashed to the hardware through the sysupgrade systemcall.

When corrected for the Onion omega2S+  the following build error is  encountered :
```
....bin is too big : 111149865 > 33226752
```
attempted solutions were to remove some packages in order to strip it down but it did not resolve the situation.

## Config Files
from my understanding we have three main config files for the build system to parse.
**Feeds.conf.default**  which is our file which the custom feed gets generate from, lists the resources to be obtained and downloaded in order for the feed to be included in the build image.
we include the feed using :
```
./scripts/feeds update -a
./scripts/feeds install -a 
```
which updates the index and installs the resources.

**Config.in**  contains the following
```
# Copyright (C) 2006-2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

mainmenu "OpenWrt Configuration"

config MODULES
	option modules
	bool
	default y

config HAVE_DOT_CONFIG
	bool
	default y

source "target/Config.in"

source "config/Config-images.in"

source "config/Config-build.in"

source "config/Config-devel.in"

source "toolchain/Config.in"

source "target/imagebuilder/Config.in"

source "target/sdk/Config.in"

source "target/toolchain/Config.in"

source "tmp/.config-package.in"
```
which from my understanding indicates the config files for the indvidual component such as sdk , imagebuilder ...

**ConfigNR200** which is a config file that gets loaded into the menuconfig interface using it's load function and sets up the required packages and components for the image.

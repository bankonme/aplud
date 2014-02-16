aplud
=====

***Automatic Persistent Live USB (Debian)***

Usage
-----

	$ wget -c http://live.debian.net/cdimage[...]debian-live-[...].iso
	# whereis/aplud debian-live.iso /dev/sdX


Requires
--------

* 7z
* syslinux
* mkfs.vfat
* mkfs.ext4
* mbr
* fdisk

Debian-based distro : 

	apt-get install 7zip-full syslinux dosfstools mbr util-linux
	
About
-----

This creates a live, persistent USB drive, with Debian (by default), grml (you
want grml2usb), slitaz4 (home persistence only). It's similar to unetbootin, 
but it's more minimalist and base code is less intimidating - it's just a 
shell script. 

It works with a plugin system, so you can create a definition for other distros.

The included plugins will try to set up the language and keyboard, so you should
not have to be worried about this (it uses $LANG environment variable).

It can create a live USB with an extra partition with the free space left on
the device from any ISOLINUX based Live-CD (See 'Using Plugins')

Runtime Problems
----------------

* Parted Failed 'device is busy' : Aplud tries hard to circumvent automounter programs. Anyway you should wait some seconds after plugging your USB pendrive, then launch aplud.

* The resulting USB pendrive may be stuck with "MBR" message. Retry aplud on it. 

* If you have no mkfs.* programs on your system, modify global.conf accordingly.

Using Plugins
-------------

Edit 'distro.conf' and comment every entry but the one you want to use.

You can comment everything. Then the default will be used. It will work
only if the 'isolinux' directory is in the 'boot' directory.

Then run alupd.

Creating Plugins
----------------

Let's say you want to add the 'newdistro' distribution : 

	$ cd whereisaplud 
	$ cp grml.conf newdistro.conf
	$ echo '. $configdir/newdistro.conf' >> distro.conf 
	$ vi newdistro.conf

Instructions are inside the .conf file. 


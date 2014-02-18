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

Debian distro : 

	apt-get install 7zip-full syslinux dosfstools mbr util-linux

```dosfstools``` is also known as ```mtools```
	
About
-----

This creates a live USB drive with:

* debian (by default, full persistence) 
* grml (you want ```grml2usb```, full persistence)
* ubuntu (full persistence, *painfully slow*)
* mint (full persistence)
* slitaz4 (home persistence only).
* Any ISOLINUX based Live-CD (See 'Using Plugins' for limitations), it will then create an extra partition using the free space left on the device. No persistence. It has been added for convenience, it works very often, but may not. 
 
It's similar to unetbootin, usb-creator etc., but it's minimalist and base code is less
intimidating - it's just a shell script.

It will use the size of the isofile + 10% for the livesystem partition, the
rest of the pendrive storage being used for persistence/data as an ext4 filesystem.

It works with a plugin system, so you can create a definition for other distros.

The included plugins will try to set up the language and keyboard, so you should
not have to be worried about this (it uses $LANG environment variable).

### Performance ###

Please note that persistent live usb is slower than a standard live usb.

Getting the good device
-----------------------

* Plug your usb pendrive in your computer
* Run

		$ dmesg | tail
		[...]
		[ 5717.903243] sd 6:0:0:0: [sdb] Attached SCSI removable disk
		[...]

* Here the device is ```/dev/sdb```


Runtime Problems
----------------

* fdisk fails with 'device is busy' : Aplud tries hard to circumvent
  automounter programs. Anyway you should wait some seconds after plugging your
  USB pendrive, then launch aplud. Also be sure no program is in a device
  mountpoint

* The resulting USB pendrive may be stuck with "MBR" message. Retry aplud on it. 

* If you have no mkfs.* programs on your system, modify global.conf accordingly.

Using Plugins
-------------

Add the distro name as the third argument of aplud. To get the list of all 
supported distros, type 

	$ ./aplud

### Using unsupported distros ###

Use simply 'generic' as distro name. It's not granted it will work, the ```isolinux```
directory must be in the ```boot``` directory of the ISO.

Creating Plugins
----------------

### How alupd works ###

You need to know that before creating a plugin. In a nutshell it acts like this : 

1. Partition the drive
2. Create filesystems
3. Extract the iso in the live filesystem
4. Convert ISOLINUX to SYSLINUX *It's where plugin matters*
   1. It needs to modify syslinux.cfg (or whatever .cfg file used by the target distro)
     to add keyboard/language/whatever needed (```persistence``` keyword is needed
     for Debian for example).

### What you need ###

For this you'll need to extract the iso and find the isolinux folder :
   
	$ mkdir /tmp/iso 
	$ cd /tmp/iso
	$ 7z x file.iso
	$ find . -type d -name isolinux 
	./boot/isolinux

Note that the syslinux prefix will be ```"boot/"```. Never use a leading '/' !
If ```isolinux/``` is in the current directory, the syslinux prefix should be
""

Now read the cfg files to find the boot entries in this isolinux folder, and
catch a common string among them. Most often it's simply ```isolinux.cfg```,
then try this: 

	$ sed 's/\(that common string\)/\1 myparam/g' file.cfg 

to test.

* ```that common string``` will be in the plugin file the value of the variable ```appentrigger```
* ```myparam``` will be in the plugin file the value of the variable ```bootappend```
* ```file.cfg``` will be in the plugin file the value of the variable ```syslinuxcfg``` If the file is ```isolinux.cfg``` then use ```syslinux.cfg``` instead

You'll need to know the boot options of the live cd you wan to use as well.

### Actual plugin creation ###
     
Let's say you want to add the 'newdistro' distribution : 

	$ cd whereisaplud/conf 
	$ cp grml.conf newdistro.conf
	$ vi newdistro.conf

It's documented by itself, and if you've the values found just before, it should work:

	$ cd whereisaplud
	# ./aplud newdistro-i686-20141210.iso /dev/sdX newdistro

debian-uwa-icpc-contestant-env
==============================

This is the configuration tree for:

  Debian UWA ICPC Contestant Environment

A software called live-build can be used to automatically build images from
this configuration tree.

live-build can be obtained from <http://live-systems.org/devel/live-build/>.
On Debian based systems, live-build can be installed with:

  # apt-get install live-build

live-build can be used to build this image with the following command executed
in this directory::

  # lb build

More information about live-build and the Live Systems project can be found on
its homepage at <http://live-systems.org/> and in the manual at
<http://live-systems.org/manual/>.


Notes
=====

Summary
-------

This Debain Live USB configuration is based on the Debian xfce-desktop configuration available at <http://live.debian.net/gitweb/?p=live-images.git>. The main changes are:

* Disable installer and root access.
* Install compilers, interpreters and editors required for the competition.
* Remove unneeded software to safe space.

Full details of the changes can be made by downloading the xfce-desktop configuration and using `diff`.

`auto/config`
-------------

This is a script that runs `lb config` and auto-generates most of the configuration files in `config/`. Main changes:

* Disable building source ISO.
* Disable memtest.
* Change apt to aptitude (allows removing unneeded software).
* Append boot parameters to:
	* Disable Debian installer.
	* Change hostname and username.
	* Change timezone.
	* Disable root access.

See `man lb config` for documentation on the `lb config` command and its options.

`config/bootloaders`
--------------------

These are syslinux configuration files that determine the options in the bootloader. Main changes:

* Set a timeout.
* Disable menu items for failsafe live environment, installer, and advanced options.
* Immediately boot live environment.
* Disable editing boot options.

`config/package-lists`
----------------------

These are lists of packages to be installed. Main changes:

* Remove installer.
* Remove unneeded desktop software (e.g. libreoffice).
* Install programming languages for competition.
* Install editors and IDEs.

The `icpc-contestant.list.chroot` and `icpc-languages.list.chroot` comprise the main changes relative to the base Debian install.

Additional software can be installed by adding to the lists here.

`config/includes.chroot`
------------------------

These are files to be included in the Live USB environment. They are added after the packages in `config/package-lists` are installed, and will overwrite any files that are already present. Main additions:

* Desktop shortcuts to DOMjudge server and on-the-day resources.
* XFCE configuration file to hide filesystem desktop shortcuts (i.e. filesystems belonging to the computer the Live USB is booting off)

Additional files could be added here to, e.g. configure firewall rules or printing services.

`config/hooks`
--------------

These are scripts that are run after the packages in `config/package-lists` are installed and the files in `config/includes.chroot` are added. Main addition:

* Run `update-alternatives` to set the default Java VM and compiler to Java 7.

Additional scripts could be added here to, e.g. configure firewall rules or printing services.

Building an image
-----------------

To build an image:

# lb build

The image will be saved as `binary.hybrid.iso`. This image is ready to be burned to a CD or copied to a USB device.

To clean up after a previous build:

# lb clean

This will remove the chroot environment and some cache files, but keep the package cache (i.e. downloaded .deb files) so they don't need to be downloaded again.

Creating a Live USB with persistence
------------------------------------

Divide a USB stick into two partitions:

1. A FAT32 partition to store the read-only Live USB environment. Size accordingly.
2. An ext4 partition to store persistent data. The label for the partition must be `persistence`.

Use an appropriate tool to create a bootable Live USB using the first partition, e.g. unetbootin (Linux) or Universal USB Installer (Windows). Once the bootable Live USB is created, on the Live USB partition edit the file `isolinux/live.cfg` and add the `persistence` option to the end of the `append` line.

On the root of the persistence partition, create a file called `persistence.conf`. This configures which directories on the Live USB will be persistent. See `man persistence.conf` for more details.

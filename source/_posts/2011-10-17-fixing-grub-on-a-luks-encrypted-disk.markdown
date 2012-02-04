---
layout: post
title: "Fixing Grub on a LUKS-encrypted Disk"
date: 2011-10-17 15:41
comments: true
categories:
  - linux
  - sysadmin
---

When I rebooted my new work notebook (a very nice x201s, with a dual-core i7, 8GB RAM and an SSD by the way), it seemed that for whatever reason there was no Grub installed. Strange, but not the only problem I had with the alternate Ubuntu installer that I used for setting up [LUKS-encrypted](http://en.wikipedia.org/wiki/Linux_Unified_Key_Setup) root and swap partitions. Since this turned out to be more involved than I originally thought, I decided to document what I did to maybe save others the trouble. BTW: You'll need a Linux Live-CD for this, I recommend the excellent Debian-based [Grml](http://grml.org/).

First we need to open the LUKS partition, which can be done with [cryptsetup](http://linux.die.net/man/8/cryptsetup).

{% codeblock lang:bash Step 1: Unlock LUKS %}
$ cryptsetup luksOpen /dev/<device> <name>
# you'll be asked for your password
{% endcodeblock %}

With that out of the way, you'll have to mount your LVM volumes. This took me a bit because my VG wasn't active, but thanks to an excellent blog post I found (see sources) this problem was solved quickly.

{% codeblock lang:bash Step 2: Mounting LVM %}
# do a lvm disk scan
$ lvmdiskscan
# activate the volume group
$ vgchange -ay
# make sure the VG is active now
$ lvscan
# mount the actual volume
$ mount /dev/<volume_group_name>/<volume> /mnt/external
{% endcodeblock %}

I then needed to mount the unencrypted `/boot` partition as well as some special filesystems (with bind mounts) at the appropriate mount points.

{% codeblock lang:bash Step 3: Preparing for the chroot %}
# mount the boot partion
$ mount /dev/<device> /mnt/external/boot
# bind mount some things we need in the chroot env
$ mount --bind /dev /mnt/external/dev
$ mount --bind /proc /mnt/external/proc
$ mount --bind /sys /mnt/external/sys
{% endcodeblock %}

With everything set up, all that's left to do is `chroot`ing into the system and fixing Grub.

{% codeblock lang:bash Step 4: chrooting and fixing Grub %}
$ chroot /mnt/external
chroot$  grub-setup # grub-install, whatever you need
{% endcodeblock %}

Sources:

* [Creating and mounting a LUKS encrypted disk](http://www.shallowsky.com/blog/linux/luks-encryption.html)
* [mount: unknown filesystem type ‘LVM2_member’](http://pissedoffadmins.com/?p=481)

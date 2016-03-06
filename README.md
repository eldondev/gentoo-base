## Filesystem Boilerplate for Purpose-Built Gentoo Installs==

> This is just a tribute.
> ~ *Tenacious D*

*The goal of this git repository is to provide some filesystem boilerplate for
a certain style of [Gentoo](https://www.gentoo.org) installation for
purpose-built systems.*

Gentoo is perhaps my favorite Linux distribution, and I've deployed it in a
number of scenarios over time. It isn't usually for the faint of heart, and
will certainly enable users to shoot themselves in the foot if they do
something silly. Along with great responsibility comes great power, and it is
possible for Gentoo to perform well in a number of different scenarios,
partially due to the fact that all packages are often compiled in concert on
the deployment machine. Gentoo's portage command ```emerge``` is the tool that
orchestrates all of this compiling, dependency management, etc. This is great
if you've got all day to compile, but for doing all of this for an application
distributed across a number of machines, the process can get cumbersome and
expensive. Enter the new whole-system packaging methodologies that are coming
into vogue today, ie docker and whole-vm disk images. ```emerge``` can handle
some of this for us as well, with the --root argument, which allows packages to
be deployed based on another directory as root. In my particular case, I
sometimes like to deploy a hardened, 64bit-only (nomultilib) version of Gentoo,
with minimal fluff in it. This means sometimes a stage3 has somewhat more in it
than I would prefer (particularly for things like docker containers).  However
I have run into a few issues doing so, and this tiny repository (just a few
directories, really) is my answer to those issues.

The following bits must be done as root, so great caution or a test system is useful.


The qemu command used to make this image looks like this: 
``` qemu-img create -f raw  /dev/shm/qemu.img 4G ```

Then the disk is set up as a loop device and partitioned via sfdisk.
```
LOOP=`losetup -f --show /dev/shm/qemu.img`
sfdisk $LOOP <sfscript 
mkfs.btrfs $LOOP\p1
mount $LOOPp1 /mnt/altroot
```
Then I have a fresh qemu image to work with, so I move the contents of this repo there, and then emerge there.


```
time  PORTAGE_CONFIGROOT=/mnt/altroot ROOT=/mnt/altroot emerge -vK -j --load-average 4  @system grub app-crypt/gnupg app-misc/screen app-emulation/docker htop
```

which gives me a specific minimal system, this one being suitable for the start
of a minimal multi-function virtual machine. I can copy whichever kernel I
would like into /mnt/altroot/boot.  I can then unmount the root fs and run
something like

```
(sleep 15 && echo -e "passme\n" && sleep 1 && echo -e "mount -o remount,rw /\\n" && sleep 1 && echo -e "echo 'root:hello'|chpasswd\\n" && sleep 1 && echo -e "halt\\n" ) | qemu-system-x86_64 -machine accel=kvm  -m 1G -smp 2  -kernel /boot/vmlinuz  -hda /dev/shm/qemu.img  -nographic -append "console=ttyS0 root=/dev/sda1 single"
```

This sets the root password of the new system. Then I run normal
```
qemu-system-x86_64 -machine accel=kvm  -m 1G -smp 2 -kernel /boot/vmlinuz -append "root=/dev/sda1 single"  -curses -hda /dev/shm/qemu.img
```

which will start the machine with the supplied kernel in single user mode. From
there I can install grub onto the qemu image (this is safer than mucking with
grub on the host).

In the above example, the current qemu image is ~500Mb, with docker and
necessary build tools installed.


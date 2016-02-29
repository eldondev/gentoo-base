==Filesystem Boilerplate for Purpose-Built Gentoo Installs==

This is just a tribute.
- Tenacious D

= The goal of this git repository is to provide some filesystem boilerplate for
a certain style of Gentoo installation for purpose-built systems. =

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

If I clone this repo into, say, a new qemu image /dev/shm/qemu.img mounted on
/mnt/altroot (a raw image mounted via loop), the types of commands I am running
are 

```
time emerge -v -j3 --load-average 4 --root=/mnt/altroot @system grub app-crypt/gnupg app-misc/screen
```

which gives me a specific minimal system, this one being suitable for the start
of a minimal multi-function virtual machine. I can copy whichever kernel I
would like into /mnt/altroot/boot.  I can then unmount the root fs and run
something like

```
qemu-system-x86_64 -machine accel=kvm  -m 1G -smp 2 -kernel /boot/vmlinuz-4.4.0 -append "root=/dev/sda1 single"  -curses -hda /dev/shm/qemu.img
```

which will start the machine with the supplied kernel in single user mode. From
there I can install grub onto the qemu image (this is safer than mucking with
grub on the host).

In the above example, the current qemu image is ~252Mb, just a little more than
a stage3.



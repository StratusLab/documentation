
Alternate Storage Types
=======================

StratusLab also offers two other storage types to support specific use
cases: temporary storage and readonly storage.  

Volatile Storage
----------------

Many applications have a need for a large storage space for temporary
data.  StratusLab provides volatile storage for this purpose.  The
space will be reclaimed (and the data lost) when the virtual machine
is terminated.  

When starting a virtual machine a volatile disk can be added to the
machine using the ``--volatile-disk`` option::

    $ stratus-run-instance \
        --volatile-disk 10 \
        KhGzWhB9ZZv5ZkLSZqm6pkWx7ZF

You must provide the size (in GiB) of the disk to be allocated.  

As for persistent disks, the volatile disks are not formatted.  You
will need to find and format the disk in the same way as for a
persistent disk.  

.. admonition:: Exercise 

   Start a virtual machine with a volatile disk.  Find, format, mount,
   and store data on the disk.  Verify that the data on the disk will
   survive a machine reboot.  (Reboot the virtual machine using
   ``reboot`` within the virtual machine.)  Unless you added the disk
   to the `/etc/fstab` file, you will need to remount the disk after
   the reboot.  The disk and data will disappear when the machine is
   terminated with ``stratus-kill-instance``.

Static Disks
------------

Many applications use datasets that are either fixed or change
slowly, for example, input databases for a scientific calculation.
Since these are fixed datasets, you can take advantage of the virtual
machine image caching mechanisms to efficiently cache and duplicate
these datasets. 

These static (read-only) disks can be attached when starting a virtual
machine.  As for virtual machine images, these disk images are also
registered in the Marketplace.  To start a machine with a static
disk::

  $ stratus-run-instance \
      --readonly-disk GPAUQFkojP5dMQJNdJ4qD_62mCo \
      KhGzWhB9ZZv5ZkLSZqm6pkWx7ZF

The value used for the ``--readonly-disk`` is the Marketplace
identifier of the disk.  The one used here is a CD-ROM image with a
small database of "Flora and Fauna".  You can search the Marketplace
for it. 

You can find, mount, and use the disk::

    $ blkid 
    /dev/sr0: LABEL="_STRATUSLAB" TYPE="iso9660" 
    /dev/sda1: UUID="b73f3da6-3ca3-4833-a2ab-8a03d6a47b2c" TYPE="ext4" 
    /dev/sdb: UUID="2a76d471-02d3-45e1-859f-eef3d2946dbb" TYPE="swap" 
    /dev/sdc: LABEL="CDROM" TYPE="iso9660" 

    $ mkdir /mnt/data 
    $ mount /dev/sdc /mnt/data 
    mount: block device /dev/sdc is write-protected, mounting read-only

    $ ls -lR /mnt/data 
    /mnt/data:
    total 4
    dr-xr-xr-x 1 root root 2048 Jan 26  2013 animals
    dr-xr-xr-x 1 root root 2048 Jan 26  2013 plants

    /mnt/data/animals:
    total 1
    -r-xr-xr-x 1 root root 4 Jan 26  2013 cat.txt
    -r-xr-xr-x 1 root root 4 Jan 26  2013 dog.txt

    /mnt/data/plants:
    total 1
    -r-xr-xr-x 1 root root 5 Jan 26  2013 rose.txt
    -r-xr-xr-x 1 root root 6 Jan 26  2013 tulip.txt

Note that the disk is read-only, so you cannot make any changes to
this disk. 

The disk will appear in the virtual machine exactly as it has been
formatted in the reference image. Usually these images are formatted as
a CD-ROM (or DVD) image so that they are portable between different
operating systems.

When creating such images it is strongly recommended (unlike here)
that they be created with a label so that they can easily be mounted
by the user without needing to know what device has been assigned.

.. admonition:: Exercise 

   Run a machine with this disk image.  Verify that it is indeed
   read-only.  What happens if you start a second disk with the same
   image? 


Using Storage and Compute Resources Together
============================================

Having the ability to use persistent storage on a virtual machine
allows you to keep the results of an analysis or to separate the
service state (e.g. a database) from the service installation.

Persistent storage can be attached to a virtual machine when it is
deployed or dynamically after it has started.

Attach a Disk at Deployment Time
--------------------------------

The easiest way to attach a persistent disk to machine is to use the
option ``--persistent-disk`` when launching a virtual machine.  The
command for doing this looks like::

    $ stratus-run-instance --persistent-disk=${VOLUME_UUID} ${APP_ID}

Attaching a disk at deployment time has a couple of limitations.
First, only one persistent disk can be attached to the machine.
Second, the persistent disk remains attached to the virtual machine
for the entire lifetime of the virtual machine. 

Initializing the Disk
~~~~~~~~~~~~~~~~~~~~~

A new persistent disk is an empty, uninitialized block device.
Consequently, the first time the disk is used, it must be formatted. 

When the virtual machine starts the persistent disk will be attached
to the machine.  Logging into the virtual machine, you can find the
device name for the disk by using the command ``fdisk -l``, looking for
a disk without a partition table and with the expected size.

A new physical disk is usually partitioned so that the space can be
used for different purposes (file storage, swap, etc.).  For
persistent disks on the cloud, there is really no benefit in doing
this unless there is a reason you need more than one file system on
the disk.  If you do want to partition the disk, you can use the
command ``fdisk`` to do so.

You must however format the disk to make it useful. To format the
disk, use the command ``mkfs``.  It is **very strongly recommended
that you provide a label for the file system**.

::

    $ mkfs --type ext4 -L MYLABEL /dev/sdc
    mke2fs 1.42.9 (4-Feb-2014)
    /dev/sdc is entire device, not just one partition!
    Proceed anyway? (y,n) y
    Filesystem label=MYLABEL
    ...
    Writing superblocks and filesystem accounting information: done

If you did not partition the disk, you'll be asked about formatting
the entire device.  Just answer yes ('y') in this case.

.. warning:: 

   The formatting of the disk should be done **only the first time you
   use the persistent disk**. If you reformat the disk later, all of
   the existing data will be lost.

Mounting the Disk
~~~~~~~~~~~~~~~~~

To use the disk to store data, it must be mounted into the file
system.  From within the virtual machine do::

    $ mkdir /mnt/pdisk
    $ mount -L MYLABEL /mnt/pdisk
    $ ls /mnt/pdisk 
    lost+found
    $ touch /mnt/pdisk/mydata.txt 

This shows that you can use the disk normally as you would any other
disk on the system. 

.. note::

   The benefit in providing a label is that you don't need to search
   for the device name of the disk.  The device name may vary
   depending on when the disk is attached to the machine and different
   operating systems may use different naming conventions.

When the machine is shutdown, the persistent disk will be released and
can then be mounted on another machine. 

.. admonition:: Exercise 

   Repeat this procedure yourself.  At the end, start up a separate
   virtual machine with this disk and verify that the file you
   initially stored is still there. 

Attach a Disk Dynamically
-------------------------

Although using the ``--persistent-disk`` option of
``stratus-run-instance`` is easy; it isn't always convenient.  You can
attach and detach disks dynamically from running virtual machines to
provide more flexiblity and to overcome the limitations stated
earlier.

The commands for attaching and detaching persistent disks dynamically
are::

    $ stratus-attach-volume --instance ${VM_ID} VOLUME_UUID
    $ stratus-detach-volume --instance ${VM_ID} VOLUME_UUID

These can be used at any time while the virtual machine is running.
Within the virtual machine, the volume must still be mounted from the
file system in order to be accessible.

Even though modern file systems are tolerant of abrupt deconnections of
devices, it is always a good idea to unmount the disk from the file
system from within the virtual machine before detaching the disk with
the ``stratus-detach-volume`` command.

.. admonition:: Exercise

   Start a virtual machine, wait for it to come up, and log into it.
   From another terminal, dynamically attach a persistent disk to the
   machine.  Verify that the disk is visible on the virtual machine
   (using ``blkid`` or ``fdisk -l``).  Mount the disk and verify that
   any previous data is visible.  Unmount the disk and detach it.
   Verify that the disk is no longer visible on the virtual machine. 

.. admonition:: Exercise

   Attach and detach a persistent disk multiple times.  Does the
   device name remain the same or change?  

.. admonition:: Exercise

   A volume can be mounted only on one virtual machine at a time. Try
   to mount a volume on more than one virtual machine to see what error
   message is given.

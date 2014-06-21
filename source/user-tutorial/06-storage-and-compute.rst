
Combining Storage and Compute
=============================

Create a persistent disk and then run a machine using this persistent
disk.

::

    $ stratus-run-instance --persistent-disk=${VOLUME_UUID} ${APP_ID}

when the virtual machine starts the persistent disk will be attached to
the machine. You can find the device name for the disk by using the
command ``fdisk -l``. (Look for a disk without a partition table and
that has the same size as your disk.)

Optionally partition (``fdisk``) and format the disk. Normally, there is
no need to partition the disk, unless you need more than one file system
on the disk.

To format the disk, use the command ``mkfs.ext4`` and then provide a
label for the file system. The label will allow you to more easily mount
the volume in the future without needing to know the device name
assigned by the virtualization layer.

::

    $ mkfs.ext4
    $ e2label /dev/vdc mylabel

NOTE: The formatting of the disk should only be done **once**. If you
reformat the disk, all of the existing data will be lost.

Now mount the disk and store some data.

::

    $ mkdir /mnt/pdisk
    $ mount --label mylabel /mnt/pdisk
    $ ls /mnt/pdisk

Shutdown the machine and then start another machine using this disk.
Verify that the data you saved on the previous machine is still there.

Dynamic Mounting of Disks
-------------------------

When you use the ``--persistent-disk`` option of
``stratus-run-instance``, the volume will be assigned to that virtual
machine for its entire lifetime. You can however, attach and detach
disks dynamically from running virtual machines.

In this case, start a virtual machine and wait until it is accessible
via ssh. Now use the ``stratus-attach-volume`` command to attach the
disk to the virtual machine. Verify from within the virtual machine that
the disk has been attached and is visible.

After this, unmount the disk from the file system and then use the
``stratus-detach-volume`` to remove the volume from the virtual machine.
Verify from within the virtual machine that the disk as indeed be
detached.

Even though modern file systems are tolerant of abrupt deconnections of
the device, it is always a good idea to unmount the disk from the file
system from within the virtual machine before detaching the disk with
the command.

.. note:: 

   A volume can be mounted only on one virtual machine at a time. Try
   to mount a volume on more than one machine to see what error
   message is given.

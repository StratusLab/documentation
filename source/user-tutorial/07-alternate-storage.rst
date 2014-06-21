
Alternate Storage Types
=======================

Volatile Storage
----------------

Used for the storage of large *temporary* data files. The data will
disappear when the VM instance is destroyed.

When starting a virtual machine a volatile disk can be added to the
machine using the ``--volatile-disk`` option. You must provide the size
(in GiB) of the disk.

.. note:: 

   The disks are not formatted! So you'll need to format and mount the
   disk. You can find the disk with the ``fdisk -l`` command.

**Exercise:** Use a volatile disk and store some data on it. Verify
that the data will survive a **reboot** of the machine. The data will
**not** survive the shutdown of the machine.

Static Disks
------------

Used for the distribution and caching of fixed/versioned data. Takes
advantage of disk caching mechanisms of StratusLab for efficient access.

When starting a virtual machine a static (read-only) disk can be
attached. These disks are declared in the Marketplace and have a
Marketplace identifier. The option to use is ``--readonly-disk`` with
the Marketplace identifier.

The disk will appear in the virtual machine exactly as it has been
formatted in the reference image. Usually these images are formatted as
a CD-ROM (or DVD) image so that they are portable between different
operating systems.

When creating such images it is strongly recommended that they be
created with a label so that they can easily be mounted by the user
without needing to know what device has been assigned.

Search for the "Flora and Fauna" image in the Marketplace. Start a
virtual machine with this image and verify that 1) the information can
be read, 2) that it is indeed read-only, and 3) you can serve this
information via a web server.

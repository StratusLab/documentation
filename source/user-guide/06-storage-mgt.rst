Storage Management
==================

Persistent Disk Storage is the StratusLab service that physically stores
machine and disk images as volumes on the cloud site. It facilitates
quick startup of VMs and hot-plugging of disk volumes as block devices
to the VMs.

Create persistent disk
----------------------

Before creating persistent disks (or volumes) [1]_, you should define
the persistent disk storage endpoint by either of

-  in your HOME/.stratuslab/stratuslab-user.cfg
-  set the environment variable STRATUSLAB\_PDISK\_ENDPOINT
-  pass it as ***-*-pdisk-endpoint** command when creating one.

Lets create a persistent disk of 5GB and named "myprivate-disk"

::

    $ stratus-create-volume --size=5 --tag=myprivate-disk
    DISK 9c5a2c03-8243-4a1b-a248-0f0d22d948c2

The command returned the UUID of the created persistent disk. Newly
created disk is by default

-  private - can be read, written and deleted only by their owner
-  of type **read-write data image**

To create public persistent disk, pass *-*-public argument to
stratus-create-volume

::

    $ stratus-create-volume --public --size=10 --tag=mypublic-disk
    DISK d955fda6-bf9c-4aa8-abc4-5bbcdb83021b

or update the disk with

::

    stratus-update-volume --public <UUID>

To get the list of available disk types and the properties that can be
updated on the disk run

::

    stratus-update-volume -h

List persistent disks
---------------------

stratus-describe-volumes allows you to query the list of all your public
and private persistent disks, and also all the public persistent disks
created by other users.

::

    $ stratus-describe-volumes 
    :: DISK a4324f26-39e0-4965-8c8f-3287cd0936e5
            created: 2011/07/20 16:37:10
            visibility: public
            tag: mypublic-disk
            owner: testor2
            size: 5
            users: 0
    :: DISK 9c5a2c03-8243-4a1b-a248-0f0d22d948c2
            created: 2011/07/20 16:10:37
            visibility: private
            tag: myprivate-disk
            owner: testor1
            size: 5
            users: 0
    :: DISK d955fda6-bf9c-4aa8-abc4-5bbcdb83021b
            created: 2011/07/20 16:26:31
            visibility: public
            tag: mypublic-disk
            owner: testor1
            size: 5
            users: 0

The above command lists 'testor1' public and private persistent disks,
and also 'testor2' public ones.

Using Persistent Disks
----------------------

Workflow:

-  Launch a virtual machine instance referencing a persistent disk
-  Format the disk in the running VM
-  Write data to the disk
-  Dismount the disk or halt the machine instance
-  Disk with persistent data is available for use by another VM
-  Launch another VM referencing the modified persistent disk

Launch VM with a persistent disk attached
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To Launch a VM we will be using the ttylinux image identifier
GOaxJFdoEXvqAm9ArJgnZ0\_ky6F from StratusLab Marketplace (default one).

*-*-persistent-disk=UUID option when used with stratus-run-instance,
tells StratusLab to attach the referenced persistent disk(UUID) to the
VM.

Instantiate ttylinux image with reference to your private persistent
disk 9c5a2c03-8243-4a1b-a248-0f0d22d948c2.

::

    $ stratus-run-instance \
        --persistent-disk=9c5a2c03-8243-4a1b-a248-0f0d22d948c2 \
        GOaxJFdoEXvqAm9ArJgnZ0_ky6F

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 3)
            Public ip: 134.158.75.35

Log into your VM using ssh, depending in the linux kernel and
distribution version of your VM, your persistent disk will be referenced
as /dev/hdc or /dev/sdc.

In ttylinux, it will be /dev/hdc.

Make sure that your disk was attached to your VM

::

    $ ssh root@134.158.75.35
    # fdisk -l 
    .........
    Disk /dev/hdc: 5368 MB, 5368709120 bytes
    255 heads, 63 sectors/track, 652 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    .........

Format attached disk
~~~~~~~~~~~~~~~~~~~~

::

    # mkfs.ext3 /dev/hdc

Mount disk, store data, unmount disk
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    # mount /dev/hdc /mnt
    # echo "Testing Persistent Disk" > /mnt/test_pdisk
    # umount /mnt 

Your persistent disk is ready to be used by another VM.

Launch VM with the modified persistent disk attached
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Instantiate new VM ttylinux with the same reference to your private
persistent disk 9c5a2c03-8243-4a1b-a248-0f0d22d948c2.

::

    $ stratus-run-instance \
        --persistent-disk=9c5a2c03-8243-4a1b-a248-0f0d22d948c2 \
        GOaxJFdoEXvqAm9ArJgnZ0_ky6F

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 4)
            Public ip: 134.158.75.36

Log into your VM using ssh, verify existence of your persistent disk

::

    $ ssh root@134.158.75.35
    # fdisk -l
    ...........
    Disk /dev/hdc: 5368 MB, 5368709120 bytes
    255 heads, 63 sectors/track, 652 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    ...........

Mount your persistent disk

::

    # mount /dev/hdc /mnt
    # ls /mnt
    lost+found  test_pdisk
    # cat /mnt/test_pdisk 
    Testing Persistent Disk

Hot-plug Persistent Disks
-------------------------

StratusLab storage also provides hot-plug feature for persistent disk.
With stratus-attach-instance you can attach a volume to a running
machine and with stratus-detach-instance you can release it.

To use the hot-plug feature, the running instance needs to have acpiphp
kernel module loaded. Image like ttylinux doesn't have this feature, you
have to use base image like Ubuntu, CentOS or Fedora.

Before hot-plugin a disk, make sure acpiphp is loaded. In your VM
execute

::

    modprobe acpiphp

To attach two volumes to the VM ID 24 with the UUIDs
1e8e9104-681c-4269-8aae-e513c6723ac6 and
5822c376-9ce1-434e-95d1-cdaa240cd47c::

    $ stratus-attach-volume -i 24 1e8e9104-681c-4269-8aae-e513c6723ac6 5822c376-9ce1-434e-95d1-cdaa240cd47c
    ATTACHED 1e8e9104-681c-4269-8aae-e513c6723ac6 in VM 24 on /dev/vda
    ATTACHED 5822c376-9ce1-434e-95d1-cdaa240cd47c in VM 24 on /dev/vdb  

Use the fdisk -l command as above to see the newly attached disks.

Make sure to unmount any file systems on the device within your
operating system before detaching the volume. Failure to unmount file
systems, or otherwise properly release the device from use, can result
in lost data and will corrupt the file system.

::

    umount /dev/vda
    umount /dev/vdb

When you finish using your disks, you can detach them from the running
VM

::

    $ stratus-detach-volume -i 24 1e8e9104-681c-4269-8aae-e513c6723ac6 5822c376-9ce1-434e-95d1-cdaa240cd47c
    DETACHED 1e8e9104-681c-4269-8aae-e513c6723ac6 from VM 24 on /dev/vda
    DETACHED 5822c376-9ce1-434e-95d1-cdaa240cd47c from VM 24 on /dev/vdb

On running instance detaching not hot-plugged disks or disks that were
not or are no longer attached to the instance will result in an error

::

    $ stratus-detach-volume -i 41 2a17226f-b006-45d8-930e-13fbef3c6cdc
    DISK 2a17226f-b006-45d8-930e-13fbef3c6cdc: Disk have not been hot-plugged

If you have attached the volume at instance start-up, it cannot be
detached while the instance is in the 'Running' state. To detach the
volume, stop the instance first.

Delete Persistent Disks
-----------------------

To delete a persistent disk use the stratus-delete-volume command, note
that you can delete only your disks.

To delete 'myprivate-disk' with UUID
9c5a2c03-8243-4a1b-a248-0f0d22d948c2

::

    $ stratus-delete-volume 9c5a2c03-8243-4a1b-a248-0f0d22d948c2
    DELETED 9c5a2c03-8243-4a1b-a248-0f0d22d948c2

Check that the disk is no longer there

::

    $ stratus-describe-volumes 
    :: DISK a4324f26-39e0-4965-8c8f-3287cd0936e5
            created: 2011/07/20 16:37:10
            visibility: public
            tag: mypublic-disk
            owner: testor2
            users: 0
            size: 5
    :: DISK d955fda6-bf9c-4aa8-abc4-5bbcdb83021b
            created: 2011/07/20 16:26:31
            visibility: public
            tag: mypublic-disk
            owner: testor1
            users: 1
            size: 5

Now try to delete 'mypublic-disk' of 'testor2' user persistent disk

::

    $ stratus-delete-volume a4324f26-39e0-4965-8c8f-3287cd0936e5
      [ERROR] Service error: Not enough rights to delete disk

Read Only Volumes
-----------------

In addition to persistent and volatile data volumes, StratusLab also
supports read-only volumes. These come in handy when you have fixed (or
slowly changing) data sets that you would like to use with multiple
machines.

Many scientific and engineering applications require access to fixed (or
slowly changing) data sets. This includes versioned copies of databases
(e.g. protein databases) or calibration information. These are often
inputs to calculations that analyze larger, more varied data sets.

Persistent disk volumes and volatile disk volumes are not well-suited
for this. The persistent disks cannot be shared between machine
instances, so multiple copies of the disk need to be maintained or the
data needs to be shared through a file server (e.g. NFS). Use of
volatile storage would require copying the data each time a new machine
instance starts.

Read-only disks allow users to create a fixed disk image containing the
data. It is then registered in the Marketplace and can then be attached
to multiple machine instances. This mechanism takes advantage of the
caching and snapshotting infrastructure used for machine images. Making
the initial copy of the data image and subsequent snapshotting for
individual machine instances completely transparent to the user.

Create a Disk Image
~~~~~~~~~~~~~~~~~~~

Disk images must contain a formatted file system that can be read by the
chosen operating system for your machine images. Although this could be
any file system, for a couple of reasons the best choice is an ISO9660
(CDROM) image.

-  These images are easy to create using the ``mkisofs`` utility (or
   variants) available in most operating systems.
-  It can be universally read by all operating systems.
-  It ensures that the operating system is aware that this is a
   read-only file system.
-  Normally these volumes can be mounted and accessed by non-root users.

Using the ``mkisofs`` utility, create a disk is easy. The procedure is
just two steps: 1) create a directory containing all of the data for the
disk and 2) run ``mkisofs`` on this directory to create the CDROM image.

The only downside is that this requires enough disk space to hold the
directory with the original data and also the created CDROM image. Using
the persistent or volatile storage makes finding a big enough playground
easy.

**It is strongly recommended that you provide a label for the disk
image.** This will allow you to mount the image in a machine instance
without having to know the hardware device name within the machine
instance.

Registering the Image
~~~~~~~~~~~~~~~~~~~~~

Just like for machine images, the data images must be registered in the
Marketplace. You need to create an image metadata entry and then upload
it into the Marketplace.

Before anything else, you need to put the image on a webserver. The URL
of the image will then be used in the 'location' element of the
metadata.

Then you'll need to create the metadata itself. This can be done with
the ``stratus-build-metadata`` command in the standard way. However,
there are a few 'gotchas'. The OS, OS version, and OS architecture must
be provided; just use 'none' for these values. You should gzip the image
and use 'gz' for the compression. The 'format' of the image should be
'raw'. Lastly, the image should have a filename that ends with '.iso.gz'
after the compression.

If the disk has a label (it does right?!), then you should add this
information in the metadata description element.

Once you've created the metadata entry, sign it with
``stratus-sign-metadata`` and upload it into the Marketplace.

Using the Data Image
~~~~~~~~~~~~~~~~~~~~

Using the image itself should be straight-forward. Use
``stratus-run-instance`` as you normally would but add the
``--readonly-disk`` option with the Marketplace identifier of the data
image. An example is:

::

    $ stratus-run-instance \
        --readonly-disk=GPAUQFkojP5dMQJNdJ4qD_62mCo \
        GJ5vp8gIxhZ1w1MQF16R6MIcNoq

This disk image is a standard image ("Flora and Fauna") used for tests
of the system. It contains a hierarchy of files named after animals and
plants.

Once the machine boots, you can use the command ``blkid`` to find the
image. For this instance, the output looks like the following:

::

    $ blkid
    /dev/sda1: UUID="2fb85561-3fc4-4258-b3cf-abd8ae53d18a" TYPE="ext4" 
    /dev/sda5: UUID="ae3f7fb4-7d0e-4f6a-b91a-2f261be6a75a" TYPE="swap" 
    /dev/sr0: LABEL="_STRATUSLAB" TYPE="iso9660" 
    /dev/sdb: UUID="84e95f5f-dd31-4452-beca-2ab2cfd1bb87" TYPE="swap" 
    /dev/sdc: LABEL="CDROM" TYPE="iso9660" 

In this case, the disk we're looking for is ``/dev/sdc``. The other
CDROM image with a label '\_STRATUSLAB' is the contextualization
information.

Mount the data disk and look at the data:

::

    $ mount /dev/sdc /mnt
    mount: warning: /mnt seems to be mounted read-only.

    $ ls -l /mnt
    total 4
    dr-xr-xr-x 1 root root 2048 Jan 26 20:01 animals
    dr-xr-xr-x 1 root root 2048 Jan 26 20:01 plants

    $ cat /mnt/animals/cat.txt 
    cat

If you create your disk with a label, then the device can be mounted
without having to know the actual device ID. This makes it easier for
automated scripts to mount the disk.

Summary
~~~~~~~

Use of read-only disks is convenient for sharing fixed datasets between
multiple machine instances. Doing so reduces the size of customized
machine images and decouples the updates of the machine images from the
updates of the datasets. Because this takes advantage of the extensive
caching mechanisms of StratusLab, the transfer of such images and the
creation of disks for each machine instance is completely transparent to
the user.

Volatile Storage
----------------

In addition to persistent volumes and read only volumes, StratusLab also
offers the possibility of volatile storage. These disks are allocated
when a virtual machine starts and are destroyed automatically when the
VM terminates. Because of the volatile nature of these disks, they are
ideal for temporary storage.

Users can request a volatile disk when starting a virtual machine with
the ``--volatile-disk`` option to ``stratus-run-instance``, giving the
required size of the disk. Note that this storage is allocated on the
physical machine where the VM is running, so requesting a very large
volatile disk may reduce the number of physical machines which can host
the virtual machine.

As for the persistent volumes, these are raw devices, so the user is
responsible for partitioning and/or formatting the volumes before using
them. They also must be subsequently mounted on the VM's file system.

Again, these volumes are appropriate **only for temporary data
storage**. The data on these volumes will be destroyed when the virtual
machine is terminated.

.. [1]
   "disk" and "volume" are used interchangeably.

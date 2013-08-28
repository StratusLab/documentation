
# Storage

StratusLab provides three different types of storage: volatile,
static, and persistent volumes.

## Volatile Storage

Volatile storage consists of a volume that is allocated when a virtual
machine starts and that is destroyed automatically when the VM
terminates.  Because of the volatile nature of these volumes, they are
ideal for **temporary** storage.

### Requesting a Volatile Disk

Users can request a volatile disk when starting a virtual machine
with the `--volatile-disk` option to `stratus-run-instance`, giving
the required size of the disk in Gigabytes.

    $ stratus-run-instance \
        --volatile-disk SIZE_GB $TTYLINUX

The created storage is a raw, unformatted volume.  To find the disk,
you can use the command `fdisk -l`. 

### Use of volatile disk 

These volatile disks are raw devices, so the user is responsible for
partitioning and/or formatting the volumes before using them.  They
also must be subsequently mounted on the VM's file system.

Connect to your VM. Format, mount, and use your disk:

   1. Disks are not formatted!  Use: `mkfs.ext4 /dev/xxx`
   2. Mount disk: `mount /dev/xxx /mnt/volatile`
   3. Use normally: `touch /mnt/volatile/mydata`

If you're planning on rebooting the machine, you may want to add the
volume to the `/etc/fstab` file so that it is mounted automatically.

### Data on a Volatile Disk
 
Volatile disks are appropriate **only for temporary data storage**.
The data on these volumes will be destroyed when the virtual machine
is terminated. For VM reboots however, the disk and data will survive.

Verify this for yourself:

   1. Reboot your VM, and verify that your disk still exists, also
      verify your data on the disk.
   2. Terminate your VM. This will also destroy your disk.

Finally, note that this storage is allocated on the physical machine
where the VM is running, so requesting a very large volatile disk may
reduce the number of physical machines which can host the virtual
machine.

## Static Disk

Many scientific and engineering applications require access to fixed
(or slowly changing) data sets.  These data sets include versioned
copies of databases (e.g. protein databases) or calibration
information.  These are often inputs to calculations that analyze
larger, more varied data sets.

Read-only (static) disks allow users to create a fixed disk image
containing the data.  It is then registered in the Marketplace and can
subsequently be attached to multiple machine instances.  This
mechanism takes advantage of the caching and snapshotting
infrastructure used for machine images.  Making the initial copy of
the data image and subsequent snapshotting for individual machine
instances completely transparent to the user.

In this tutorial we will be using "Flora and Fauna" read-only disk
from the Marketplace.  The identifier is:

    GPAUQFkojP5dMQJNdJ4qD_62mCo

This disk contains a hierarchy of files named after plants and
animals.

### Run VM with Static (Read-Only) Disk

Using the image itself should be straight-forward.  Use
`stratus-run-instance` as you normally would but add the
`--readonly-disk` option with the Marketplace identifier of the data
image.  An example is:

    $ stratus-run-instance \
        --readonly-disk=GPAUQFkojP5dMQJNdJ4qD_62mCo \
        $TTYLINUX

This disk image is a standard image ("Flora and Fauna") used for tests
of the system.  It contains a hierarchy of files named after animals
and plants.  For the appliance, you can use any linux appliance.

Disks appear exactly as in reference image, formatting included.
You can use the command `blkid` to find the
image.  For this instance, the output looks like the following:

    $ blkid
    /dev/sda1: UUID="2fb85561-3fc4-4258-b3cf-abd8ae53d18a" TYPE="ext4"
    /dev/sda5: UUID="ae3f7fb4-7d0e-4f6a-b91a-2f261be6a75a" TYPE="swap"
    /dev/sr0: LABEL="_STRATUSLAB" TYPE="iso9660"
    /dev/sdb: UUID="84e95f5f-dd31-4452-beca-2ab2cfd1bb87" TYPE="swap"
    /dev/sdc: LABEL="CDROM" TYPE="iso9660"

In this case, the disk we're looking for is `/dev/sdc`.  The other
CDROM image with a label '_STRATUSLAB' is the contextualization
information.

Mount the disk and ensure that the filesystem is visible:

    $ mount /dev/sdc /mnt
    mount: warning: /mnt seems to be mounted read-only.

    $ ls -l /mnt
    total 4
    dr-xr-xr-x 1 root root 2048 Jan 26 20:01 animals
    dr-xr-xr-x 1 root root 2048 Jan 26 20:01 plants

You can then access the data on the disk as you normally would:

    $ cat /mnt/animals/dog.txt
    dog

A real disk would likely contain more interesting data!

As the name implies, data is fixed on static (read-only) disks! The
disk and its cannot be modified.  Updating the data requires creating
a new disk image and registering the new one in the Marketplace.

### Creating a Static Disk

This tutorial does not cover the creation of static disks.  However,
the basic recipe is:

  1. Create a CDROM image containing the desired file, preferably with
     a descriptive label.
  2. Make the image accessible via a web server.
  3. Register the image in the Marketplace.
  4. Reference the CDROM image when starting the machine.

The details can be found in the User's Guide.

## Persistent Volumes

Persistent Disk Service is the StratusLab service that physically
stores machine and disk images as volumes for the cloud
infrastructure.  It gives users the ability to store data
independently of any particular VM.  It also facilitates quick startup
of VMs and hot-plugging of disk volumes as block devices to the VMs.

### Create persistent disk

Before creating persistent volumes (or disks)[^1], you should make
sure that your configuration file is properly setup.  For the
StratusLab cloud at LAL, you must define both the `endpoint` and
`pdisk_endpoint` parameters.  For other sites, you may only need to
define the `endpoint` parameter.

Let's create a persistent disk of 5 GB and named "myprivate-disk".

    $ stratus-create-volume --size=5 --tag=myprivate-disk
    DISK 24f77fbd-1f26-46a5-9726-5659279843e7

The command returned the UUID of the created persistent disk.  For
convenience in the commands below, we'll define a variable:

    $ export UUID=24f77fbd-1f26-46a5-9726-5659279843e7

Substitute this value where indicated below.

Newly created disk is private (only accessible to the user) and of
type "read-write data image".  You can update the properties of the
disk via the `stratus-update-volume` command:

    stratus-update-volume --tag=my-updated-disk $UUID

Use the help option (`--help`) to see all of the properties that can
be updated. 

You can create a public persistent disk by passing the `--public`
option to `stratus-create-volume`.  However, this is a **deprecated
feature**.

### List Persistent Disks

`stratus-describe-volumes` allows you to query the list of all your
persistent volumes, and also all the public persistent disks created
by other users.

    $ stratus-describe-volumes
    :: DISK a4324f26-39e0-4965-8c8f-3287cd0936e5
            created: 2011/07/20 16:37:10
            visibility: public
            tag: mypublic-disk
            owner: testor2
            size: 5
            users: 0
    :: DISK 24f77fbd-1f26-46a5-9726-5659279843e7
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

The above command lists 'testor1' persistent volumes, and also the 
public 'testor2' ones.

Internally, the StratusLab services use the persistent disk service to
cache snapshots of VM appliances.  If you are running machines, you
will also see those snapshots listed.

### Persistent Disk Workflow

The typical workflow for using a persistent disk is the following:

  * Launch a virtual machine instance referencing a persistent disk
  * Format the disk in the running VM
  * Write data to the disk
  * Dismount the disk or halt the machine instance
  * Disk with persistent data is available for use by another VM
  * Launch another VM referencing the modified persistent disk

Note that the persistent disk should only be formatted the first time
it is used.  Formatting the disk will erase any previous data!

To demonstrate this workflow, we will use the same ttylinux appliance
identifier that we used before.

#### Launch VM with a persistent disk attached

The `--persistent-disk=UUID` option when used with
`stratus-run-instance`, tells StratusLab to attach the referenced
persistent disk(UUID) to the VM when starting the machine.

Instantiate the ttylinux appliance with reference to your private
persistent disk.

    $ stratus-run-instance --persistent-disk=$UUID $TTYLINUX

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 3)
            Public ip: 134.158.75.35

Log into your VM using ssh, depending on the linux kernel and
distribution version of your VM, your persistent disk will be
referenced as `/dev/hdc` or `/dev/sdc`.  In ttylinux, it will be
`/dev/hdc`.

Log into the machine and make sure that your disk was attached to your
VM:

    $ ssh root@134.158.75.35
    # fdisk -l
    .........
    Disk /dev/hdc: 5368 MB, 5368709120 bytes
    255 heads, 63 sectors/track, 652 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    .........

You should see a disk with the same size as the one you created.

#### Format Attached Disk

When created, the persistent volumes are unformatted.  To use it, you
will need to format it.  Use the 'ext3' or 'ext4' formatting. 

    # mkfs.ext4 /dev/hdc

You can also optionally partition the disk, which gives you the
possiblity to provide a label for the partition. 

#### Use the Disk

To use the disk, just mount it and use it normally.  When finished you
can unmount the device.

    # mount /dev/hdc /mnt
    # echo "Testing Persistent Disk" > /mnt/test_pdisk
    # umount /mnt

Once the persistent disk is unmounted and the machine has been
terminated, your persistent disk is ready to be used by another VM.

#### Reuse the Disk

Instantiate new ttylinux VM with the same reference to your private
persistent disk.

    $ stratus-run-instance --persistent-disk=$UUID $TTYLINUX

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 4)
            Public ip: 134.158.75.36

Log into your VM using ssh, verify existence of your persistent disk:

    $ ssh root@134.158.75.35
    # fdisk -l
    ...........
    Disk /dev/hdc: 5368 MB, 5368709120 bytes
    255 heads, 63 sectors/track, 652 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    ...........

Mount your persistent disk and verify that the file that was
previously saved on the other VM is still there:

    # mount /dev/hdc /mnt
    # ls /mnt
    lost+found  test_pdisk
    # cat /mnt/test_pdisk
    Testing Persistent Disk

These disks provide a mechanism to save data with a lifetime longer
than a single VM.  Keep in mind however, that like physical disks, **a
persistent disk can only be used by one machine at a time**.

#### Hot-plug Persistent Disks

StratusLab storage also provides hot-plug feature for persistent
disks. With `stratus-attach-volume` you can attach a volume to a
running machine and with `stratus-detach-volume` you can release it.

To use the hot-plug feature, the running instance needs to have
acpiphp kernel module loaded.  **The ttylinux appliance does not have
this feature.** All of the other StratusLab base appliance (Ubuntu,
CentOS, Fedora, etc.) do support this feature.

Before dynamically attaching a disk, make sure the acpiphp module is
loaded. In your VM execute:

    modprobe acpiphp

For all of the StratusLab appliances, this is done automatically.
Other appliances might need to have this done manually.

To attach two volumes to the VM ID 24 with the given UUIDs, use the
command: 

    $ stratus-attach-volume --instance=5737 $UUID
    ATTACHED 24f77fbd-1f26-46a5-9726-5659279843e7 in VM 5737 on /dev/vda

Note that the `-i` or `--instance` option is required and specifies
to which machine instance the disks should be attached.

From within the VM, use the `fdisk -l` or `blkid` command as above to
see the newly attached disks.  The devices listed in the command
response are **best guesses**.  It is always a good idea to confirm
the location of the disks.

Make sure to unmount any file systems on the device within your
operating system before detaching the volume. Failure to unmount file
systems, may corrupt the file system and lead to data loss.

    umount /dev/vda

When you finish using your disks, you can detach them from the running
VM:

    (SL)~> stratus-detach-volume --instance=5737 $UUID
    DETACHED 24f77fbd-1f26-46a5-9726-5659279843e7 from VM 5737 on /dev/vda

Trying to detach disks that were mounted with the
`stratus-run-instance` command or disks that are no longer attached
will result in an error.

Disks mounted at instance start-up will only be released after the
`stratus-kill-instance` command has been executed. 

### Delete Persistent Disks

To delete a persistent disk use the `stratus-delete-volume` command,
note that you can delete only your disks.

To delete the disk we created above:

    $ stratus-delete-volume $UUID
    DELETED 24f77fbd-1f26-46a5-9726-5659279843e7

Check that the disk is no longer there

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

Trying to delete the disk of another user ('mypublic-disk' of
'testor2') will result in an error:

    $ stratus-delete-volume a4324f26-39e0-4965-8c8f-3287cd0936e5
      [ERROR] Service error: Not enough rights to delete disk

Note that there is no confirmation required when deleting disks.  Be
sure you have the correct disk UUID before executing the command!


[^1]: "disk" and "volume" are used interchangeably.


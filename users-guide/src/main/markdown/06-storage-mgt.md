
# Storage Management

Persistent Disk Storage is the StratusLab service that physically stores 
machine and disk images as volumes on the cloud site. It facilitates quick 
startup of VMs and hot-plugging of disk volumes as block devices to the VMs.

## Create persistent disk

Before creating persistent disks (or volumes)[^1], you should define the persistent disk 
storage endpoint by either of 

  * in your HOME/.stratuslab/stratuslab-user.cfg
  * set the environment variable STRATUSLAB_PDISK_ENDPOINT
  * pass it as **_-_-pdisk-endpoint** command when creating one.

Lets create a persistent disk of 5GB and named "myprivate-disk"

    $ stratus-create-volume --size=5 --tag=myprivate-disk
    DISK 9c5a2c03-8243-4a1b-a248-0f0d22d948c2

The command returned the UUID of the created persistent disk. Newly 
created disk is by default 

  * private - can be read, written and deleted only by their owner
  * of type **read-write data image**

To create public persistent disk, pass _-_-public argument to 
stratus-create-volume

    $ stratus-create-volume --public --size=10 --tag=mypublic-disk
    DISK d955fda6-bf9c-4aa8-abc4-5bbcdb83021b

or update the disk with

    stratus-update-volume --public <UUID>

To get the list of available disk types and the properties that can be 
updated on the disk run

    stratus-update-volume -h

## List persistent disks

stratus-describe-volumes allows you to query the list of all your public and 
private persistent disks, and also all the public persistent disks created by 
other users.

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

The above command lists 'testor1' public and private persistent disks, and also 
'testor2' public ones.

## Using Persistent Disks

Workflow:

  * Launch a virtual machine instance referencing a persistent disk
  * Format the disk in the running VM
  * Write data to the disk
  * Dismount the disk or halt the machine instance
  * Disk with persistent data is available for use by another VM
  * Launch another VM referencing the modified persistent disk

### Launch VM with a persistent disk attached

To Launch a VM we will be using the ttylinux image identifier 
GOaxJFdoEXvqAm9ArJgnZ0_ky6F from StratusLab Marketplace (default one).

_-_-persistent-disk=UUID option when used with stratus-run-instance, tells 
StratusLab to attach the referenced persistent disk(UUID) to the VM.

Instantiate ttylinux image with reference to your private persistent 
disk 9c5a2c03-8243-4a1b-a248-0f0d22d948c2.

    $ stratus-run-instance \
        --persistent-disk=9c5a2c03-8243-4a1b-a248-0f0d22d948c2 \
        GOaxJFdoEXvqAm9ArJgnZ0_ky6F
    
     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 3)
            Public ip: 134.158.75.35

Log into your VM using ssh, depending in the linux kernel and distribution 
version of your VM, your persistent disk will be referenced as /dev/hdc or /dev/sdc.

In ttylinux, it will be /dev/hdc.

Make sure that your disk was attached to your VM

    $ ssh root@134.158.75.35
    # fdisk -l 
    .........
    Disk /dev/hdc: 5368 MB, 5368709120 bytes
    255 heads, 63 sectors/track, 652 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    .........

### Format attached disk

    # mkfs.ext3 /dev/hdc
    
### Mount disk, store data, unmount disk
    
    # mount /dev/hdc /mnt
    # echo "Testing Persistent Disk" > /mnt/test_pdisk
    # umount /mnt 

Your persistent disk is ready to be used by another VM.

### Launch VM with the modified persistent disk attached

Instantiate new VM ttylinux with the same reference to your private persistent 
disk 9c5a2c03-8243-4a1b-a248-0f0d22d948c2.

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

    $ ssh root@134.158.75.35
    # fdisk -l
    ...........
    Disk /dev/hdc: 5368 MB, 5368709120 bytes
    255 heads, 63 sectors/track, 652 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    ...........

Mount your persistent disk

    # mount /dev/hdc /mnt
    # ls /mnt
    lost+found  test_pdisk
    # cat /mnt/test_pdisk 
    Testing Persistent Disk

## Hot-plug Persistent Disks

StratusLab storage also provides hot-plug feature for persistent disk. With 
stratus-attach-instance you can attach a volume to a running machine and with 
stratus-detach-instance you can release it.

To use the hot-plug feature, the running instance needs to have acpiphp kernel 
module loaded. Image like ttylinux doesn't have this feature, you have to use 
base image like Ubuntu, CentOS or Fedora.

Before hot-plugin a disk, make sure acpiphp is loaded. In your VM execute

    modprobe acpiphp

To attach two volumes to the VM ID 24 with the UUIDs 
1e8e9104-681c-4269-8aae-e513c6723ac6 and  
5822c376-9ce1-434e-95d1-cdaa240cd47c

    $ stratus-attach-volume -i 24 1e8e9104-681c-4269-8aae-e513c6723ac6 5822c376-9ce1-434e-95d1-cdaa240cd47c
    ATTACHED 1e8e9104-681c-4269-8aae-e513c6723ac6 in VM 24 on /dev/vda
    ATTACHED 5822c376-9ce1-434e-95d1-cdaa240cd47c in VM 24 on /dev/vdb  

Use the fdisk -l command as above to see the newly attached disks.

Make sure to unmount any file systems on the device within your operating system 
before detaching the volume. Failure to unmount file systems, or otherwise 
properly release the device from use, can result in lost data and will corrupt 
the file system.

    umount /dev/vda
    umount /dev/vdb

When you finish using your disks, you can detach them from the running VM

    $ stratus-detach-volume -i 24 1e8e9104-681c-4269-8aae-e513c6723ac6 5822c376-9ce1-434e-95d1-cdaa240cd47c
    DETACHED 1e8e9104-681c-4269-8aae-e513c6723ac6 from VM 24 on /dev/vda
    DETACHED 5822c376-9ce1-434e-95d1-cdaa240cd47c from VM 24 on /dev/vdb

On running instance detaching not hot-plugged disks or disks that were not 
or are no longer attached to the instance will result in an error

    $ stratus-detach-volume -i 41 2a17226f-b006-45d8-930e-13fbef3c6cdc
    DISK 2a17226f-b006-45d8-930e-13fbef3c6cdc: Disk have not been hot-plugged

If you have attached the volume at instance start-up, it cannot be detached 
while the instance is in the 'Running' state. To detach the volume, stop the 
instance first.

## Delete Persistent Disks

To delete a persistent disk use the stratus-delete-volume command, 
note that you can delete only your disks.

To delete 'myprivate-disk' with UUID 9c5a2c03-8243-4a1b-a248-0f0d22d948c2

    $ stratus-delete-volume 9c5a2c03-8243-4a1b-a248-0f0d22d948c2
    DELETED 9c5a2c03-8243-4a1b-a248-0f0d22d948c2

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

Now try to delete 'mypublic-disk' of 'testor2' user persistent disk

    $ stratus-delete-volume a4324f26-39e0-4965-8c8f-3287cd0936e5
      [ERROR] Service error: Not enough rights to delete disk

[^1]: "disk" and "volume" are used interchangeably.  

[ref-infra]: /try/2012/12/04/try-reference-cloud-infrastructures.html
[user-client-install]: /try/2012/01/10/try-user-cli-installation.html

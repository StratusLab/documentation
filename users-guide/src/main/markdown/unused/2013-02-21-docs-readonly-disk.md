---
layout: article
title: Read Only Disks
category: review
---

In addition to persistent and volatile data volumes, StratusLab also
supports read-only volumes.  These come in handy when you have fixed
(or slowly changing) data sets that you would like to use with
multiple machines. 

This article explains how to create, register, and use these read-only
volumes. 


Overview
--------

Many scientific and engineering applications require access to fixed
(or slowly changing) data sets.  This includes versioned copies of
databases (e.g. protein databases) or calibration information.  These
are often inputs to calculations that analyze larger, more varied data
sets.

Persistent disk volumes and volatile disk volumes are not well-suited
for this.  The persistent disks cannot be shared between machine
instances, so multiple copies of the disk need to be maintained or the
data needs to be shared through a file server (e.g. NFS).  Use of
volatile storage would require copying the data each time a new
machine instance starts.

Read-only disks allow users to create a fixed disk image containing
the data.  It is then registered in the Marketplace and can then be
attached to multiple machine instances.  This mechanism takes
advantage of the caching and snapshotting infrastructure used for
machine images.  Making the initial copy of the data image and
subsequent snapshotting for individual machine instances completely
transparent to the user. 


Create a Disk Image
-------------------

Disk images must contain a formatted file system that can be read by
the chosen operating system for your machine images.  Although this
could be any file system, for a couple of reasons the best choice is
an ISO9660 (CDROM) image.

* These images are easy to create using the `mkisofs` utility (or
  variants) available in most operating systems.
* It can be universally read by all operating systems. 
* It ensures that the operating system is aware that this is a
  read-only file system.
* Normally these volumes can be mounted and accessed by non-root
  users. 

Using the `mkisofs` utility, create a disk is easy.  The procedure is
just two steps: 1) create a directory containing all of the data for
the disk and 2) run `mkisofs` on this directory to create the CDROM
image. 

The only downside is that this requires enough disk space to hold the
directory with the original data and also the created CDROM image.
Using the persistent or volatile storage makes finding a big enough
playground easy.

**It is strongly recommended that you provide a label for the disk
image.** This will allow you to mount the image in a machine instance
without having to know the hardware device name within the machine
instance.


Registering the Image
---------------------

Just like for machine images, the data images must be registered in
the Marketplace.  You need to create an image metadata entry and then
upload it into the Marketplace. 

Before anything else, you need to put the image on a webserver.  The
URL of the image will then be used in the 'location' element of the
metadata.

Then you'll need to create the metadata itself.  This can be done with
the `stratus-build-metadata` command in the standard way.  However,
there are a few 'gotchas'.  The OS, OS version, and OS architecture
must be provided; just use 'none' for these values.  You should gzip
the image and use 'gz' for the compression.  The 'format' of the image
should be 'raw'.  Lastly, the image should have a filename that ends
with '.iso.gz' after the compression.

If the disk has a label (it does right?!), then you should add this
information in the metadata description element.

Once you've created the metadata entry, sign it with
`stratus-sign-metadata` and upload it into the Marketplace.


Using the Data Image
--------------------

Using the image itself should be straight-forward.  Use
`stratus-run-instance` as you normally would but add the
`--readonly-disk` option with the Marketplace identifier of the data
image.  An example is:

    $ stratus-run-instance \
        --readonly-disk=GPAUQFkojP5dMQJNdJ4qD_62mCo \
        GJ5vp8gIxhZ1w1MQF16R6MIcNoq

This disk image is a standard image ("Flora and Fauna") used for tests
of the system.  It contains a hierarchy of files named after animals
and plants.

Once the machine boots, you can use the command `blkid` to find the
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

Mount the data disk and look at the data:

    $ mount /dev/sdc /mnt
    mount: warning: /mnt seems to be mounted read-only.

    $ ls -l /mnt
    total 4
    dr-xr-xr-x 1 root root 2048 Jan 26 20:01 animals
    dr-xr-xr-x 1 root root 2048 Jan 26 20:01 plants

    $ cat /mnt/animals/cat.txt 
    cat

If you create your disk with a label, then the device can be mounted
without having to know the actual device ID.  This makes it easier for
automated scripts to mount the disk.

Conclusions
-----------

Use of read-only disks is convenient for sharing fixed datasets
between multiple machine instances.  Doing so reduces the size of
customized machine images and decouples the updates of the machine
images from the updates of the datasets.  Because this takes advantage
of the extensive caching mechanisms of StratusLab, the transfer of
such images and the creation of disks for each machine instance is
completely transparent to the user. 

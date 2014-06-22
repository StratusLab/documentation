
Persistent Storage
==================

Raw compute power without persistent storage only supports a limited
range of applications.  StratusLab like most cloud software also
provides services for managing persistent storage.

For StratusLab, persistent storage is in the form of raw block devices
(volumes). These volumes can be used in conjunction with virtual
machines, but importantly have a lifecycle that is independent of
them.

Raw block devices behave similarly to physical hard drives.  In
particular, they have some of the same limitations:

-  They are initially empty and unformatted; they must be initialized
   when first used.
-  They can only be attached on one virtual machine at a time; they
   must be detached from one machine before being attached to
   another. 

StratusLab does not natively provide file or object-based storage
services.

Lifecycle
---------

The primary commands for a persistent disks are::

    $ stratus-create-volume --size ${GiB} --tag ${MY_DESC}
    $ stratus-describe-volumes ${VOLUME_UUID}
    $ stratus-update-volume [opts] ${VOLUME_UUID}
    $ stratus-delete-volume ${VOLUME_UUID}

These commands create, describe (list), update metadata, and delete
persistent disks, respectively.

The ``stratus-create-volume`` command takes the size of the volume in
Gibibytes (GiB) and an optional tag.  The tag is useful for
remembering the contents of a given volume.  A tag be added later with
the ``stratus-update-volume`` command.  This command returns the UUID
of the created disk.

The ``stratus-describe-volumes`` command provides a list of persistent
disks if no argument is provided.  If you provide an argument, then
the details for the given disk are provided.  The ``--filter`` option
is useful for limiting the number of disks returned in the list.

.. note::

   The StratusLab caching mechanism for virtual machine appliances
   uses the persistent disk infrastructure.  When virtual machines are
   running you will see "snapshot" volumes corresponding to the root
   volumes for those machines. 

The ``stratus-delete-volume`` deletes a given volume.  This releases
the allocated storage space and **permanently deletes any stored
data**.

.. warning::

   Once a persistent disk has been deleted, all of the data on the
   disk is deleted and **cannot be recovered**.  Be certain that you
   reference the correct disk before deleting it!

.. admonition:: Exercise

   Run through the entire persistent disk lifecycle with the command
   line interface.  Try to change the tag associated with the disk
   after you've created it.

.. admonition:: Exercise

   Create several (small) disks with different tags and sizes.  Use
   the filtering to limit the information returned by the
   ``stratus-describe-volumes`` command.

Web Interface
-------------

There is also a web interface for this service which allows you to
perform the same operations as you can from the command line.  (It
also allows you to see the current mount status of a disk which the
command line client can't do.)

You can discover the location of the web interface by pointing a
browser at the URL for the "pdisk_endpoint" in your configuration (or
at the "endpoint" value if "pdisk_endpoint" isn't defined).  Look for
the "svc-pdisk.html" file in the listing. 

.. warning::

   That the web interface will change significantly in the coming
   releases.

.. admonition:: Exercise

   Run through the entire persistent disk lifecycle though the web
   interface.  Modify the tag (or add one) to an existing persistent
   disk. 

Using Ceph
==========

`Ceph <http://ceph.com>`__ is a set of storage technologies that allow
object, block, and file storage that is reliable and scalable while
maintaining high-performance.

The block storage component of Ceph can be used as a storage backend to
the persistent disk service.

Requirements
------------

-  A working Ceph cluster
-  A Ceph pool for pdisk
-  A a manager identity for this pool

To create the pool: ceph osd pool create ${poolname} 128 128

To create the manager identity for the pool:

::

    ceph auth get-or-create client.${identity} \
      mon 'allow r' osd \
      'allow class-read object_prefix rbd_children, allow rwx pool=${poolname}'

Modifications
-------------

Some modifications are needed for the persistent disk server and the VM
hosts.

-  Linux kernel 3.x
-  Ceph package
-  Ceph configuration in ``/etc/ceph/ceph.conf``
-  Manager keyring in ``/etc/ceph/ceph.client.${identity}.keyring``
-  Parameters in ``pdisk-backend.cfg`` and ``pdisk-host.conf``

For testing, you can use a package provided by `ELrepo
repository <http://elrepo.org/tiki/tiki-index.php>`__ which provides a
3.x kernel for CentOS v6.4.

Currently, using a proxy server is not very useful for this backend
because the persistent disk server needs a 3.x kernel and the Ceph
package to attach pdisks to itself when importing a remote appliance
(cf. DiskUtils.copyUrlToVolume).

There is not automatic configuration via the StratusLab installer yet.
This is being worked on.

The ``oneadmin`` user must also be able to execute Ceph commands as
root. Update the ``/etc/sudoers`` file appropriately:

::

    # /etc/sudoers
    oneadmin ALL= NOPASSWD: /usr/bin/rbd


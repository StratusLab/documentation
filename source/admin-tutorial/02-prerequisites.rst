
Prerequisites
=============

This tutorial demonstrates a minimal installation of a StratusLab cloud
on two physical machines.  Before trying to install the StratusLab
software there are many prerequisites that must be satisfied by the
hardware, operating system, and software. 

Physical Machines
-----------------

The physical machines should be relatively modern machines with the
following **minimum** characteristics:

-  1 **64-bit multicore** CPU (>= 4 cores)
-  4 GB of RAM
-  200 GB local disk space

In general cloud infrastructures prefer "fat" machines; that is
machines that have the maximum number of CPUs, RAM, and disk space as
possible.  This is because the maximum size of a single virtual
machine is limited by the size of the largest physical machine.

Virtualization Support
----------------------

The machine must have a CPU that supports the VT-x virtualization
extensions and must have this support enabled in the BIOS.  **Many
vendors ship machines with these extensions disabled.**

RedHat has a `good document
<http://virt-tools.org/learning/check-hardware-virt/>`__ for checking
if your machine will support virtualization.  In short, check the
`/proc/cpuinfo` for the proper flags::

    $ grep vmx /proc/cpuinfo 

and check the `dmesg` console for any KVM errors when trying to load
the KVM kernel module::

    $ dmesg | grep kvm

looking for any messages about KVM not supporting virtualisation or
having it disabled in BIOS.

Operating System
----------------

Be sure to read all of the operating system requirements before
installing your machines.  This will save you from having to do
the installation multiple times!

Supported Versions
~~~~~~~~~~~~~~~~~~

Install a minimal version of `CentOS 6 <http://www.centos.org>`__ on the
two physical machines that will be used for the cloud infrastructure.
Other distributions that are compatible with CentOS 6 will likely
work; however, only CentOS 6 is systematically tested.

Operating Systems in the Debian family (e.g. Ubuntu) are not currently
supported. 

Disable SELinux
~~~~~~~~~~~~~~~

The SELinux system must be disabled on **all of the machines** in the
cloud infrastructure.  The standard CentOS installation procedure
normally activates SELinux.

To disable SELinux, ensure that the file ``/etc/selinux/config`` has
the following line::

    SELINUX=disabled

You can also use the commands ``getenforce`` and ``sestatus`` to find
the current SELinux status.  Changes in this configuration are only
taken into account after **rebooting the machine**. 

You can reboot the machine to have your changes taken into account or
disable SELinux temporarily::

    $ echo 0 > /selinux/enforce

This change will be lost at the next reboot unless you have also
changed the ``/etc/selinux/config`` configuration file.
    
Disk Configuration
------------------

StratusLab allows for a variety of storage options behind the persistent
disk service.

This tutorial uses the default storage solution using LVM and iSCSI.
Because of this, the machines **must be configured to use LVM for the
disk storage.**

The Front End will host the storage service and the physical storage
associated with it.  It is strongly recommended that the Front End
machine be configured with **two LVM groups**: one for the base
operating system (~20 GB) and one for the StratusLab storage service
(remaining space).  One LVM group is possible, but you risk starving
normal system services of disk space.

In the tutorial, we assume that the volume group names are "vg.01" for
the operating system and "vg.02" for the StratusLab storage service on
the Front End.  You can use other names, but change the commands and
configuration parameters below as necessary.

The "Node" machine can be configured with a single LVM group.

You can see the configured volume groups with the command::

    $ vgdisplay

     --- Volume group ---
     VG Name               rootvg
     System ID             
     Format                lvm2
     ...

You'll need the volume group name "VG Name" of the volume group you'll
be using for the cloud storage.  Verify that the device associated
with the group name exists.  Here for example, the device is
`/dev/rootvg`.

Software
--------

Python Version
~~~~~~~~~~~~~~

The default version of Python installed with CentOS should be correct.
Verify that the correct version of Python is installed::

    $ python --version
    Python 2.6.6

StratusLab requires a version of Python 2 with a version **2.6 or
later**. The StratusLab command line tools **do not work with Python
3**.

Package Repositories
~~~~~~~~~~~~~~~~~~~~

The StratusLab installation takes packages from four yum repositories:

1. The standard CentOS repository,
2. The `EPEL 6 <http://fedoraproject.org/wiki/EPEL>`__ repository,
3. The `StratusLab repository <http://yum.stratuslab.eu>`__, and
4. The `IGTF Root
   Certificates <http://repository.egi.eu/sw/production/cas/1/current/>`__.

The configuration for the CentOS repository is done when the system is
installed and the IGTF repository will be configured by the StratusLab
tools as necessary.  The others require explicit configuration. 

The directory `/etc/yum.repos.d` contains the currently configured yum
repositories.

EPEL Repository
^^^^^^^^^^^^^^^

Configure **both** the Front End and Node for the EPEL repository.  Do
the following::

    $ wget -nd http://mirrors.ircam.fr/pub/fedora/epel/6/i386/epel-release-6-8.noarch.rpm 
    $ yum install -y epel-release-6-8.noarch.rpm

This will add the necessary files to the ``/etc/yum.repos.d/``
directory.  You can find the latest version of the EPEL configuration
RPM on the EPEL wiki.

StratusLab Repository
^^^^^^^^^^^^^^^^^^^^^

To configure **both** the Front End and Node for the StratusLab
repository, put the following into the file
``/etc/yum.repos.d/stratuslab.repo``::

    [StratusLab-Releases]
    name=StratusLab-Releases
    baseurl=http://yum.stratuslab.eu/releases/centos-6-v14.06.0_RC5/
    gpgcheck=0

replacing the URL with the version you want to install.

Cleanup and Upgrade
^^^^^^^^^^^^^^^^^^^

Although not strictly necessary, it is advisable to clear all of the yum
caches and upgrade the packages to the latest versions::

    $ yum clean all
    $ yum upgrade -y

This may take some time if you installed the base operating system from
old media.

Network Setup
-------------

DNS and Hostname
~~~~~~~~~~~~~~~~

Ensure that the **hostname** is properly setup on the Front End and the
Node. The DNS must provide both the forward and reverse naming of the
nodes.  For example::

    $ host cloud.lal.stratuslab.eu 
    cloud.lal.stratuslab.eu is an alias for onehost-4.lal.in2p3.fr.
    onehost-4.lal.in2p3.fr has address 134.158.75.4

    $ host 134.158.75.4 
    4.75.158.134.in-addr.arpa domain name pointer onehost-4.lal.in2p3.fr.

Ensure that the host resolves to an IP address and that the IP address
resolves back to the original name (or alias). 

Also ensure that the hostname is properly set for the node::

    $ hostname -f

should return the full hostname (with domain).  Set the hostname if it
is not correct.

Throughout this tutorial we use the variables $FRONTEND\_HOST
($FRONTEND\_IP) and $NODE\_HOST ($NODE\_IP) for the Front End and Node
hostnames (IP addresses), respectively. Change these to the proper names
for your physical machines when running the commands.

DHCP Server
~~~~~~~~~~~

**You must have a range of free IP addresses that can be assigned to
virtual machines.** The range should be large enough to handle the
maximum number of virtual machines you expect to have running
simultaneously on your infrastructure.

These IP addresses must be publicly visible if the cloud instances are
to be accessible from the internet.

In addition, a DHCP server must be configured to assign static IP
addresses corresponding to known MAC addresses for the virtual
machines.  You can use an external DHCP server or if one is not
available (or not desired), the StratusLab installation command can be
used to properly configure a DHCP server on the Front End for the
virtual machines.

This tutorial will start a DHCP server on the Front End by default.

Network Bridge
~~~~~~~~~~~~~~

A network bridge must be configured on the Node to allow virtual
machines access to the internet. You can do this manually if you want,
but the StratusLab installation scripts are capable of configuring this
automatically.

This tutorial uses the installation scripts to configure the network
bridge.

SSH Configuration
-----------------

The installation scripts will automate most of the work, but the scripts
require **password-less root access**:

-  From the Front End to each Node and
-  From the Front End to the Front End itself

Check to see if there is already an SSH key pair in
``/root/.ssh/id_rsa*``. If not, then you need to create a new key pair
**without a password**::

    $ ssh-keygen -q 
    Enter file in which to save the key (/root/.ssh/id_rsa): 
    /root/.ssh/id_rsa already exists.
    Overwrite (y/n)? y
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 

Now do the necessary configuration to ensure that you can log into the
Front End from the Front End with your SSH key (and without a
password). Do the following::

    $ ssh-copy-id $FRONTEND_HOST
    The authenticity of host 'onehost-5.lal.in2p3.fr (134.158.75.5)' can't be established.
    RSA key fingerprint is e9:04:03:02:e5:2e:f9:a1:0e:ae:9f:9f:e4:3f:70:dd.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'onehost-5.lal.in2p3.fr,134.158.75.5' (RSA) to the list of known hosts.
    root@onehost-5.lal.in2p3.fr's password: 
    Now try logging into the machine, with "ssh 'onehost-5.lal.in2p3.fr'", and check in:

      .ssh/authorized_keys

    to make sure we haven't added extra keys that you weren't
    expecting.

and then the same thing for the node::

    $ ssh-copy-id $NODE_HOST
    ...

After these commands you key should have been added to the
`authorized_key` file on both nodes and should allow you to log in
without a password.

.. note::

   If you machine does not have the `ssh-copy-id` command, then you
   will have to do the configuration by hand.  Append the contents of
   your `$HOME/.ssh/id_rsa.pub` file to the
   `$HOME/.ssh/authorized_keys` file on both the Front End and the
   Node.  You will also have to accept the host's SSH key the first
   time you log in.

Verify that the password-less access works as expected.

::

    $ ssh $FRONTEND_HOST 

    Last login: Mon May 27 14:26:29 2013 from mac-91100.lal.in2p3.fr
    # 
    # exit
    logout
    Connection to onehost-5.lal.in2p3.fr closed.

    $ ssh $NODE_HOST

    Last login: Mon May 27 14:26:43 2013 from mac-91100.lal.in2p3.fr
    # 
    # exit
    logout
    Connection to onehost-6.lal.in2p3.fr closed.

Now that SSH is properly configured, the StratusLab scripts will be able
to install software on both the Front End and the Node.

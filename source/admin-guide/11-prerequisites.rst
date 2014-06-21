Prerequisites
=============

Physical Machines
-----------------

This tutorial demonstrates a minimal installation of a StratusLab cloud
on two physical machines. The physical machines should be relatively
modern machines with the following **minimum** characteristics:

-  1 **64-bit multicore** CPU (>= 4 cores) with **VT-x extensions**
-  4 GB of RAM
-  200 GB local disk space

**The hardware virtualization extensions must be enabled in the BIOS on
the "Node" machine.** Many vendors ship machines with these extensions
disabled.

In general cloud infrastructures prefer "fat" machines, that is machines
that have a maximum number of CPUs, RAM, and disk space as possible.
This is because the maximum size of a single virtual machine is limited
by the size of the largest physical machine.

Operating System
----------------

Install a minimal version of [CentOS 6][centos] on the two physical
machines that will be used for the cloud infrastructure.

Disable SELinux
---------------

The SELinux system must be disabled on **all of the machines**. Normally
this is enabled by default. To disable SELinux, ensure that the file
``/etc/selinux/config`` has the following line:

::

    SELINUX=disabled

**You must reboot the machine for this to take effect.**

Python Version
--------------

The default version of Python installed with CentOS should be correct.
StratusLab requires a version of Python 2 with a version **2.6 or
later**. The StratusLab command line tools **do not work with Python
3**.

Verify that the correct version of Python is installed:

::

    $ python --version
    Python 2.6.6

Disk Configuration
------------------

StratusLab allows for a variety of storage options behind the persistent
disk service. The tutorial uses the defaults using LVM and iSCSI.

**The machines must be configured to use LVM for the disk storage.**

The Front End must be configured with two LVM groups: one for the base
operating system (~20 GB) and one for the StratusLab storage service
(remaining space).

The "Node" machine can be configured with a single LVM group.

Below, we assume that the volume group names are "vg.01" for the
operating system and "vg.02" for the StratusLab storage service. You can
use other names, but then change the commands below as necessary.

Package Repositories
--------------------

The StratusLab installation takes packages from four yum repositories:

1. The standard CentOS repository,
2. The [EPEL 6][epel] repository,
3. The [StratusLab repository][stratuslab-yum], and
4. The [IGTF Root Certificates][igtf-certs].

The configuration for the CentOS repository is done when the system is
installed. The others require additional configuration.

To configure **both** the Front End and Node for the EPEL repository, do
the following:

::

    $ wget -nd http://mirrors.ircam.fr/pub/fedora/epel/6/i386/epel-release-6-8.noarch.rpm 
    $ yum install -y epel-release-6-8.noarch.rpm

This will add the necessary files to the ``/etc/yum.repos.d/``
directory.

To configure **both** the Front End and Node for the StratusLab
repository, put the following into the file
``/etc/yum.repos.d/stratuslab.repo``:

::

    [StratusLab-Releases]
    name=StratusLab-Releases
    baseurl=http://yum.stratuslab.eu/releases/centos-6.2-v13.02/
    gpgcheck=0

replacing the URL with the version you want to install.

Although not strictly necessary, it is advisable to clear all of the yum
caches and upgrade the packages to the latest versions:

::

    $ yum clean all
    $ yum upgrade -y

This may take some time if you installed the base operating system from
old media.

DNS and Hostname
----------------

Ensure that the **hostname** is properly setup on the Front End and the
Node. The DNS must provide both the forward and reverse naming of the
nodes. This is required for critical services to start.

You can verify this on both the Front End and the Node with the command:

::

    $ hostname -f

Set the hostname if it is not correct.

Throughout this tutorial we use the variables $FRONTEND\_HOST
($FRONTEND\_IP) and $NODE\_HOST ($NODE\_IP) for the Front End and Node
hostnames (IP addresses), respectively. Change these to the proper names
for your physical machines when running the commands.

SSH Configuration
-----------------

The installation scripts will automate most of the work, but the scripts
require **password-less root access**:

-  From the Front End to each Node and
-  From the Front End to the Front End itself

Check to see if there is already an SSH key pair in
``/root/.ssh/id_rsa*``. If not, then you need to create a new key pair
**without a password**:

::

    $ ssh-keygen -q 
    Enter file in which to save the key (/root/.ssh/id_rsa): 
    /root/.ssh/id_rsa already exists.
    Overwrite (y/n)? y
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 

Now ensure that you can log into the Front End from the Front End
without needing a password. Do the following:

::

    $ ssh-copy-id $FRONTEND_HOST
    The authenticity of host 'onehost-5.lal.in2p3.fr (134.158.75.5)' can't be established.
    RSA key fingerprint is e9:04:03:02:e5:2e:f9:a1:0e:ae:9f:9f:e4:3f:70:dd.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'onehost-5.lal.in2p3.fr,134.158.75.5' (RSA) to the list of known hosts.
    root@onehost-5.lal.in2p3.fr's password: 
    Now try logging into the machine, with "ssh 'onehost-5.lal.in2p3.fr'", and check in:

      .ssh/authorized_keys

    to make sure we haven't added extra keys that you weren't expecting.

Do the same thing for the node:

::

    $ ssh-copy-id $NODE_HOST
    ...

And verify that the password-less access works as expected.

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

DHCP Server
-----------

A DHCP server must be configured to assign static IP addresses
corresponding to known MAC addresses for the virtual machines. These IP
addresses must be publicly visible if the cloud instances are to be
accessible from the internet.

If an external DHCP server is not available, the StratusLab installation
command can be used to properly configure a DHCP server on the Front End
for the virtual machines.

This uses a DHCP server on the Front End.

Network Bridge
--------------

A network bridge must be configured on the Node to allow virtual
machines access to the internet. You can do this manually if you want,
but the StratusLab installation scripts are capable of configuring this
automatically.

This tutorial allows the installation scripts to configure the network
bridge.

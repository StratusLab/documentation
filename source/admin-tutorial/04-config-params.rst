
Configuration Parameters
========================

Principles
----------

The entire StratusLab Cloud is configured from a single configuration
file ``/etc/stratuslab/stratuslab.cfg``. This file contains many
options, but only a few are required.

StratusLab ships with a default configuration file in the standard
location and a reference configuration file located in
``/etc/stratuslab/stratuslab.cfg.ref``.

To simplify viewing the configuration parameters and changing them, the
``stratus-config`` command can be used.

To list the content of the configuration, and show the differences
between the ``stratuslab.cfg`` file and the reference configuration, you
can use the ``-k`` or ``--keys`` option::

    $ stratus-config -k

    ... lots of parameter values! ...

To change a value, specify the key and the new value. To view a single
value, simply specify the key.

We will use this command to set the various configuration parameters
below.

VM Management Service
---------------------

The parameters for the frontend and VM management::

    $ stratus-config frontend_system centos
    $ stratus-config frontend_ip $FRONTEND_IP

Storage Service
---------------

Similar parameters must also be set for the Persistent Disk service.

For this tutorial, this service is installed on the Front End, so the
same IP address should be used.

::

    $ stratus-config persistent_disk_system centos
    $ stratus-config persistent_disk_ip $FRONTEND_IP
    $ stratus-config persistent_disk_merge_auth_with_proxy True 

The Persistent Disk service and the Nodes communicate using a strategy
defined by the ``persistent_disk_storage`` and ``persistent_disk_share``
parameters. The default values ("lvm" and "iscsi", respectively) will be
used for this tutorial.

One needs to specify what device will be used for the physical storage
for the Persistent Disk service::

    $ stratus-config persistent_disk_lvm_device /dev/vg.02

    # Provide detailed parameters for storage backend plugins.
    # (NOTE: The opening and closing single quotes!)
    $ stratus-config persistent_disk_backend_sections '
    [%(persistent_disk_ip)s]
            type=LVM
            volume_name = /dev/vg.02
            lun_namespace = stratuslab
            volume_snapshot_prefix = pdisk_clone
            initiator_group =
    '

If you've used another name for the LVM volume group, then change the
above command.

Network configuration
---------------------

Use the frontend as the general gateway for the cloud::

    $ stratus-config default_gateway $FRONTEND_IP

Set the IP and mac addresses for virtual machines::

    $ stratus-config one_public_network_addr \
        134.158.xx.yy 134.158.xx.yy 134.158.xx.yy

    $ stratus-config one_public_network_mac \
        0a:0a:86:9e:49:2a 0a:0a:86:9e:49:2b 0a:0a:86:9e:49:2c

In this example, the Front-End is configured on IP address $FRONTEND\_IP
and three IP/MAC address pairs are defined for virtual machines.

**You must use the real values for the Front End IP addresses and for
the range of addresses you will use for the virtual machines.**

More network parameters are described in the "one-network" section in
the reference configuration file.

DHCP Configuration
------------------

Allow the script to automatically configure and start the DHCP server on
the Front End. Do the following::

    $ stratus-config dhcp True
    $ stratus-config dhcp_subnet 134.158.75.0
    $ stratus-config dhcp_netmask 255.255.255.0
    $ stratus-config dhcp_lease_time 3600

    $ stratus-config dhcp_one_public_network True
    $ stratus-config dhcp_one_local_network_routers $FRONTEND_IP
    $ stratus-config dhcp_one_local_network_domain_name lal.in2p3.fr
    $ stratus-config dhcp_one_local_network_domain_name_servers \
         134.158.91.80, 134.158.88.149

Use **your** values for these parameters!

Finalize Front End Installation
-------------------------------

Now that we have defined all of the configuration parameters, you can
now do the full Front End installation by issuing the following
command::

    $ stratus-install -vv

To get more details on what the command is (because of curiosity or
errors), use the option ``-v``, ``-vv``, or ``-vvv``.

If you run into errors, the ``stratus-install`` command can simply be
rerun after adjusting the configuration parameters.

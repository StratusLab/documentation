
Configuration Parameters
========================

There is a fair number of cloud services and a large number of
associated parameters.  Fortunately, the default values for many of
these services will work fine, so only a limited number of parameters
actually need to be set.

This section describes the parameters that need to be set by service.

Front End Parameter
-------------------

For the Front End, you need only set the value of its IP address:: 

    $ stratus-config frontend_ip $FRONTEND_IP

This must be set to the real IP address of the node.  The localhost
value 127.0.0.1 will not produce a working system. 

Storage Service
---------------

Similar parameters must also be set for the Persistent Disk service.

For this tutorial, this service is installed on the Front End, so the
same IP address should be used.

::

    $ stratus-config persistent_disk_system centos
    $ stratus-config persistent_disk_ip $FRONTEND_IP
    $ stratus-config persistent_disk_port 443
    $ stratus-config persistent_disk_path pdisk
    $ stratus-config persistent_disk_merge_auth_with_proxy True 

The Persistent Disk service and the Nodes communicate using a strategy
defined by the ``persistent_disk_storage`` and ``persistent_disk_share``
parameters. The default values ("lvm" and "iscsi", respectively) will be
used for this tutorial.

One needs to specify what device will be used for the physical storage
for the Persistent Disk service::

    $ stratus-config persistent_disk_lvm_device /dev/vg.02

    # *** NOTE: Copy the following exactly.  Be careful of ***
    # *** the opening and closing single quotes!           ***
    $ stratus-config persistent_disk_backend_sections '
    [%(persistent_disk_ip)s]
            type=LVM
            volume_name = /dev/vg.02
            lun_namespace = stratuslab
            volume_snapshot_prefix = pdisk_clone
            initiator_group =
    '

.. warning::

   If your LVM volume group name is not "vg.02", then change the
   values in the above commands.

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
the range of addresses you will use for the virtual machines.**  The
mac addresses are arbitrary, but it is a good idea to have the last
four fields match the IPv4 network address.  

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

Review Parameters
-----------------

Do a final review of all of the service parameters to make sure that
they are correct.  If everything looks good, you're ready to do the
Front End installation.

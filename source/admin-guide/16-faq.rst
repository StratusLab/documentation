Frequently Asked Questions
==========================

This section collects a number of frequently asked questions
concerning the administration of a StratusLab cloud infrastructure.
If there are other questions that you would like to see treated here,
please contact StratusLab support.

How to restart VMs after a power outage?
----------------------------------------

After the power outage the nodes hosting the virtual machines must be
restarted.  If this has not happened automatically, ensure that all of
the nodes have rebooted.

Connect to each of the nodes to ensure that all of the iSCSI mounts
are present.

::

    # iscsiadm -m session

If the links are not correctly mounted (bad links will blink in the
list), then the easiest way to recover this is simply to reboot the
machine again. 

Once you have verified all of the iSCSI links on all of the nodes,
then you must log into the frontend machine as `oneadmin`.  As this
user, you can then force OpenNebula to restart all of the machines in
the `unknown` state.

The commands for this (assuming that you're logged into the frontend
as `root`), are:

::

    # su - oneadmin
    $ for vm in `onevm list all |grep unk | awk ‘{print $1}’`; \
        do; \
          onevm restart $vm; \
        done 

After this, all of the VMs should return to the `running` state and
again be accessible from the network.  Any machines that don't restart
are probably lost and will have to be killed and a new instance
created.

How to support advanced CPU flags in virtual machines?
------------------------------------------------------

By default, libvirt and KVM provide a generic CPU processor to virtual
machines.  For computing intensive applications, access to more
advanced CPU capabilities (CPU "flags") can make an enormous
difference in the efficiency of a calculation.  Consequently, the
generic CPU profile provided by libvirt and KVM is inadequate for
these applications.

Most modern processors have the advanced CPU capabilities that
numerical applications need.  The only problem is how to get libvirt
and KVM to expose these capabilities to the hosted virtual machines?

Unfortunately, libvirt does not provide a mechanism for passing
arbitrary options to the underlying hypervisor.  However, the call to
the hypervisor can be wrapped to provide the additional options.

Create a wrapper script for the call to `qemu` that will add the
`-cpu` option:

::

    #!/bin/bash
    /usr/libexec/qemu-kvm -cpu host $@

This additional option will allow the host CPU specification to be
passed directly to virtual machines, allowing all of the advanced
capabilities of the host processor to be used.

.. warning::

    The wrapper script must be installed on all of the nodes of the
    StratusLab cloud infrastructure.  This script is named
    `/var/lib/one/bin/qemu-start.sh` below; change this value as
    necessary.

Once the wrapper script is installed, then the OpenNebula
configuration must be changed to force this script to be used to start
virtual machines.  Modify the configuration files
`/etc/one/vmm_exec/vmm_exec_kvm*.conf` setting the value of the
`EMULATOR` parameters::

    EMULATOR = /var/lib/one/bin/qemu-start.sh

Change the value to match the wrapper script that you have created.
Once this is done, restart OpenNebula.  Execute::

    $ service oned restart

on the frontend node of your StratusLab cloud infrastructure.

Once this is done, any new virtual machines will use the wrapper
script and will have the full CPU capabilities of the host
processor. You can verify the CPU capabilities from the virtual
machine with the command::

    $ cat /proc/cpuinfo

The relevant capabilities are in the "flags" part of the response. 



    

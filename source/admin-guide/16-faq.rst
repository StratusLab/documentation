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


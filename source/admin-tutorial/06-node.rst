
Node Installation
=================

The deployment of the StratusLab Nodes is done from the Front End,
thus, **all the commands below should be run from the Front End.**

To add a Node to the cloud, specify the Linux distribution of the
machine and indicate that the bridge should be configured::

    $ stratus-config node_system centos

Request the automatic configuration of the network bridge::

    $ stratus-config node_bridge_configure True
    $ stratus-config node_bridge_name br0
    $ stratus-config node_network_interface eth0

.. warning::

   Check carefully the name of the interface on the node!  Using the
   wrong interface will cause the network to be misconfigured and you
   will likely lose remote access to the machine.

Invoke installation with::

    $ stratus-install -vv -n $NODE_IP 2>&1 | tee node-install.log

As before, you can increase the verbosity level by adding the option
``-v`` or ``-vv``.

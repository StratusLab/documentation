
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

Check carefully the name of the interface on the node!

Invoke installation with::

    $ stratus-install -vv -n $NODE_IP

As before, you can increase the verbosity level by adding the option
``-v`` or ``-vv``.


Administrator CLI
=================

The first step is to install the StratusLab system administrator command
line client from the `StratusLab
repository <http://yum.stratuslab.eu>`__ **on the Front End**::

    $ yum install -y stratuslab-cli-sysadmin

This will install the system administrator client and all of the
necessary dependencies. You can verify that it is correctly installed by
doing the following::

    $ stratus-config --help

    Usage: stratus-config [options] [key [value]]
    If the [value] is not provided, the command returns the current value
    of the key.
    ...

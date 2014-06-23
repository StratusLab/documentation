
Administrator CLI
=================

The StratusLab administrator command line client will help define
parameters and perform the installation.

Installation
------------

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

Service Configuration
---------------------

The command `stratus-config` will help you view and set the various
parameters for the StratusLab installation.  This is essentially an
interface to the configuration file `/etc/stratuslab/stratuslab.cfg`
and the associated reference file
`/etc/stratuslab/stratuslab.cfg.ref`.

To list all of the defined parameters (keys) and their values use the
command:: 

    $ stratus-config --keys 
    ...

This will display a long list of keys, their current values, and their
default values, by section.  You can view a single parameter by naming
the parameter::

   $ stratus-config frontend_system

and set it by giving a value::

   $ stratus-config frontend_system centos

Each service has a parameter to determine whether the service will be
installed.  For example, the registration service::

   $ stratus-config registration
   False

is not installed by default.  The values that should be set will be
discussed in the following sections. 

Service Installation
--------------------

Once all of the service configuration parameters have been set, you
can use the ``stratus-install`` command to perform the installation.
This command should always be executed on the Front End.  It will use
SSH to access the machines (including the Front End!) where the
services will be installed.

Normally, if errors occur in the installation, you can simply correct
the configuration and rerun ``stratus-install``. 

This command is designed to simplify the initial installation of the
cloud.  It can be used also to update the cloud software, although
there are some significant limitations when doing this.  Notably there
are certain parts of the configuration (like the address ranges) that
cannot be updated automatically. 

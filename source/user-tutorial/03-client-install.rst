Command Line Client
===================

The StratusLab command line client is written primarily in Python,
although there is a small part related to XML cryptographic signatures
that is written in Java.

Installation
------------

End-users should use the Python package installer (``pip``) to install
the client on their machines.  Do the following to install the client
and all of the required dependencies::

    $ pip install stratuslab-client
    Downloading/unpacking stratuslab-client
    ...
    Successfully installed stratuslab-client dirq httplib2 requests
    Cleaning up...

The command should end with a message saying that the installation was
successful. By default, ``pip`` will install the software in the
system area to make it available for all users on the system.

.. note::

   If you want to install a beta version or release candidate, then
   for newer versions of ``pip`` you will have to use the ``--pre``
   option to allow pre-release versions to be installed.

.. warning::

   On Mac OS X with the latest version of ``pip``, the system-wide
   installation will not work correctly because of a change in where
   data files are placed.  A user-level or ``virtualenv`` installation
   is recommended to work around this problem.

If you don't want a system-wide installation of the client, you can do
a user-level installation using the option ``--user``.  However in
this case, you will need to modify your environment, adding for
example, the appropriate directories to your PATH::

    PATH=$PATH:$HOME/.local/bin

Adjust the command appropriately for your operating system and command
shell.

Configuration
-------------

You must configure the command line client, providing the service
endpoints for the StratusLab cloud that you want to use and your
credentials for that cloud.

Create your configuration file by running the following command::

    $ stratus-copy-config

If successful, this will create the file
``$HOME/.stratuslab/stratuslab-user.cfg``. You will now need to edit
this file to provide the required information. The file itself is
extensively commented, showing all of the available options.

Your edited file should be similar to the following::

    [default]

    selected_section = sl-cloud

    endpoint_timeout = 5

    [sl-cloud]

    name = "StratusLab Cloud"
    country = "France"

    endpoint = https://cloud.lal.stratuslab.eu/one-proxy/xmlrpc
    pdisk_endpoint = https://pdisk.lal.stratuslab.eu/pdisk
    marketplace_endpoint = https://marketplace.stratuslab.eu/marketplace

    username = ....
    password = ....

Obviously, you will need to replace the username and password values
with your actual credentials.  You will also need to replace the
endpoint values for the cloud you are using.  The endpoints given here
are for the StratusLab reference infrastructure.

.. note::

   If your password (or any other value) contains a percent sign (%),
   you **must** double that character in the configuration file. This
   is because the Python software used to read the configuration files
   allows variable substitution with the syntax ``%(variable)``.

Testing
-------

To test that the client is installed correctly and that you can
contact the cloud services, run the following commands::

    $ stratus-describe-instance
    id  state     vcpu memory    cpu% host/ip                 name

    $ stratus-describe-volumes
    No disk to show

These should return an empty list of virtual machines and of volumes,
respectively.  Any error indicates a problem with the installation or
configuration of the client.  Correct these errors before continuing!

Getting Help
------------

To see the help for a given command, do the following::

    $ stratus-describe-instance --help
    Usage: stratus-describe-instance [options] [vm-id ...]

     Provides information about the virtual machine with the given
     identifiers or all virtual machine if no identifier is given.
    ...

All of the StratusLab commands support this option.  It provides a
summary of the purpose of the command and a detailed list of the
available options.

All of the commands also support the ``--version`` option that prints
the version number of the client.  When reporting problems, it is very
helpful to also provide the exact version number of the client.

In general the command line interface returns a minimum of information
to the user.  To make the commands more verbose (especially when
tracking down errors), you can add the ``-v`` or ``--verbose`` option.
This can be specified multiple times to increase the verbosity
further.  All commands support this option.

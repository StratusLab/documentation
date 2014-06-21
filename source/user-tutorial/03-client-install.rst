Command Line Client
===================

Installation
------------

The StratusLab command line client is written mostly in Python except
for a small part (related to XML cryptographic signatures) written in
Java.

End-users should use the Python package installer (``pip``) to install
the client on their machines.  The command required to install the
client and all of the required dependencies is::

    $ pip install stratuslab-client

By default, ``pip`` will install the software in the system area to make
it available for all users on the system. If you don't have the rights
to do this, then you can do a user-level installation::

    $ pip install --user stratuslab-client

In this case, you will need to add the appropriate directories to
your path, adding for example, a line like::

    PATH=$PATH:$HOME/.local/bin

to your ``.bash_profile`` file (or the equivalent for whatever shell you
are using).

.. note::

   If you want to install a beta version or release candidate, then
   for newer versions of ``pip`` you will have to use the ``--pre``
   option to allow pre-release versions to be installed.

Configuration
-------------

You must configure the command line client, providing the service
endpoints for the StratusLab cloud that you want to use and your
credentials for that cloud.

Create your configuration file by running the following command::

    $ stratus-copy-config

This will create the file ``$HOME/.stratuslab/stratuslab-user.cfg``. You
will now need to edit this file to provide the required information. The
file itself is extensively commented, showing all of the available
options.

If you are using the StratusLab reference cloud infrastructure, your
file should be similar to the following after you have made the
necessary changes::

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
with your actual credentials.

.. note::

   If your password (or any other value) contains a percent sign (%),
   you **must** double that character in the configuration file. This
   is because the Python software used to read the configuration files
   allows variable substitution with the syntax ``%(variable)``.

Verifying the Client
--------------------

To test that the client is functioning correctly and can contact the
cloud services, run the following commands::

    $ stratus-describe-instance
    $ stratus-describe-volumes

These should return an empty list of virtual machines and of volumes,
respectively.  Any error indicates a problem with the installation or
configuration of the client.  Correct these errors before continuing!

Getting Help
------------

To see the help for a given command, do the following::

    $ stratus-run-instance --help

All of the StratusLab command support this option.  It provides a
summary of the purpose of the command and a detailed list of the
available options.

All of the commands also support the ``--version`` option that prints
the version number of the client.  When reporting problems, it is very
helpful to also provide the exact version number of the client.

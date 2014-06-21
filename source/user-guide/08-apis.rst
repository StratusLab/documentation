Programmatic Access
===================

Application developers often desire to have programmatic access to cloud
services. This allows them to easily integrate those services into
existing applications and analysis frameworks.

StratusLab provides several mechanisms by which application developers
can access its services. All services provide REST interfaces, making
them easily accessible from all languages via standard HTTP(S) client
libraries. The collaboration also provides the python APIs that are used
internally by the command line interface and a Libcloud plug-in.

REST Interfaces
---------------

Services with a resource-oriented architecture expose a set of resources
or objects via well defined URLs, standard CRUD (create, read, update,
and delete) operations are then supported for those resources. Although
not required, nearly all such services use the HTTP(S) protocol and
reuse the standard HTTP actions for implementing the CRUD functionality.

Using the standard HTTP protocol and having a well defined hierarchy of
URLs representing resources, makes access easy from any programming
language with a HTTP client library. Because of this universal
accessibility and the intuitive mapping between resources and URLs,
StratusLab using a resource-oriented architecture for all of its
services.

However, because of significant underlying changes in the StratusLab
service implementations at this time, it is recommended that application
developers either use the Libcloud API or script the command line
interface. This will protect them from the significant, incompatible
changes in the APIs that are currently taking place.

StratusLab API
~~~~~~~~~~~~~~

As mentioned above, the REST APIs and the corresponding StratusLab
python API are undergoing significant changes at the moment. Application
developers should avoid these interfaces if possible. Contact the
StratusLab support for more information about the current status.

CIMI API
~~~~~~~~

The CIMI API, a standard REST API for IaaS clouds, will become the
standard interface for all of the StratusLab services. Application
developers planning to port their software to the StratusLab cloud
should target this API. This API should appear over the next couple of
StratusLab releases (i.e. 3-6 months).

Libcloud API
------------

The Libcloud API provides a generic python interface for controlling
cloud resources from multiple providers. StratusLab now provides a
Libcloud compute driver allowing users to access StratusLab cloud
infrastructures via this API.

Application developers who desire a stable interface should prefer this
API. It is available now and will remain stable despite the underlying
changes to the StratusLab service implementations.

Installing the Driver
~~~~~~~~~~~~~~~~~~~~~

The driver is intended to be installed with pip. You should be able to
simply do the following:

::

    pip install stratuslab-libcloud-drivers

which will install the StratusLab Libcloud driver, Libcloud itself
(0.12.1), and the StratusLab client. If you want to use the
``deploy_node()`` function, you'll also need to install paramiko, a
python SSH library, as well.

::

    pip install paramiko

If pip is configured to do system-wide installations, then the
PYTHONPATH and PATH should already be set correctly. If it is setup for
user area installations, you will likely need to set these variables by
hand.

You can download the package directly from
`PyPi <http://pypi.python.org/>`__. The name of the package is
"stratuslab-libcloud-drivers". You will also need to download and
install all of the dependencies. **Using pip is very strongly
recommended.**

Configuring the StratusLab Client
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Libcloud drivers for StratusLab use the same configuration file as
for the command line client.

To copy a reference configuration file into place, use the command
``stratus-copy-config`` to copy an example configuration file into
place. The command will print the location of your configuration file.
The example configuration file contains extensive documentation on the
parameters. Edit the file and put in your credentials and cloud
endpoints.

More detailed documentation can be found in the `StratusLab
documentation <http://stratuslab.eu/documentation/>`__ area on the
website.

Using the Driver
~~~~~~~~~~~~~~~~

Once you've downloaded, installed, and configured the necessary
dependencies, you are ready to start using the driver.

From the Python interactive shell do the following:

::

    from libcloud.compute.providers import set_driver

    set_driver('STRATUSLAB', 
               'stratuslab.libcloud.compute_driver',
               'StratusLabNodeDriver')

This registers the driver with the Libcloud library. This import must be
done **before** asking Libcloud to use the driver! Once this is done,
then the driver can be used like any other Libcloud driver.

::

    # Obtain an instance of the StratusLab driver. 
    from libcloud.compute.types import Provider
    from libcloud.compute.providers import get_driver
    StratusLabDriver = get_driver('stratuslab')
    driver = StratusLabDriver('default')

    # Use the Libcloud methods to find, create and control nodes.
    nodes = driver.list_nodes()

There are a couple examples in the test area of the GitHub repository
for this driver
`lc-sl-examples <https://github.com/StratusLab/libcloud-drivers/tree/master/test>`__.
You can also find general information on the Apache Libcloud website.

Driver Status
~~~~~~~~~~~~~

The driver is functionally complete and should work with all of the
standard Libcloud workflows. Problems encountered when using the driver
should be reported via the StratusLab support mailing list.

In detail, the following functions have working implementations:

-  ``list_images``: list all valid images in Marketplace
-  ``list_locations``: list of sections in configuration file
-  ``list_sizes``: list of standard machine instance types
-  ``create_node``: start a virtual machine
-  ``deploy_node``: start a VM and run a script (see notes below)
-  ``destroy_node``: terminate a virtual machine
-  ``list_nodes``: list of active virtual machines
-  ``create_volume``: create persistent disk
-  ``destroy_volume``: destroy a persistent disk
-  ``attach_volume``: attach a volume to node
-  ``detach_volume``: remove a volume from a node

The ``list_volumes`` function is specific to the StratusLab driver and
is not part of the Libcloud standard abstraction.

The ``reboot_node`` function will not be implemented as the required
functionality is not provided by a StratusLab cloud.

**Notes** for ``deploy_node``:

1. The SSH library used by Libcloud seems to only work correctly with
   DSA SSH keys. You can have both RSA and DSA keys available in
   parallel.

2. This function uses sftp to transfer the script between the client and
   the virtual machine. Consequently, SSH implementations that do not
   support sftp will not work. This include, notably, ttylinux.



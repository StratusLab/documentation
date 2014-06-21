Compute Resources
=================

As with all cloud infrastructures, compute resources are provided
through virtual machines with StratusLab.  This section starts with a
description of the Marketplace, a catalog of virtual machine images,
and then covers how to start, access, and stop virtual machine
instances.

StratusLab Marketplace
----------------------

All of the available virtual machine images (or appliances) are
registered in the StratusLab Marketplace. These include standard images
created and maintained by the StratusLab developers and others created
by StratusLab users.

Use your web browser to view the available appliances by navigating to
the central `Marketplace <https://marketplace.stratuslab.eu/>`__. You
can find the standard images from StratusLab by putting
"images@stratuslab.eu" into the "endorser" field on the right side of
the interface.

The summary contains the basic information for an appliance along with a
link to find more information. The most imporant bit of information is
the **image identifier**. This is a 27 character Base 64 value that
uniquely identifies the appliance. This identifier is used to select the
appliance you want to run as a virtual machine.

StratusLab provides minimal distributions of CentOS 6, ScientificLinux
6, Ubuntu 12.04, and Ubuntu 14.03. These follow the best practices for
appliance creation and are kept up to date with security patches. They
make good bases for creating your own applications.

Virtual Machine Lifecycle
-------------------------

The virtual machine lifecycle consists of the following commands:: 

    $ stratus-run-instance ${APP_ID}
    $ stratus-describe-instance
    $ stratus-connect-instance ${VM_ID} or ssh root@${VM_IP}
    $ stratus-kill-instance

These commands start, list, access, and destroy a virtual machine,
respectively.

.. note::

   The ``stratus-connect-instance`` command does not work on Windows.
   Use your local SSH command or application to connect to your
   virtual machine.

.. warning::

   The ``stratus-kill-instance`` stops a running virtual machine
   instantly. This is the equivalent of pulling out the plug. A more
   graceful shutdown should be done in most cases where the machine is
   halted (from within the machine) before running this command.

Choose one of the standard appliances and use these commands to go
through the workflow.

Allocated Resources
-------------------

The resources allocated to virtual machines can be changed with options
*at deployment time*. A set of predefined "machine types" provide some
default configurations.

You can see the predefined configurations with the command::

    $ stratus-run-instance --list-types

The values for the CPU, RAM (MiB), and SWAP (MiB) space are listed for
each type with the default type marked with an asterisk. You can change
which type is selected by using the ``--type`` option when starting a
virtual machine.

Fine-grained control over the resource allocation is also possible. The
options ``--cpu``, ``--ram``, and ``--swap`` allow you to set values
separately. For values that are not specified explicitly, the value will
be taken from the selected machine type.

Contextualization
-----------------

Contextualization is the process by which a virtual machine discovers
characteristics of its environment and properly configures itself. This
is used, for example, for network configuration but can also be used for
user-level service configuration.

StratusLab supports two types of contextualization: OpenNebula
contextualization through a CD-ROM image and CloudInit contextualization
via a VFAT image. The OpenNebula method is the default. The CloudInit
contextualization is more flexible.

Default Contextualization
~~~~~~~~~~~~~~~~~~~~~~~~~

CloudInit Contextualization
~~~~~~~~~~~~~~~~~~~~~~~~~~~

See the documentation for the details on the CloudInit options.

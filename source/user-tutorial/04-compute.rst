Compute Resources
=================

"Infrastructure as a Service" cloud infrastructures provide computing
resources through "virtual machines".  For end users, these virtual
machines act just like physical machines, except that they can be
provisioned quickly with exactly the configuration desired.

This section starts with a description of the Marketplace, a catalog
of virtual machine images, and then covers how to start, access, and
stop virtual machine instances.

Appliance Marketplace
---------------------

All virtual machine instances are created from virtual machine images
(or appliances) that contain the starting configuration for the
virtual machine. 

For StratusLab clouds, the Marketplace contains a database of all of
the available appliances.  These include standard appliances created
and maintained by the StratusLab developers as well as appliances
created by other StratusLab users.

Use your web browser to view the available appliances by navigating to
the central `Marketplace
<https://marketplace.stratuslab.eu/marketplace/>`__. You can find the
standard images from StratusLab by putting "images\@stratuslab.eu"
into the "endorser" field on the right side of the interface.

The summary contains the basic information for an appliance along with a
link to find more information. The most imporant bit of information is
the **image identifier**. This is a 27 character Base 64 value that
uniquely identifies the appliance. This identifier is used to select the
appliance you want to run as a virtual machine.

StratusLab provides minimal distributions of CentOS 6, ScientificLinux
6, Ubuntu 12.04, and Ubuntu 14.04. These follow the best practices for
appliance creation and are kept up to date with security patches. They
make good starting points for creating your own appliances.

.. admonition:: Exercise

   Use your web browser to navigate through the Marketplace.  Use the
   search widget on the right to see what appliances are available.
   Use the search widget to find all of the appliances created by the
   StratusLab collaboration.

Virtual Machine Lifecycle
-------------------------

The virtual machine lifecycle consists of the following commands:: 

    $ stratus-run-instance ${APP_ID}  # APP_ID from the Marketplace
                                      # returns VM_ID and VM_IP
    $ stratus-describe-instance ${VM_ID}

    # Connect to the deployed virtual machine
    $ stratus-connect-instance ${VM_ID}
    $ ssh root@${VM_IP}

    $ stratus-kill-instance ${VM_ID}

These commands start, list, access, and destroy a virtual machine,
respectively.

The ``stratus-run-instance`` command starts a new virtual machine
instance.  It requires the appliance or image identifier from the
Marketplace (``APP_ID``).  This will return the virtual machine
identifier (``VM_ID``) and its assigned IP address (``VM_IP``).

The ``stratus-describe-instance`` command displays the information
about virtual machines.  Without an argument, it will provide a
concise summary for all non-terminated virtual machines.  If you
provide a virtual machine identifier, then only information for that
machine will be returned.  More detailed information can be obtained
by using the ``--verbose`` option.

You can log into your virtual machine using SSH.  Your public SSH key
will have been transferred to the machine and will allow you to log
into the machine as ``root``.  Use ``stratus-connect-instance
${VM_ID}`` or just ``ssh root@${VM_IP}`` to log into the machine.
Depending on the operating system, resources, etc., it can take
several minutes to have access to a virtual machine.  

.. note::

   The ``stratus-connect-instance`` command does not work on Windows.
   Use your local SSH command or application to connect to your
   virtual machine.

The ``stratus-kill-instance`` command stops a virtual machine and
releases all of the allocated resources.  Virtual machines that have
been terminated will no longer appear in the
``stratus-describe-instance`` output by default.

.. warning::

   The ``stratus-kill-instance`` stops a running virtual machine
   instantly. This is the equivalent of pulling out the plug. A more
   graceful shutdown should be done in most cases: halt the virtual
   machine (with ``halt`` or ``shutdown`` within the machine) and then
   run ``stratus-kill-instance``.

.. admonition:: Exercise 

   Choose one of the standard appliances (e.g. Ubuntu 14.04) and use
   these commands to go through the lifecycle for a virtual machine.
   How long was it before you could ``ping`` the virtual machine?  How
   long before you could log in?

Allocated Resources
-------------------

The resources allocated to virtual machines can be defined with
options *at deployment time*. StratusLab does not allow the resource
allocations for existing virtual machines to be changed.

A set of predefined "machine types" provide some default
configurations.

You can see the predefined configurations with the command::

    $ stratus-run-instance --list-types
     Type             CPU       RAM      SWAP
      c1.medium       2 CPU    1536 MB    1536 MB
      c1.xlarge       4 CPU    6144 MB    6144 MB
      m1.large        2 CPU    6144 MB    6144 MB
      m1.medium       1 CPU    3072 MB    3072 MB
    * m1.small        1 CPU    1536 MB    1536 MB
      m1.xlarge       4 CPU    8192 MB    8192 MB
      t1.micro        1 CPU     512 MB     512 MB

The values for the CPU, RAM (MiB), and SWAP (MiB) space are listed for
each type with the default type marked with an asterisk. You can change
which type is selected by using the ``--type`` option when starting a
virtual machine.

Fine-grained control over the resource allocation is also
possible. The options ``--cpu``, ``--ram``, and ``--swap`` allow you
to set these values separately. For values that are not specified
explicitly, the value will be taken from the selected machine type.

.. admonition:: Exercise 

   When logged into a virtual machine, can you determine how many CPUs
   were allocated and how much memory?  You can find this information
   by looking at `/proc/meminfo` and `/proc/cpuinfo`, for example.

.. admonition:: Exercise 

   Use the ``--type``, ``--cpu``, and ``--ram`` options to change the
   allocated resources for a virtual machine.  Verify that the correct
   amount of resources has been allocated.

Contextualization
-----------------

Contextualization is the process by which a virtual machine discovers
characteristics of its environment and properly configures itself. This
is used, for example, for network configuration but can also be used for
user-level service configuration.

Unfortunately, there is no standard for the contextualization
process, although the CloudInit process is slowly becoming a *de
facto* standard. 

StratusLab supports two contextualization mechanisms: HEPiX/OpenNebula
and CloudInit.  For historical reasons the HEPiX/OpenNebula mechanism
is currently the default.

HEPiX Contextualization
~~~~~~~~~~~~~~~~~~~~~~~

The HEPiX/OpenNebula contextualization passes information from the
user (given with the ``stratus-run-instance`` command) to the virtual
machine via a CD-ROM image.  The virtual machine automatically mounts
the CD-ROM image and executes a contextualization script using the
information from the image.

You public SSH key is automatically passed to the virtual machine
using this mechanism.  Additional key-value pairs can be passed to the
virtual machine via the ``--context`` parameter.

The context information can be seen on the client side by using the
``stratus-describe-instance -vvv ${VM_ID}`` command.  This displays
all of the information defining a given virtual machine.

From within the virtual machine, you can mount the CD-ROM image (if it
isn't already mounted) to see what scripts and what information has
been passed from the client to the virtual machine.  You can find the
image by using the `blkid` command.  CD-ROMs have the type
"iso9660".

.. admonition:: Exercise

   Start a virtual machine.  Log into the virtual machine, find the
   context CD-ROM, and mount it.  What files are there?  How are these
   executed in the startup process?  (Hint: Look in ``/etc/init.d/``.)

.. admonition:: Exercise

   Use the context options to start another virtual machine.  How are
   the key-value pairs you defined passed into the virtual machine?
   Can you imagine how to use this information to configure a service
   on the machine? 

CloudInit Contextualization
~~~~~~~~~~~~~~~~~~~~~~~~~~~

CloudInit is a very flexible contextualization mechanism that is
becoming a *de facto* standard.  StratusLab supports this mechanism,
although it currently is not the default.

To start a virtual machine using CloudInit, use the ``--cloud-init``
option.  For example::

    $ stratus-run-instance --cloud-init "" \
        KhGzWhB9ZZv5ZkLSZqm6pkWx7ZF

The ``--cloud-init`` option **requires** a value.  Passing the empty
string will use the default, which is to pass your SSH public key as
for the HEPiX/OpenNebula contextualization. 

Ubuntu provides `good documentation
<https://help.ubuntu.com/community/CloudInit>`__ for CloudInit
describing what can be passed to the virtual machine.

To demonstrate the flexibility, we will show how to use CloudInit to
start up a web server on a CentOS virtual machine.  Create a file
called ``run-httpd.sh`` with the contents::

    #!/bin/bash -x

    yum install -y httpd 

    cat > /var/www/html/test.txt <<EOF
    SUCCESSFUL TEST
    EOF

    chkconfig httpd on 

    service httpd start

This will install, configure, and run the web server on the virtual
machine. 

Pass this script to a virtual machine based on a CentOS image::

    $ stratus-run-instance \
        --cloud-init "x-shellscript,run-http.sh" \
        KT8gOU8gve_k3UFL7p5Els57My2

After a couple of minutes, you should be able to visit the url
`http://your-vm.example.com/test.txt` to see a page containing
"SUCCESSFUL TEST".

If you want to pass multiple files, you can separate the mimetype/file
pairs with hash (#) characters or use the ``stratus-prepare-context``
and then the ``--context-file`` option of ``stratus-run-instance``.

.. admonition:: Exercise

   Start a virtual machine with CloudInit.  Log into the virtual
   machine, find the context VFAT disk, and mount it.  What files are
   there?  How are these executed in the startup process?

.. admonition:: Exercise

   Do the same exercise for an Ubuntu machine?  What did you have to
   change to get this to work?  Can you install and configure another
   service on a virtual machine that would be visible from your web
   browser?

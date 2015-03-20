Image Management
================

Being able to fully customize the execution environment is one of the
strongest attractions of a IaaS cloud. StratusLab provides tools to find
existing customized virtual machine images and to create new ones if
necessary.

Finding Available Images
------------------------

Building new virtual machine images can be a tedious and time-consuming
task. Your first instinct should be to first look to see if someone else
has done the work for you!

The `StratusLab Marketplace <https://marketplace.stratuslab.eu/>`__
provides a registry for available, public virtual machine images. These
are created by the people within the community as well as by StratusLab
partners to help people get started using the cloud quickly.

The StratusLab partners themselves provide "base" images for ttylinux,
CentOS (a RHEL derivative), Ubuntu, Debian, and OpenSuSE. These are
minimal, but functional, installations of these operating systems. They
can be used directly or customized to create personalized machines.
These can be found in the Marketplace by looking for "hudson.builder" as
the endorser of the images.

The Marketplace also contains images for various scientific disciplines.
IGE has prepared images for testing Globus services and for running
tutorials with the services. IBCP has created appliances with numerous
bioinformatics applications already installed. Search the Marketplace to
find appliances that interest you.

Building New Images from Existing Images
----------------------------------------

Although there are many images in the Marketplace, sometimes a suitable
image cannot be found. In this case, try to find a suitable appliance as
a starting point and create a new appliance by customizing the initial
one.

Automated Process
~~~~~~~~~~~~~~~~~

StratusLab provides the ``stratus-create-image`` command to automate the
production of a new image based on an existing one. (NOTE: This command
does not work on Windows. See manual process section.) This takes three
inputs:

-  The Marketplace identifier of the starting image,
-  A list of additional packages to install, and
-  A script to configuring the image.

In addition, some information about the new information will be
required--such as a description, the author, and the author's email
address.

As an example, let's use the StratusLab Ubuntu base image, adding an
Apache web server, and customizing the home page. To do this, we will
need to add the "apache2" and "chkconfig" packages to the image. We will
also need to run a script that modifies the server's home page.

First, create a script ``setup-ubuntu.sh`` that contains the following
commands:

::

    #!/bin/bash 

    #
    # Workaround to ensure old networking information isn't cached
    #
    rm -f /lib/udev/rules.d/*net-gen*
    rm -f /etc/udev/rules.d/*net.rules

    #
    # Modify the web server's home page.
    #
    cat > /var/www/cloud.txt <<EOF
    Cloudy Weather Expected
    EOF

This will modify the server's home page. When we eventually start the
modified image, we can use this to ensure that the modifications have
been correctly made.

Now use the ``stratus-create-image`` command to create the new image:

::

    $ stratus-create-image \
      -s setup-ubuntu.sh \
      -a apache2,chkconfig \
      --type m1.xlarge \
      --comment "ubuntu create image test" \
      --title "My first image" \
      --author "Joe Builder" \
      --author-email builder@example.org \
      HZTKYZgX7XzSokCHMB60lS0wsiv

Note that the necessary packages are included and the configuration
script has been referenced. In addition, information about the author
and the new image has been provided. The argument is the Marketplace
identifier of the image to start with; in this case, it is a base Ubuntu
image.

**Warning**: Be sure to provide a correct email address. The results of
the process will be sent to that address!

Running this command will produce output like the following:

::

     :::::::::::::::::::::::::::::
     :: Starting image creation ::
     :::::::::::::::::::::::::::::
     :: Checking that base image exists
     :: Retrieving image manifest
     :: Starting base image
      [WARNING] Image availability check is disabled.

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 1655)
     Public ip: 134.158.75.239
     :: Done!
     :: Waiting for machine to boot
    .............
     :: Waiting for machine network to start
    ....
     :: Check if we can connect to the machine
     :: Executing user prerecipe
     :: Installing user packages
     :: Executing user recipe
     :: Executing user scripts
    Connection to 134.158.75.239 closed.

     ::::::::::::::::::::::::::::::::::::::::
     :: Finished building image increment. ::
     ::::::::::::::::::::::::::::::::::::::::

     ::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
     :: Please check builder@example.org for new image ID and instruction. ::
     ::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
     :: Shutting down machine

At this point if you check the running machines, you'll see something
like this:

::

    $ stratus-describe-instance 
    id   state     vcpu memory    cpu% host/ip                  name
    1655 Epilog    4    0         0    vm-239.lal.stratuslab.eu creator: 2012-12-04T07:58:25Z

For a normal machine, the "Epilog" state flashes by very quickly because
it just deletes the virtual machine's resources. In this case however,
the "Epilog" process actually saves the modified image to a new volume
in the persistent disk service. Because these are generally
multi-gigabyte files, this process can take several minutes.

At the end of the "Epilog" process, an email will be sent to the user
with a subject like "New image created IOeo3R5qEdCas5j\_r1HxVne3JMk".
The body of the email contains:

-  The location of the created image,
-  The identifier of the created image, and
-  A draft metadata entry for the new image.

There will also be a temporary entry created in the Marketplace to allow
private testing of the image after creation. You can search for the
image identifier to find the metadata entry.

You can also find the created disk by searching the persistent disk
service:

::

    $ stratus-describe-volumes 
    :: DISK 410b7fb4-973b-4b6d-82a7-e637a5103f4d
       count: 0
       tag: 
       owner: builder
       identifier: IOeo3R5qEdCas5j_r1HxVne3JMk
       size: 6

Now we will try to deploy the new machine and verify that the web
service responds. Ubuntu takes several minutes to go through the full
boot process and to start the web service, so a little patience is
required.

::

    $ stratus-run-instance --type c1.medium IOeo3R5qEdCas5j_r1HxVne3JMk 

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 1657)
     Public ip: 134.158.75.58
     :: Done!

    $ # after waiting a few minutes...

    $ curl http://vm-58.lal.stratuslab.eu/cloud.txt 
    Cloudy Weather Expected

After testing the image, you'll need to take a few more steps to make
the image accessible for more than 2 days or to make it public.

To make the image public, the contents will need to be copied to a
public server. Mount the define on a VM and copy the contents to a
suitable location. (A future version will allow you to expose the disk
contents directly without copying them.) For a private disk, you do not
need to make any copies.

In both cases, modify the draft image metadata, especially providing a
longer validity period for the image. A resonable value is 6 months.
Sign the metadata with ``stratus-sign-metadata`` and upload it to the
Marketplace with ``stratus-upload-metadata`` or via the web interface.

Manual Process
~~~~~~~~~~~~~~

The ``stratus-create-image`` automates the interactions with the new
machine, but you may want to make modifications by hand (or be working
on Windows).

To repeat the above exercise with the manual process, start with the
command ``stratus-run-instance``:

::

    $ stratus-run-instance \
      --save \
      --type m1.xlarge \
      --comment "manual ubuntu test image" \
      --author "Joe Builder" \
      --author-email builder@example.org \
      --image-version=2.0 \
      HZTKYZgX7XzSokCHMB60lS0wsiv

      [WARNING] Image availability check is disabled.

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 1659)
     Public ip: 134.158.75.62
     :: Done!

The important option is the **--save** option. This will trigger the
copy of the image to a persistent disk at when the machine is shutdown.

Once the machine is accessible via SSH, log into the machine and execute
the following commands:

::

    $ rm -f /lib/udev/rules.d/*net-gen*
    $ rm -f /etc/udev/rules.d/*net.rules 
    $ apt-get install -y apache2 chkconfig
    $ cat > /var/www/cloud.txt
    Cloudy Weather Expected

See the previous section for information about what these commands do.

After all of the modifications have been made, log out of the machine.
**Use the command ``stratus-shutdown-instance`` to stop the machine.**
If you use ``stratus-kill-instance`` the changes you've made will be
lost.

::

    $ stratus-shutdown-instance 1659 
    $ stratus-describe-instance 
    id   state     vcpu memory    cpu% host/ip                 name
    1659 Shutdown  4    8388608   7    vm-62.lal.stratuslab.eu creator: 2012-12-04T09:16:35Z

    $ stratus-describe-instance 
    id   state     vcpu memory    cpu% host/ip                 name
    1659 Epilog    4    8388608   7    vm-62.lal.stratuslab.eu creator: 2012-12-04T09:16:35Z

The machine will entry the "Shutdown" then the "Epilog" states. The
image is copied during the "Epilog" state. When completed, you will
receive an email with the image metadata.

You can check that the image functions correctly:

::

    $ stratus-run-instance --type c1.medium J-zVxEV5vfFscoKPLOHjtubmrJF 

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 1664)
     Public ip: 134.158.75.70
     :: Done!

    $ # after waiting a few minutes...

    $ curl http://vm-70.lal.stratuslab.eu/cloud.txt 
    Cloudy Weather Expected

Follow the instructions in the previous section to make this a public
image.

Building Images from Scratch
----------------------------

Sometimes a suitable starting image cannot be found and building an
image from scratch is required. Usually it is easiest to build new
images with desktop virtualization solutions. The results must be
converted to a format suitable for KVM.

Generating an image from scratch can be tedious and there are lots of
pitfalls along the way. Keep in mind the following points:

-  Images must support the StratusLab contextualization scheme
-  Ensure DHCP network configuration (and turn off udev persistent net
   rules)
-  All private information (keys, passwords, etc.) must be removed
-  Remote access must only be via SSH keys, not by password
-  Activate firewall blocking all unused ports
-  Minimize installed software and services

As there are many places to run into problems, you're advised to contact
the `StratusLab support <mailto:support@stratuslab.eu>`__ before
starting.

Contextualization
-----------------

Contextualization allows a virtual machine instance to learn about its
cloud environment (the 'context') and to configure itself to run
correctly there. StratusLab now supports CloudInit contextualization in
addition to the OpenNebula and HEPiX contextualization schemes.

OpenNebula and HEPiX Contextualization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To be done.

CloudInit
~~~~~~~~~

The `CloudInit <https://help.ubuntu.com/community/CloudInit>`__
mechanism is gaining traction within the cloud ecosystem and is moving
towards becoming a de facto standard for the process. Because it handles
both web-server and disk based contextualization mechanisms it is fairly
straightforward for cloud software developers to implement.

For the appliance developers it provides a convenient modular framework
for allowing both system and user-level service configuration. Being
written in python with OS packages for most systems, makes it easy for
those developers to include and use the software.

Ubuntu provides `good
documentation <https://help.ubuntu.com/community/CloudInit>`__ for
CloudInit. The software itself can be downloaded from
`launchpad <https://launchpad.net/cloud-init>`__ or installed from
standard repositories for your operating system (e.g.
`EPEL <http://fedoraproject.org/wiki/EPEL>`__ for CentOS).

CloudInit-Enabled Images
~~~~~~~~~~~~~~~~~~~~~~~~

All of the latest versions of the base virtual machine images maintained
by StratusLab now support CloudInit. Using one of those images is the
easiest way to see how CloudInit works. However, you can build your own
image with CloudInit support; see the appendix of this document for
instructions.

Web Server Example
~~~~~~~~~~~~~~~~~~

To show how users can pass CloudInit information to the appliance, we
will work through an example with the latest CentOS base image. We will
use a script to install and configure a web server on the example
machine. This script is generated by the user, sent via the
``stratus-run-instance`` command and then executed in the machine
instance by the CloudInit contextualization.

The script that we want to execute on the machine instance is the
following:

::

    #!/bin/bash -x

    yum install -y httpd 

    cat > /var/www/html/test.txt <<EOF
    SUCCESSFUL TEST
    EOF

    chkconfig httpd on 

    service httpd start

This installs, configures, and starts a web server on the machine. We've
named this script ``run-http.sh``. Create this script on the machine
where you've installed the StratusLab client commands.

The context information must be defined and passed to the virtual
machine when starting it. The context is defined by a set of
mimetype,file pairs:

::

    ssh,$HOME/.ssh/id_rsa.pub
    x-shellscript,run-http.sh

For ssh keys, use the pseudo-mimetype of 'ssh'. A single file can be
included literally (instead of being embedded in a multipart message)
with a pseudo-mimetype of 'none'.

This information can be passed directly to ``stratus-run-instance`` with
the ``--cloud-init`` option:

::

    $ stratus-run-instance \
         --cloud-init \
         'ssh,$HOME/.ssh/id_rsa.pub#x-shellscript,run-http.sh' \
         ... other options ...

Note that in this case, multiple pairs are separated by a hash sign. You
can also create a ``cloud-init.txt`` file containing the context
information:

::

    $ stratus-prepare-context \
         ssh,$HOME/.ssh/id_rsa.pub \
         x-shellscript,run-http.sh

and then pass this to the ``stratus-run-instance`` command with the
``--context-file`` option:

::

    $ stratus-run-instance --context-file cloud-init.txt

The first option is generally the most convenient option unless further
(non-cloud-init) options need to be passed to the virtual machine.

The context can contain multiple public ssh keys and multiple scripts or
other files. See the `CloudInit
documentation <https://help.ubuntu.com/community/CloudInit>`__ for what
is permitted for 'user data'.

**Warning**: No ssh keys are included by default. You must specify
explicitly any keys that you want to include.

**Warning**: Be sure that the contextualization information you are
passing can be used by the CloudInit version within the appliance
itself. **Be particularly careful because multipart inputs do not work
if the python version in the virtual machine is less than 2.7.3.**

This is the case with the CentOS image used here, so we'll include the
script literally in the user data with context information:

::

    ssh,$HOME/.ssh/id_rsa.pub
    none,run-http.sh

Note the use of the 'none' pseudo-mimetype. If 'none' is used, only the
last file marked as 'none' will be included in the user data; it will be
included literally, that is without encapsulating it in a multipart
form.

If you use the ``stratus-prepare-context`` command, you can see what
key-value pairs are passed to the virtual machine. The
``cloud-init.txt`` will look like:

::

    CONTEXT_METHOD=cloud-init
    CLOUD_INIT_AUTHORIZED_KEYS=c3NoLXJzYS...
    CLOUD_INIT_USER_DATA=H4sIAGHZzV...

The last two parameters contain base64 encoded representations of the
ssh keys and the ``run-http.sh`` script.

The machine can then be started with the command:

::

    $ stratus-run-instance \
        --cloud-init \
        ssh,$HOME/.ssh/id_rsa.pub'#none,run-http.sh' \
        IRei7LKvxoWVRsiiup2cz3-sSsk

After this machine starts it should be possible to see the configured
file in the appliance's web server:

::

    $ curl http://vm.example.org/test.txt 
    SUCCESSFUL TEST

Of course you need to change the node name to point to the machine
instance that you've started.

Future Work
~~~~~~~~~~~

StratusLab support for CloudInit should make it easier for users to
create appliances that will run on StratusLab as well as on other clouds
using an implementation compatible with CloudInit. This will be an
important gain for users as they move to a federated cloud environment.

This preview support for CloudInit demonstrates the utility of this
contextualization method. The collaboration is interested in hearing
your feedback on the implementation so that we can improve it in
upcoming releases. This will likely become the default contextualization
method in a future release.

Building an Image with CloudInit
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The standard StratusLab commands for creating appliances (e.g.
``stratus-create-image``) can be used to create an appliance using
CloudInit. See the image creation document for details on the commands.

The following script can be fed to ``stratus-create-image`` to create an
example appliance with CloudInit support. Note that all of the recent
base images maintained by StratusLab already contain CloudInit support.

::

    #!/bin/bash -x 

    #
    # Turn off udev caching of network information.
    #
    rm -f /lib/udev/rules.d/*net-gen*
    rm -f /etc/udev/rules.d/*net.rules

    #
    # Configure the machine for the EPEL repository.  The CloudInit
    # package is available from there.
    #
    wget -nd http://fr2.rpmfind.net/linux/epel/6/i386/epel-release-6-8.noarch.rpm
    yum install -y epel-release-6-8.noarch.rpm

    #
    # Upgrade all package on the machine and install cloud-init.
    yum clean all 
    yum upgrade -y 
    yum install -y cloud-init

    #
    # Change configuration to allow root access.  Signal that the 'user'
    # account should also be configured.
    #
    sed -i 's/user: ec2-user/user: root/' /etc/cloud/cloud.cfg
    sed -i 's/disable_root: 1/disable_root: 0/' /etc/cloud/cloud.cfg

This script was named ``create-cloud-init-appliance.sh`` for the command
to create the machine:

::

    $ stratus-create-image \
        -s create-cloud-init-appliance.sh \
        --type c1.medium \
        --title 'example title' \
        --comment 'example comment' \
        --author 'Jane Creator' \
        --author-email 'jane@example.org' \
        Jd3yRF6x4ofxfCeVK6BmCkuHc0m 

The image ID Jd3yRF6x4ofxfCeVK6BmCkuHc0m refers to a standard CentOS 6.2
machine without CloudInit support. The configuration commands and the
package to be installed will depend on what base operating system you
choose.

The image generated with the above procedure will work with the
CloudInit tutorial described above.

Converting a VirtualBox Image
-----------------------------

If you used VirtualBox to create a VM, you can convert it to a raw
images to run on StratusLab. The easiest way to make your VM compatible
with StratusLab is to convert your vdi disk into raw disk. This can be
done with standard VirtualBox tools with the following command:

::

    $ VBoxManage internalcommands converttoraw mydisk.vdi mydisk.img
    $ gzip mydisk.img

Now, you ve got a img.gz, you can put this in a public location and
register the image in the Marketplace as usual.

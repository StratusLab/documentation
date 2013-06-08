---
layout: article
title: Customized Images
category: documentation
---

Being able to fully customize the execution environment is one of the
strongest attractions of a IaaS cloud.  StratusLab provides tools to
find existing customized virtual machine images and to create new ones
if necessary.


Finding Existing Images
-----------------------

Building new virtual machine images can be a tedious and
time-consuming task.  Your first instinct should be to first look to
see if someone else has done the work for you!

The [StratusLab Marketplace](https://marketplace.stratuslab.eu/)
provides a registry for available, public virtual machine images.
These are created by the people within the community as well as by
StratusLab partners to help people get started using the cloud
quickly.

The StratusLab partners themselves provide "base" images for ttylinux,
CentOS (a RHEL derivative), Ubuntu, Debian, and OpenSuSE.  These are
minimal, but functional, installations of these operating systems.
They can be used directly or customized to create personalized
machines.  These can be found in the Marketplace by looking for
"hudson.builder" as the endorser of the images.

The Marketplace also contains images for various scientific
disciplines.  IGE has prepared images for testing Globus services and
for running tutorials with the services.  IBCP has created appliances
with numerous bioinformatics applications already installed.  Search
the Marketplace to find appliances that interest you. 


Building New Images from Existing Images
----------------------------------------

Although there are many images in the Marketplace, sometimes a
suitable image cannot be found.  In this case, try to find a suitable
appliance as a starting point and create a new appliance by
customizing the initial one.

### Automated Process

StratusLab provides the `stratus-create-image` command to automate the
production of a new image based on an existing one.  (NOTE: This
command does not work on Windows.  See manual process section.) This
takes three inputs:

* The Marketplace identifier of the starting image,
* A list of additional packages to install, and 
* A script to configuring the image. 

In addition, some information about the new information will be
required--such as a description, the author, and the author's email
address.

As an example, let's use the StratusLab Ubuntu base image, adding an
Apache web server, and customizing the home page.  To do this, we will
need to add the "apache2" and "chkconfig" packages to the image.  We
will also need to run a script that modifies the server's home page. 

First, create a script `setup-ubuntu.sh` that contains the following
commands: 

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

This will modify the server's home page.  When we eventually start the
modified image, we can use this to ensure that the modifications have
been correctly made.

Now use the `stratus-create-image` command to create the new image:

    $ stratus-create-image \
      -s setup-ubuntu.sh \
      -a apache2,chkconfig \
      --type m1.xlarge \
      --comment "ubuntu create image test" \
      --author "Joe Builder" \
      --author-email builder@example.org \
      HZTKYZgX7XzSokCHMB60lS0wsiv

Note that the necessary packages are included and the configuration
script has been referenced.  In addition, information about the author
and the new image has been provided.  The argument is the Marketplace
identifier of the image to start with; in this case, it is a base
Ubuntu image. 

**Warning**: Be sure to provide a correct email address.  The results
of the process will be sent to that address!

Running this command will produce output like the following:

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

    $ stratus-describe-instance 
    id   state     vcpu memory    cpu% host/ip                  name
    1655 Epilog    4    0         0    vm-239.lal.stratuslab.eu creator: 2012-12-04T07:58:25Z

For a normal machine, the "Epilog" state flashes by very quickly
because it just deletes the virtual machine's resources.  In this case
however, the "Epilog" process actually saves the modified image to a
new volume in the persistent disk service.  Because these are
generally multi-gigabyte files, this process can take several
minutes.

At the end of the "Epilog" process, an email will be sent to the user
with a subject like "New image created IOeo3R5qEdCas5j_r1HxVne3JMk".
The body of the email contains:

* The location of the created image,
* The identifier of the created image, and 
* A draft metadata entry for the new image.

There will also be a temporary entry created in the Marketplace to
allow private testing of the image after creation.  You can search for
the image identifier to find the metadata entry. 

You can also find the created disk by searching the persistent disk
service: 

    $ stratus-describe-volumes 
    :: DISK 410b7fb4-973b-4b6d-82a7-e637a5103f4d
       count: 0
       tag: 
       owner: builder
       identifier: IOeo3R5qEdCas5j_r1HxVne3JMk
       size: 6

Now we will try to deploy the new machine and verify that the web
service responds.  Ubuntu takes several minutes to go through the full
boot process and to start the web service, so a little patience is
required. 

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
public server.  Mount the define on a VM and copy the contents to a
suitable location.  (A future version will allow you to expose the
disk contents directly without copying them.)  For a private disk, you
do not need to make any copies.

In both cases, modify the draft image metadata, especially providing a
longer validity period for the image.  A resonable value is 6 months.
Sign the metadata with `stratus-sign-metadata` and upload it to the
Marketplace with `stratus-upload-metadata` or via the web interface. 

### Manual Process

The `stratus-create-image` automates the interactions with the new
machine, but you may want to make modifications by hand (or be
working on Windows).

To repeat the above exercise with the manual process, start with the
command `stratus-run-instance`:

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

The important option is the **--save** option.  This will trigger the
copy of the image to a persistent disk at when the machine is
shutdown. 

Once the machine is accessible via SSH, log into the machine and
execute the following commands:

    $ rm -f /lib/udev/rules.d/*net-gen*
    $ rm -f /etc/udev/rules.d/*net.rules 
    $ apt-get install -y apache2 chkconfig
    $ cat > /var/www/cloud.txt
    Cloudy Weather Expected

See the previous section for information about what these commands
do. 

After all of the modifications have been made, log out of the
machine.  **Use the command `stratus-shutdown-instance` to stop the
machine.**  If you use `stratus-kill-instance` the changes you've made
will be lost.

    $ stratus-shutdown-instance 1659 
    $ stratus-describe-instance 
    id   state     vcpu memory    cpu% host/ip                 name
    1659 Shutdown  4    8388608   7    vm-62.lal.stratuslab.eu creator: 2012-12-04T09:16:35Z

    $ stratus-describe-instance 
    id   state     vcpu memory    cpu% host/ip                 name
    1659 Epilog    4    8388608   7    vm-62.lal.stratuslab.eu creator: 2012-12-04T09:16:35Z

The machine will entry the "Shutdown" then the "Epilog" states.  The
image is copied during the "Epilog" state.  When completed, you will
receive an email with the image metadata.

You can check that the image functions correctly:

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
image from scratch is required.  Usually it is easiest to build new
images with desktop virtualization solutions.  The results must be
converted to a format suitable for KVM.

Generating an image from scratch can be tedious and there are lots of
pitfalls along the way.  Keep in mind the following points:

* Images must support the StratusLab contextualization scheme
* Ensure DHCP network configuration (and turn off udev persistent
  net rules)
* All private information (keys, passwords, etc.) must be removed
* Remote access must only be via SSH, not password
* Activate firewall blocking all unused ports
* Minimize installed software and services

As there are many places to run into problems, you're advised to
contact the [StratusLab support](mailto:support@stratuslab.eu) before
starting.


# Quick Start 

This chapter provides the bare-bones instructions for getting up and
running with the StratusLab command line client.  The client provides
a set of commands for interacting with all of the StratusLab services.
Getting started involves just four steps:

1. Verifying the prerequisites,
2. Installation of the client,
3. Configuring the client, and
4. Testing the installation.

Each is covered in a section below.  More detailed information on
installing and configuring the client are in subsequent chapters;
similarly later chapters also cover the utilization of the StratusLab
services more throughly.

## Verifying the Prerequisities

Before starting, you must verify that the prerequisites for the
StratusLab command line are satisfied:

  * Python 2 (2.6+), virtualenv and pip are installed.
  * Java 1.6 or later is installed.
  * An SSH client is installed with an SSH key pair.
  * You have an active StratusLab account and connection parameters.

For the first three points, more information can be found in the
following chapter and in the appendix.  

For the StratusLab account, you must contact the administrator of your
cloud infrastructure.  The following parts of this chapter presume
that you have a username/password pair for credentials and are using
the StratusLab reference infrastructure at LAL.

## Client Installation

First create a virtual environment to hold the StratusLab client and
its dependencies.

    $ virtualenv $HOME/env/SL
    New python executable in /home/sluser/env/SL/bin/python
    Installing setuptools............done.
    Installing pip...............done.

You may want to choose a different name or location for your virtual
environment.  Now you will need to activate that environment.

    $ source $HOME/env/SL/bin/activate 
    (SL)$ 

The prompt should change to include the name of the virtual
environment. 

Now use pip to install the StratusLab client:

    (SL)$ pip install stratuslab-client 
    Downloading/unpacking stratuslab-client
      Downloading stratuslab-client-13.05.0.RC1.tar.gz (1.2MB): 1.2MB downloaded
    ...
    Successfully installed stratuslab-client dirq ...
    Cleaning up...

You can verify that the client is installed and accessible with by
searching for one of the StratusLab commands:

    (SL)$ which stratus-copy-config 
    ~/env/SL/bin/stratus-copy-config

All of the StratusLab commands begin with "`stratus-".  On systems
that support it, you can use tab completion to see all of the
available commands. 

## Client Configuration

Now that the StratusLab client is installed, it needs to be
configured.  You will need to have your credentials and the cloud
service endpoints available. 

Copy the reference configuration file into place and verify it is
present: 

    (SL)$ stratus-copy-config 

    (SL)$ ls $HOME/.stratuslab/
    stratuslab-user.cfg

This configuration file contains descriptions of all of the parameters
that can be set.  There are only three or four that must be set.

Set the service endpoints for the cloud entry point and the storage
(pdisk):

    endpoint = cloud.lal.stratuslab.eu
    pdisk_endpoint = pdisk.lal.stratuslab.eu

substituting the values for your cloud infrastructure.  (These are the
values for the StratusLab reference cloud infrastructure at LAL.)
Also set the values for your username and password:

    username = your.username
    password = your.password

again substituting your values for these parameters.

You can now see if you have any running machines on the cloud to test
if the client is correctly installed:

    (SL)$ stratus-describe-instance 
    id  state     vcpu memory    cpu% host/ip                 name
    (SL)$ 

This should return an empty list of machines.  If it returns any
errors, then you'll need to correct whatever went wrong in the
installation.  See the more detailed documentation. 

## Testing the Installation

To test the installation more throughly and to give you an idea how to
use the cloud, we will start a virtual machine and create a persistent
disk. 

### Virtual Machines

Normally, you would browse the [StratusLab
Marketplace](https://marketplace.stratuslab.eu/) to find an image that
is useful for you.  You then use the image identifier to start a copy
of that image.

We use the image identifier `BN1EEkPiBx87_uLj2-sdybSI-Xb` for a
ttylinux image.  This is a very minimal linux distribution usually
intended as an embedded operating system.  It is extremely small and
boots quickly, making it ideal for tests.

Launch a virtual machine instance using this image:

    (SL)$ stratus-run-instance BN1EEkPiBx87_uLj2-sdybSI-Xb

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 4710)
     Public ip: 134.158.75.152
     :: Done!

This gives you the VM identifier (4710) and the IP address from where
the machine can be accessed.  Afterwards, check the status of the
machine.  

    (SL)$ stratus-describe-instance 
    id   state     vcpu memory    cpu% host/ip                  name
    4710 Running   1    1572864   8    vm-152.lal.stratuslab.eu one-4710

You may have to wait a little while until it is in a running state.
Then verify that the machine is accessible with `ping`.  Once it is
visible try to log into the machine:  

    (SL)$ stratus-connect-instance 4710
    ...
    Enter passphrase for key '/home/sluser/.ssh/id_rsa': 
    # hostname
    ttylinux_host
    # exit

Note that the password is the password for your SSH key.  You can also
log in directly using ssh:

    (SL)$ ssh root@vm-152.lal.stratuslab.eu
    ...
    Enter passphrase for key '/home/sluser/.ssh/id_rsa': 
    #

Note that your must use the username defined by the person that
created the image.  This is almost always "root".  In both these
cases, information about the SSH host key have been suppressed for
clarity. 

The machine can then be killed (stopped) with the command:

    (SL)$ stratus-kill-instance 4710 
    (SL)$ 
    (SL)$ stratus-describe-instance 4710
    id   state     vcpu memory    cpu% host/ip                  name
    4710 Done      1    1572864   1    vm-152.lal.stratuslab.eu one-4710

The resources allocated to the machine are only released when the
machine is killed.  If you shutdown the machine while inside the
operating system with `halt` or `shutdown`, you must still use the
StratusLab command to kill the machine to release the resources.

### Persistent Disks

The lifecycle for a persistent disk (or volume) is also rather
simple.

To create a disk:

    (SL)$ stratus-create-volume --size 1 --tag=mydisk 
    DISK 08f59022-463a-4662-8136-c0cee5517f17

This creates a new disk with the given tag and a size of 1 GiB.  The
UUID is the identifier for the disk.

The disks can be listed with the command:

    (SL)$ stratus-describe-volumes 
    :: DISK 08f59022-463a-4662-8136-c0cee5517f17
       count: 0
       owner: cal
       tag: mydisk
       size: 1

Normally at this point, it would be attached to a virtual machine
instance, formatted, and then used to store data.  We will leave that
for the detailed chapters below.

When the disk is no longer needed, it can be deleted with the command: 

    (SL)$ stratus-delete-volume 08f59022-463a-4662-8136-c0cee5517f17
    DELETED 08f59022-463a-4662-8136-c0cee5517f17

That is the complete lifecycle for a persistent disk. 

## Conclusions

This chapter has shown you the procedure for installing the client and
then, the basic lifecycles for starting machines and for creating
persistent disks.  Hopefully, you are intrigued enough to read
following chapters that provide more detail on the StratusLab services
and their functionality.

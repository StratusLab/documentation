
Testing Installation
====================

At this point, you have both the Front End and one Node installed.  In
principle this is a fully functional installation.  To ensure this is
the case, we'll run the standard battery of tests on the cloud
infrastructure as a normal user.

User Configuration
------------------

We will create a new StratusLab user account. Note that StratusLab
accounts are independent of the Unix accounts on the machine itself.

Add the following line to the end of the file
``/etc/stratuslab/authn/login-pswd.properties``::

    $ cat >> /etc/stratuslab/authn/login-pswd.properties
    sluser=slpass,cloud-access

This creates a new StratusLab user 'sluser' with a password 'slpass'.
The group 'cloud-access' is mandatory for the user to have access to
the cloud services. (Crypted or hashed password values are also
allowed in the configuration.)

The StratusLab distribution supports other authentication methods
(LDAP, X509 certificates, X509 proxies, etc.), but a username/password
pair is the simplest for this tutorial.

StratusLab Client
-----------------

Now we will test that the cloud functions correctly by starting a new
virtual machine instance and logging into it. We'll test the cloud
service from a normal Unix user account on the Front End.

First, ensure that the StratusLab user client is installed on the
machine. Do the following as root::

    $ yum install -y stratuslab-cli-user

It is very likely that the user client commands are already installed.

.. note::

   For normal client installations, it is strongly recommended to use
   ``pip`` for the client installation.  See the User Tutorial or User
   Guide for more information.

Now create a normal Unix user for testing::

    $ adduser sluser

Now log in as the user and setup the account for using StratusLab. An
SSH key pair is required to log into your virtual machines and the
client requires that a complete client configuration file.

Log in as the user and create an SSH key pair. This is similar to the
process used for the root account on the machine.

::

    $ su - sluser
    $ ssh-keygen -q
    ...

Now copy the reference configuration file into place and edit the
parameters.

::

    $ stratus-copy-config
    $ vi $HOME/.stratuslab/stratuslab-user.cfg

You will need to set the "endpoint", "username", and "password"
parameters in this file. For the "endpoint" use the hostname or IP
address of your Front End. For the "username" and "password" use
"sluser" and "slpass", respectively.

Everything should be setup now. So try deploying a virtual machine. You
can look in the Marketplace to find an interesting machine to deploy.
We'll use an Ubuntu image here.

::

    $ stratus-run-instance KhGzWhB9ZZv5ZkLSZqm6pkWx7ZF

     :::::::::::::::::::::::::
     :: Starting machine(s) ::
     :::::::::::::::::::::::::
     :: Starting 1 machine
     :: Machine 1 (vm ID: 1)
     Public ip: 134.158.75.42
     :: Done!

Check the status of the machine as it starts::

    # Check its status.  Pending -> not yet assigned to a Node
    $ stratus-describe-instance 
    id  state     vcpu memory    cpu% host/ip                 name
    1   Pending   1    0         0    vm-42.lal.stratuslab.eu one-1

    # Check again.  Prolog -> resources for VM are being initialized 
    $ stratus-describe-instance 
    id  state     vcpu memory    cpu% host/ip                 name
    1   Prolog    1    0         0    vm-42.lal.stratuslab.eu one-1

    # Check again. Running -> hypervisor has started machine
    $ stratus-describe-instance 
    id  state     vcpu memory    cpu% host/ip                 name
    1   Running   1    0         0    vm-42.lal.stratuslab.eu one-1

When the machine reaches the 'running' status, the virtual machine is
running in the hypervisor on the Node. It will probably take some
additional time for the operating system to boot.

Verify that the machine has fully booted and is accessible from the
network::

    # Ping the virtual machine to see if it is accessible.    
    $ ping vm-42.lal.stratuslab.eu 
    PING vm-42.lal.stratuslab.eu (134.158.75.42) 56(84) bytes of data.
    From onehost-5.lal.in2p3.fr (134.158.75.5) icmp_seq=2 Destination Host
     Unreachable
    ...
    From onehost-5.lal.in2p3.fr (134.158.75.5) icmp_seq=8 Destination Host
     Unreachable
    64 bytes from vm-42.lal.stratuslab.eu (134.158.75.42): icmp_seq=9
     ttl=64 time=1.44 ms
    ...

    # Now login to the machine as root.
    $ ssh root@vm-42.lal.stratuslab.eu 

    The authenticity of host 'vm-42.lal.stratuslab.eu (134.158.75.42)'
     can't be established.
    RSA key fingerprint is
     6a:bd:f7:2d:b6:82:39:61:e6:ca:3f:c7:61:9d:72:31.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'vm-42.lal.stratuslab.eu,134.158.75.42'
     (RSA) to the list of known hosts.


    #       # <- we're logged into the virtual machine
    # exit  # just logout of the session
    logout
    Connection to vm-42.lal.stratuslab.eu closed.

Now the machine can be terminated::

    $ stratus-kill-instance 1

Going through the full lifecycle of a machine shows that all of the
services are working.

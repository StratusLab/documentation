
# Command Line Client

The tutorial will use both a web browser and the StratusLab command
line client to access the cloud resources.  The following describes
how to install and configure the command line client.

See the full [User's Guide][users-guide] if more details are needed or
you run into problems.

## Client Installation

First create a virtual environment to hold the StratusLab client and
its dependencies.

    $ virtualenv $HOME/env/SL
    New python executable in /home/sluser/env/SL/bin/python
    Installing setuptools............done.
    Installing pip...............done.

You may want to choose a different name or location for your virtual
environment.

Now you will need to activate that environment.

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

All of the StratusLab commands begin with `stratus-`.  On systems
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

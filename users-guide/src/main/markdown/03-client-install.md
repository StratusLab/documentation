
# Command Line Client

StratusLab provides a simple command line client that is easy to
install on all platforms. This client provides access to all of the
StratusLab services.

## Overview

The command line client is the principal method for accessing the
StratusLab services.  It is written almost entirely in portable python
and is easy to install and configure on all platforms (Linux, Mac OS
X, Windows).

A list of the commands along with brief descriptions are provided in
the table.  All of the commands start with the `stratus-` prefix.  On
platforms that support it, tab completion can be used to find all of
the commands.  All of the commands support the `--help` option, which
provides detailed information about the command and its options.  All
of the commands also support the `--version` option that will display
the version of the client being used.

-----------------------------  ----------------------------------------------
`stratus-copy-config`          copy reference configuration file into
                               the correct location

`stratus-describe-instance`    list VM instances

`stratus-run-instance`         start a new VM instance

`stratus-kill-instance`        destroy and release resources for a
                               given VM instance

`stratus-shutdown-instance`    stop and save a VM instance, must be 
                               used with `--save` option when starting
                               VM instance

`stratus-describe-volumes`     list persistent disk volumes

`stratus-create-volume`        create a new persistent disk volume

`stratus-delete-volume`        destroy an existing persistent disk
                               volume

`stratus-attach-volume`        dynamically attach a persistent disk 
                               volume to a VM instance

`stratus-detach-volume`        dynamically detach a persistent disk
                               volume from a VM instance

`stratus-update-volume`        update the metadata for a persistent
                               disk volume

`stratus-build-metadata`       create a Marketplace entry for a VM
                               image

`stratus-sign-metadata`        cryptographically sign a Marketplace
                               entry for a VM image

`stratus-validate-metadata`    verify that a VM image entry is
                               syntactically correct and signed

`stratus-upload-metadata`      upload a VM image entry to the
                               Marketplace

`stratus-deprecate-metadata`   deprecate a Marketplace entry for a VM
                               image

`stratus-create-image`         create a new VM image from an existing
                               image

`stratus-upload-image`         (deprecated) upload an image to the 
                               appliance repository

`stratus-connect-instance`     connect via ssh to a given VM instance

`stratus-hash-password`        hash a password to give to cloud
                               administrator

`stratus-prepare-context`      prepare a CloudInit contextualization
                               file

`stratus-run-cluster`          utility to run a batch cluster on the
                               cloud
-----------------------------  ----------------------------------------------

Table: Overview of StratusLab commands.

## Installation

### Verifying the Prerequisities

Before starting, you must verify that the prerequisites for the
StratusLab command line are satisfied:

  * Python 2 (2.6+), virtualenv and pip are installed.
  * Java 1.6 or later is installed.
  * An SSH client is installed with an SSH key pair.
  * You have an active StratusLab account and connection parameters.

Quick recipes for checking the first three points are below, with more
detailed information in the appendices. 

For the StratusLab account, you must contact the administrator of your
cloud infrastructure.  The administrator must give you 1) your
credentials and 2) the service endpoints of the cloud infrastructure.

You can quickly verify the availability of Python and utilities with
the following commands:

    $ python --version 
    Python 2.6.6

    $ which virtualenv 
    /usr/bin/virtualenv

Note that pip is always installed with virtualenv.

Similarly for java use the following:

    $ java -version
    java version "1.7.0_19"
    OpenJDK Runtime Environment (rhel-2.3.9.1.el6_4-x86_64)
    OpenJDK 64-Bit Server VM (build 23.7-b01, mixed mode)
 
Note that there is only one hyphen in the option. 

For SSH check to see if the directory `$HOME/.ssh/` contains files
starting with "`id_`".

    $ ls $HOME/.ssh/id_* 
    /home/sluser/.ssh/id_rsa  /home/sluser/.ssh/id_rsa.pub

### Local Installation

For local installations of the StratusLab client, for example within a
non-priviledged user account or on a user's laptop, an installation
using virtualenv and pip is very strongly recommended.

Assuming that the prerequisites are installed, the installation is
just a matter of a few commands.  First create a virtual environment
to hold the StratusLab client and its dependencies.

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
      Downloading stratuslab-client-13.05.0.RC1.tar.gz ...
    ...
    Successfully installed stratuslab-client dirq ...
    Cleaning up...

This will also install all of the required Python dependencies for the
client as well.  (The above output has been abridged.)

You can verify that the client is installed and accessible with by
searching for one of the StratusLab commands:

    (SL)$ which stratus-copy-config 
    ~/env/SL/bin/stratus-copy-config

All of the StratusLab commands begin with `stratus-`.  On systems
that support it, you can use tab completion to see all of the
available commands. 

Once the client is installed, it must be configured.  See the
instructions below. 

### System Wide Installation

The above method can also be used for system wide installations for
multi-user machines.  Simply use pip directly without using
virtualenv.

Additionally, StratusLab provides client packages for RedHat
Enterprise Linux (RHEL) systems.  These packages work also on
derivatives of these systems like CentOS, ScientificLinux, and
OpenSuSE.  

You must have root access to your machine to install these packages.
For RHEL and RHEL-like systems, it is recommended to do the
installation with yum.  The [configuration for
yum](http://yum.stratuslab.eu/) tells yum where to find the StratusLab
packages.  Choose the `centos-6` repository.  Use the command:

    $ yum install stratuslab-cli-user

to install the latest version of the client tools.

For SuSE, configure zypper for the StratusLab OpenSuSE repository
("opensuse-12.1") and use it to install the package.

Users must then configure the client within their accounts.

## Client Configuration

The values of the configuration parameters for the client can be
provided in several different ways.  The various mechanisms in order
of precedence are:

  * Command line parameters
  * Environment variables (`STRATUSLAB_*`)
  * Configuration file parameters
  * Defaults in the code

The names of the command line parameters and the environmental
variables can be derived from the name of the configuration file
parameter.  The table gives an example for one parameter and the
algorithm for deriving the other names. 

---------------------------- -----------------------------------------
`pdisk_endpoint`             configuration file parameter

`--pdisk-endpoint`           command line option: change underscores
                             to hyphens and prefix with two 
                             hyphens (`--`) 
                             
`STRATUSLAB_PDISK_ENDPOINT`  environmental variable: make all letters 
                             uppercase, prefix with `STRATUSLAB_` 
---------------------------- -----------------------------------------

Table: Deriving command line option and environmental names from 
the configuration file parameter.

The configuration file is in the standard INI format.  The file
**must** contain the `[default]` section.  It may also contain
additional sections describing parameters for different cloud
infrastructures.

A minimal configuration file, assuming that a username/password pair
is used for credentials is:

    [default]
    endpoint = cloud.lal.stratuslab.eu
    pdisk_endpoint = pdisk.lal.stratuslab.eu
    username = sluser
    password = slpass

The values will obviously have to change to correspond to your
credentials and the cloud infrastructure that you are using.

### Credentials

StratusLab provides a very flexible authentication system, supporting
username/password pairs, X509 certificates, Globus/VOMS certificate
proxies, and PKCS12 certificates.

------------------ --------------------------------
`username`         user's StratusLab username
`password`         user's password
`pem_certificate`  X509 or proxy certificate file
`pem_key`          X509 or proxy key file
------------------ --------------------------------

Table: Parameters for supplying user credentials.

Users should specify either the username/password or the
pem_certificate/pem_key parameters but not both sets.  If both are
specified, then the username/password will take precedence.  

The default for the pem certificate and key are the files
`usercert.pem` and `userkey.pem`, respectively in the directory
`$HOME/.globus`.  Contrary to the usual rules, the command line
parameter for `pem_certificate` is `--pem-cert`. 

### Service Endpoints

The user must also specify the cloud service endpoints.  These will be
provided by the cloud administrator.  

------------------ --------------------------------
`endpoint`         cloud entry point (VM mgt.)
`pdisk_endpoint`   pdisk entry point (storage mgt.)
`marketplace`      Marketplace URL
------------------ --------------------------------

Table: Cloud service endpoints.

If the `pdisk_endpoint` parameter is not specified, then the value for
`endpoint` will be used.  The default value for the `marketplace`
parameter is `https://marketplace.stratuslab.eu/`, the central
StratusLab Marketplace.

### Other Credentials

Various other credentials are used to access running virtual machines
and for signing information for the Marketplace.

----------------------- --------------------------------
`user_public_key_file`  user public SSH key file
`p12_certificate`       PKCS12-formatted certificate
`p12_password`          password for PKCS12 certificate
----------------------- --------------------------------

Table: Other user credentials. 

The default for the public SSH key file is `$HOME/.ssh/id_rsa.pub`.
Contrary to the usual rules, the environmental variable and command
line parameter are `STRATUSLAB_KEY` and `--key`, respectively. 

The PKCS12 certificate is used to sign image metadata entries before
uploading them to the Marketplace.  Contrary to the usual rules, the
command line option for the parameter `p12_certificate` is
`--p12-cert`.

## Multi-cloud Configuration

If more than one StratusLab cloud infrastructure is being used, then
the configuration for all of these can be kept in a single file.  This
allows you to quickly switch between the various clouds.

In the configuration file it is possible to create uniquely named user
specific sections to define any of the variables described
above. Example of 'endpoint'

    [my-section]
    endpoint = <another.cloud.frontend.hostname>
    username = <another.username>
    password = <another.password>

which can be activated using the `selected_section` parameter in the 
`[default]` section of the configuration file:

    selected_section = my-section

It can also be activated using command-line options
`--user-config-section` or `-S` or via the environmental variable
`STRATUSLAB_USER_CONFIG_SECTION`.

This example configuration file defines custom 'fav-cloud' section
with endpoint/credentials and a custom SSH key:

    [fav-cloud]
    endpoint = favorit-cloud.tld
    username = clouduser
    password = cloudpass
    user_public_key_file = /home/clouduser/.ssh/id_rsa-favcloud.pub

Values for parameters not specified in this section will be taken from
the `[default]` section. 

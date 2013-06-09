
# Command Line Client Installation

StratusLab provides a simple command line client that is easy to
install on all platforms. This client provides access to all of the
StratusLab services; it provides a scriptable alternative to access
via a web browser.

After installation and configuration of the client follow the
instructions to [launch a Virtual Machine][launch-vm] and then, read
the in-depth [documentation][docs] on the use of the StratusLab cloud.

## Prerequisites

* Python >= 2.6 and < 3.x
* Java 1.6+
* SSH client
* SSH user key-pair

### Python

Ensure that you have a recent version of Python **>= 2.6**, but <
**3.0** installed on your system. From the command line type

    $ python -V
      Python 2.6.1

See the [python site][python] for Python downloads and instructions.

### SSH Setup

In most of the cases SSH will be used to log into virtual machines
that have been started in the cloud. Ensure that you have SSH public
and private key pair.

#### Linux/UNIX

The private and public keys are usually located under `~/.ssh/` and
have names like `id_rsa` and `id_rsa.pub`.

You can generate them with the following command

    $ ssh-keygen

The default values are appropriate in most cases, but you should
provide a passphrase and not leave it empty.

Verify the generated key pair permissions. The `id_rsa` should have
permissions 0600 (read/write access for owner only) and the
`id_rsa.pub` - 0644 (read access for all; write access for owner).

**Be sure to remember the passphrase that you have used!**

Be careful if an ssh agent is configured by default for your operating
system.  Ensure that it is setup to use the correct key and that it
provides the correct password for that key.

#### Windows

* You need to generate an SSH key pair on a linux or Unix system.
* Import the private key into Putty

To generate an SSH key pair on a linux or Unix system, follow the
above instructions described in the Linux/UNIX part above.  In your
Windows machine, install Putty and PuttyGen from [page][putty-gen].

To import your id_rsa file to Putty:
  1. Start PuttyGen,
  2. Click "Load", and browse to your id_rsa file,
  3. Click "Save private key". Your private key will be saved in the
     format required by Putty.

To log in your virtual machine using Putty:
  1. Start Putty,
  2. In "session" category provide the Host Name or IP address
  3. In Connection/SSh/Auth category, in "Private key for
     authentication" field, browse to your private key.
  4. Open

More information on how to "Connecting to Linux/UNIX Instances from
Windows Using PuTTY" can be found on this [page][amazon-ssh]

## Individual Client Installation

Look for the latest version of the command-line client tarball
`stratuslab-cli-user-*.tar.gz` in the [yum
repositories][yum-repo-centos]. (Even though this is a CentOS
repository, **all of the tarballs and zip files work on all
platforms**.)  Unpack the tarball in a convenient location.

Update your `PATH` and `PYTHONPATH` variables:

    export PATH=$PATH:<install location>/bin
    export PYTHONPATH=$PYTHONPATH:<install location>/lib/stratuslab/python

The above are appropriate for Bourne-type shells. Modify the commands
as necessary if you are using a different shell.

## System Wide Client Installation

These packages only work on RedHat Enterprise Linux systems (and its
derivatives like CentOS and ScientificLinux) and OpenSuSE.  You must
have root access to your machine to install them.

For RHEL and RHEL-like systems, it is recommended to do the
installation with [yum][yum]. The configuration for yum can be found
[here][yum-config], choosing the "centos-6.2" repository.  Execute:

    $ yum install stratuslab-cli-user

to install the latest version of the client tools.

For OpenSuSE, configure zypper for the StratusLab OpenSuSE repository
("opensuse-12.1") and use it to install the package.

## Client Configuration

One has to know at least one endpoint of the StratusLab cloud
deployment and possess valid credentials to access it. Refer to
[StratusLab Reference infrastructure][sl-ref-infra].

### Credentials

For working with user command-line tools one needs to possess the
following credentials (requirement depends on use-case and utility
used)

    username/password of the account in StratusLab Cloud frontend(s)
    X509 key/pair
    Globus/VOMS proxy
    PKCS12 certificate

For more see [documentation on user credentials][user-creds-docu].

### Configuration file

Configuration file should contain definitions of StratusLab services
endpoints and credentials required for the user client. For example

    endpoint = cloud.lal.stratuslab.eu
    pdisk_endpoint = pdisk.lal.stratuslab.eu
    username = clouduser
    password = cloudpass
    pem_certificate = /Users/localuser/.globus/usercert.pem
    pem_key = /Users/localuser/.globus/userkey.pem

#### Linux/UNIX

By default the user client expects the configuration file at
`$HOME/.stratuslab/stratuslab-user.cfg`.

The client ships with a reference configuration file

* in tarball &lt;install location&gt;/conf/stratuslab-user.cfg.ref 
* in RPM /etc/stratuslab/stratuslab-user.cfg.ref 

User has to copy the file to the default location
`$HOME/.stratuslab/stratuslab-user.cfg` and modify it following
explanations to the variables in the file. The variables that are
commented out (e.g. p12_certificate) take their default values from
the code.

#### Windows

By default the user client expects the configuration file at
`%HOMEDRIVE%%HOMEPATH%\.stratuslab\stratuslab-user.cfg`.

    mkdir %HOMEDRIVE%%HOMEPATH%\.stratuslab

The reference configuration file 

* in zip package `<install location>\conf\stratuslab-user.cfg.ref`

User has to copy the file to the default location
`%HOMEDRIVE%%HOMEPATH%\.stratuslab\stratuslab-user.cfg` and modify it
following explanations to the variables in the file. The variables
that are commented out (e.g. p12_certificate) take their default
values from the code.


## Providing user parameters

The following are they means of providing user parameters and
presented in the order of precedence

  * via command line parameters
  * via environment variables (STRATUSLAB_\*)
  * in configuration file (by default:
    HOME/.stratuslab/stratuslab-user.cfg)
  * default in the code

## Cloud Endpoint

Used for VMs instantiation. There is no default value for the endpoint in the 
code.

Command-line parameters

    --endpoint
    --username
    --password

Environment variables

    STRATUSLAB_ENDPOINT
    STRATUSLAB_USERNAME
    STRATUSLAB_PASSWORD

Configuration file

    endpoint
    username
    password

## Persistent Disk service

Used for manipulation machine and disk images (create, delete, 
hot-attach/detach) in the Persisten Disk storage service. There is no default 
for PDisk endpoint in the code. If not provided, the client will use the value 
of 'endpoint'.

Command-line parameters

    --pdisk-endpoint
    --pdisk-username
    --pdisk-password

Environment variables

    STRATUSLAB_PDISK_ENDPOINT
    STRATUSLAB_PDISK_PROTOCOL  # default 'https'
    STRATUSLAB_PDISK_PORT      # default '8445'
    STRATUSLAB_PDISK_USERNAME
    STRATUSLAB_PDISK_PASSWORD

Configuration file

    pdisk_endpoint
    pdisk_port

NB! If 'pdisk-username' and/or 'pdisk-password' are not provided by any of the 
means the ones defined for 'endpoint' will be used.

## Marketplace

Marketplace endpoint to be used when uploading metadata, and/or when creating 
instance of VM.

Default value for Marketplace endpoint in the code

    https://marketplace.stratuslab.eu

Command-line parameter

    --marketplace-endpoint

Environment variable

    STRATUSLAB_MARKETPLACE_ENDPOINT

Configuration file

    marketplace_endpoint

## Private/Public keys

### SSH

SSH public key file. Used at VM instantiation. The public key is pushed to the 
VM to enable password-less SSH access. Default: **HOME/.ssh/id_rsa.pub**.

Command-line parameter

    --key
    
Environment variable

    STRATUSLAB_KEY

Configuration file

    user_public_key_file

### PEM

Used to authenticate to the cloud endpoint. If you want to use authentication 
with grid certificate and if username/password and PEM key/cert are both 
enabled, PEM key/cert takes precedence.

Default: **HOME/.globus/userkey.pem**, **HOME/.globus/usercert.pem**

Command-line parameters

    --pem-cert
    --pem-key

Environment variables

    STRATUSLAB_PEM_CERTIFICATE
    STRATUSLAB_PEM_KEY

Configuration file

    pem_key
    pem_certificate

### PKCS12

PKSC12-formatted certificate, for signing and validating image metadata.

Default: **HOME/.globus/usercert.p12**

Command-line parameters

    --p12-cert
    --p12-password

Environment variables

    STRATUSLAB_P12_CERTIFICATE
    STRATUSLAB_P12_PASSWORD

Configuration file

    p12_certificate
    p12_password

## Custom sections in configuration file

In the configuration file it is possible to create uniquely named user specific 
sections to define any of the variables described above. Example of 'endpoint'

    [my-section]
    endpoint = <another.cloud.frontend.hostname>
    username = <another.username>
    password = <another.password>

which can be activated using the **selected_section** parameter in the 
**default** section of the configuration file

    selected_section = my-section

or using command-line option
 
    --user-config-section or -S

or using environment variable

    STRATUSLAB_USER_CONFIG_SECTION

Example. Configuration file defines custom 'fav-cloud' section with 
endpoint/credentials and custom SSH key

    [fav-cloud]
    endpoint = favorit-cloud.tld
    username = clouduser
    password = cloudpass
    user_public_key_file = /home/clouduser/.ssh/id_rsa-favcloud.pub

Launch a ttylinux image on the cloud endpoint defined by 'fav-cloud' section

    stratus-run-instance -S fav-cloud $TTYLINUX_IMAGE


[python]: http://python.org/
[yum]: http://yum.baseurl.org/
[yum-config]: http://yum.stratuslab.eu/
[yum-repo-centos]: http://yum.stratuslab.eu/releases/centos-6.2/
[amazon-ssh]: http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/putty.html
[docs]: /documentation/
[sl-ref-infra]: /try/2012/12/04/try-reference-cloud-infrastructures.html
[launch-vm]: /try/2012/01/01/try-launch-vm.html
[user-creds-docu]: /documentation/2012/10/05/docs-tutor-user-credentials.html
[putty-gen]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

SSH Client
==========

Nearly always, virtual machine instances are accessed remotely via an
SSH (secure shell) session. This requires that the user has an SSH
client installed and has generated an SSH key pair.

Linux Operating Systems
-----------------------

Client Installation
~~~~~~~~~~~~~~~~~~~

An SSH client, usually `OpenSSH <www.openssh.org>`__, comes as a
standard part of all Linux operating systems. It is normally installed
by default, but can be installed via the distribution's package
management system if necessary.

Creating an SSH Key Pair
~~~~~~~~~~~~~~~~~~~~~~~~

To use the client to connect to virtual machine instances, the user must
have an SSH key pair consisting of a "public" key and a "private" key.
These keys are usually located in the ``~/.ssh/`` directory and have
names like ``id_rsa`` (private) and ``id_rsa.pub`` (public).

You can generate them with the following command

::

    $ ssh-keygen

The default values are appropriate in most cases, but you should provide
a passphrase and not leave it empty.

Verify the generated key pair permissions. The ``id_rsa`` should have
permissions 0600 (read/write access for owner only) and the
``id_rsa.pub`` should have permissions 0644 (read access for all; write
access for owner).

**Be sure to remember the passphrase that you have used!** This
passphrase can (and usually is) different from the password for the
user's account.

SSH Agent
~~~~~~~~~

SSH agents allow users to provide the passphrase once per session,
caching the passphrase and providing it automatically after the first
request. This makes use of SSH more convenient when multiple connections
are being made to a virtual machine.

Some operating systems start an SSH agent automatically when a user logs
in. If this is the case, be sure that the agent uses the correct key and
the correct password for that key.

You can check if an SSH agent is running by looking at the
``SSH_AGENT_PID`` variable.

::

    $ printenv SSH_AGENT_PID

If this isn't empty, then the agent is running. You can add your key to
the agent with the command:

::

    $ ssh-add

Providing the passphrases for your keys when prompted for them. See the
manpages for ``ssh-agent`` and ``ssh-add`` for more information.

Mac OS X
--------

Client Installation
~~~~~~~~~~~~~~~~~~~

The SSH client is a standard part of Mac OS X. No installation is
necessary.

Generating an SSH Key Pair
~~~~~~~~~~~~~~~~~~~~~~~~~~

The commands and procedure are the same as for the Linux operating
systems. Follow the instructions there.

SSH Agent
~~~~~~~~~

An SSH agent is started automatically when logging into the machine. It
will automatically ask for your passphrase the first time and then
remember it for all future sessions.

Windows
-------

Client Installation
~~~~~~~~~~~~~~~~~~~

Windows does not ship with an SSH client, so you must install one.
Although there are some other solutions (especially for recent versions
of Windows), most people install and use PuTTY. Binaries and
installation instructions can be found on the `PuTTY
website <http://www.chiark.greenend.org.uk/~sgtatham/putty/>`__.

It is recommended to install the full PuTTY distribution, but at a
minimum the ``putty`` and ``puttygen`` executables need to be available.

Generating an SSH Key Pair
~~~~~~~~~~~~~~~~~~~~~~~~~~

To use PuTTY with the cloud machines, you must generate a certificate.
The most recent version of PuTTY allows you to do this on your machine,
using the executable PuTTYGen.

In the PuTTYGen interface do the following:

-  Click "generate".
-  Provide key passphrase if you want
-  Click "save public key" to save as file (e.g. in the .ssh folder)
-  Click "save private key" to save as file (e.g. in the .ssh folder)
-  Copy the text in the "Public key for pasting into OpenSSH..." box to
   the clipboard
-  Save this text in the file $HOME/.ssh/id\_rsa.pub as a **plain text
   file**

Logging into a VM with PuTTY
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To log in your virtual machine using PuTTY:

1. Start PuTTY,
2. In the "session" category provide the hostname or IP address
3. In Connection/SSH/Auth category, in "Private key for authentication"
   field, browse to your private key.
4. Open

Be sure to login with the correct username for the virtual machine; this
is nearly always "root".

If you are using X11 for graphical interfaces, you must also check the
following:

-  Connection/SSH/Auth panel: click "Allow agent forwarding" and select
   the private key file you saved above
-  Connection/SSH/X11 panel: click "Enable X11 forwarding"

The X11 server on your machine must be started before making the
connection to the virtual machine.

More information on how to "Connecting to Linux/UNIX Instances from
Windows Using PuTTY" can be found in the `Amazon
documentation <http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/putty.html>`__.

SSH Agent
~~~~~~~~~

PuTTY supports the SSH agent functionality through the Pageant
executable.

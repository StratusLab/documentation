
# Prerequisites

Before trying to follow this tutorial you must have an account on a
StratusLab cloud infrastructure and you must have the StratusLab
command line client installed on your laptop or workstation.  The
network you are using must also allow access to the ports used by the
StratusLab services. 

## Registration

If you are planning to use the StratusLab Reference Cloud
infrastructure, then you can follow the procedure below.  If not, you
must contact the cloud administrator for your cloud to see how to
obtain an account.

To register on the StratusLab Reference infrastructure please go to
the [registration page][registration].  Click on the "register" link
and you should see a page like the one in the screenshot.

![Marketplace Screenshot](images/registration-screenshot.png)

For user authentication, either a username/password pair or an
[IGTF][igtf] accredited digital certificate.  For the purposes of this
tutorial the username/password is easier.  You can ignore the optional
X500 field needed when using a digital certificate.

Fill in the form, agree to the terms and conditions, and then client
the "create" button.  Although not mandatory, it is useful to provide
a short message describing your use of the cloud, the
scientific/engineering domain of your application, and your institute
(if any).

You will receive an email when your account has been approved by the
administrator.

## Client Prerequisites

Before starting, you must verify that the prerequisites for the
StratusLab command line are satisfied:

  * Python 2 (2.6+), virtualenv and pip are installed.
  * Java 1.6 or later is installed.
  * An SSH client is installed with an SSH key pair.

More information on installing these dependencies can be found in the
complete [Users Guide][users-guide].

## Network

The network that you are using must allow you to contact the
StratusLab services.  This requires that the following ports are open
for outbound access:  80 (http), 443 (https), 2633 (OpenNebula), 8444
(registration svc.), and 8445 (storage svc.).


[registration]: https://register.stratuslab.eu:8444
[igtf]: http://www.igtf.net/
[users-guide]: http://stratuslab.eu/release/13.05.0/users-guide/users-guide.html


# Couchbase

The StratusLab architecture uses a distributed database at the core to
hold the full state of the cloud.  This simplifies all of the other
components of the system by allowing them to stateless and by acting
as a means for coordinating the components of the system.

StratusLab uses [Couchbase][couchbase] for this distributed database.
It was chosen because of its ability to store and to index
JSON-formatted documents efficiency, matching well the needs of
managing cloud resources through the CIMI data model.  It is easy to
deploy for small database, while retaining the ability to scale to
very large systems.

Because all other StratusLab components rely on having access to the
Couchbase database, it is the first component that must be installed. 

## Installation Overview

Couchbase is available as a standard RPM package from the Couchbase
website.  The company distributes two versions of the database from
their website: a "community" version and an "enterprise" version.

The community version is open source and released under the Apache 2
license (the same as for StratusLab).  StratusLab uses this version
for all of its own deployments and tests.  For convenience, the RPM
package for Couchbase is included in the StratusLab yum repository.

The enterprise version requires the purchase of a commercial license
for anything other than small test deployments.  However the
commercial support for deployment problems and software bugs may be
interesting for those deploying mission-critical cloud
infrastructures.  StratusLab should work with the enterprise version,
but does not require it.

The installation of Couchbase consists of installing the RPM package
and then initializing a Couchbase cluster.  The StratusLab
installation commands automate the process.

## Installation and Configuration

The standard StratusLab deployment installs a Couchbase instance on
each of the "CIMI Interface" nodes.  The instances on these nodes form
the Couchbase cluster for the full StratusLab cloud.  The minimal
deployment has only one "CIMI Interface" node and hence only one
Couchbase instance.

Production StratusLab deployments should have more than one Couchbase
instance in the Couchbase cluster for reliability and redundancy.
Although the default location for these instances is on the "CIMI
Interface" nodes, you can deploy them on dedicated nodes or other
cloud services nodes.

Log into the node that will function as the "CIMI Interface" node for
your cloud infrastructure as "root".  Verify that all of the
prerequisites in detailed in the previous chapters are satisfied.

Install the StratusLab system administrator command line interface
(CLI).  This installs the commands that simplify and automate the
installation of all of the StratusLab components.  This should be as
simple as:

    $ yum install stratuslab-cli-admin

Once this completes, you should have a set of StratusLab commands in
your path.  All of the StratusLab commands start with the prefix
`stratus-`. 

We will now use the command `stratus-install` to install and
initialize the Couchbase database.

    $ stratus-install --couchbase-server

This should install the necessary packages and then setup the
database.  The installation creates an administrator account for the
database called 'admin'.  The randomly generated password for this
account is available in `/opt/couchbase/cluster-password.txt`.

If you wish, you can use the Couchbase web interface to view the
current status of the cluster.  Connect to the port 8091 on this
machine with a web browser.  Use the administrator username and
password to log in.

> **NOTE**: By default, this web interface is only available from the
> localhost.  If you want to connect from outside of the machine, then
> you will need to setup an SSH tunnel to access the web interface.

If the installation worked correctly, you will now have a running
Couchbase cluster (of one node!).  You are ready to install the CIMI
service on this machine.


[couchbase]: http://couchbase.com


# Architecture

The architecture of the StratusLab software has evolved to make the
overall system more scalable and more robust.  At the center of the
system is a distributed database that removes single points-of-failure
in the system and permits redundant deployments of StratusLab service
components for better reliability.

StratusLab also now exposes the [CIMI interface][cimi]--a standard
from [DMTF][dmtf] that provides a coherent, unified API for all of the
StratusLab cloud resources and that follows the usual REST patterns.
It simplifies programmatic access to the cloud and provides a good
foundation for browser-based access.

## StratusLab Components

The diagram shows a high-level view of the various components in the
StratusLab architecture.  The distributed database is at the core of
the system.  On the user-facing side are machines that provide the
CIMI interface to the system.  On the service side are a set of
controllers for different types of resources (storage, VMs, etc.).
Behind the controllers are the physical resources used by the cloud.

![StratusLab Architecture](images/stratuslab-architecture.png)

The distributed database contains the complete state of the cloud.
Consequently, the CIMI interfaces and the various controllers can be
completely stateless, allowing redundant instances to be deployed as
necessary.

As all of the communication between controllers takes place through
the database, specialized controllers can be added to the cloud
infrastructure easily, such as:

  * Different type of storage (backed up, shared, fast, etc.)
  * Support for Linux containers as well as virtual machines
  * Dynamic network configurations

The controllers react to jobs and resources in the database, and
update those entries when completing tasks or making adjustments to
resources. 

## Service Configuration

Generally, the configuration of the StratusLab cloud services also
resides within the database.  This allows deployment of redundant
services while maintaining a consistent configuration of those
services across machines.

The configuration information within the database is split into a
series of JSON-formatted documents.  Each document has an document
identifier of the form `ServiceConfiguration/service-instance`, where
the "service" corresponds to the name of the service and the
"-instance" is optional and used only if the configuration of an
instance differs from the general service configuration.

Most configuration exists within the database, but there are two
notable exceptions: third-party services and the Couchbase access
parameters. 

To minimize the StratusLab development effort, third-party services
used by StratusLab are not modified to use the database.
Consequently, these continue to use configuration files on the local
file system.  One example are the certificate authorities used for PKI
authentication in the European Grid Infrastructure.  When deploying
multiple service instances, these files must be coordinated between
physical machines. 

The StratusLab services must discover the contact parameters for the
Couchbase database.  This information resides in a file on each node
(`/etc/stratuslab/couchbase.cfg`).  If this file doesn't exist, then
services will use the default bucket and assume that the local machine
is a member of the Couchbase cluster.

The standard "ini" format is used for the file:

    [DEFAULT]
    host=localhost
    bucket=default
    username=default
    password=

    [service]
    config=ServiceConfiguration/service-instanceX

This example shows the default values.  You can define a different
bucket and location for the database.  However, the same database must
be shared by all of the StratusLab cloud services.  **If changes are
made to this file, then the StratusLab service must be restarted.**

The example also shows the possibility of providing instance-specific
configuration documents for services.  Generally, these should not be
needed but are available for allow for specialized service
configurations. 

Despite the exceptions, having the majority of configuration
information in the database will make configuring and maintaining the
services simpler for system administrators.  Generally, it is just a
matter of updating a document in the database to change the behavior
of the cloud infrastructure.


[cimi]: http://dmtf.org/sites/default/files/standards/documents/DSP0263_1.0.1.pdf
[dmtf]: http://dmtf.org

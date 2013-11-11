
# Architecture

The architecture of the StratusLab software has evolved to make the
overall system more scalable and more robust.  At the center of the
system is a scalable, distributed database that removes single
points-of-failure.  By allowing the other components to become
'stateless', multiple redundant instances can be deployed to improve
reliability.

In addition, StratusLab now exposes the CIMI interface--a standard
from DMTF that provides a coherent, unified API for all of the
StratusLab cloud resources and that follows the usual REST patterns.
This will simplify programmatic access to the cloud and provide a good
basis for browser-based access in the future.

## StratusLab Components

The diagram shows a high-level view of the various components in the
StratusLab architecture.  The distributed database is at the core of
the system.  On the user-facing side are machines that provide the
CIMI interface to the system.  On the service side are a set of
controllers for different types of resources (storage, VMs, etc.);
behind them are the physical resources used by the cloud.

![StratusLab Architecture](images/stratuslab-architecture.png)

The distributed database contains the complete state of the cloud.
Consequently, the CIMI interfaces and the various controllers can be
completely stateless, allowing redundant instances to be deployed as
necessary.

The standard deployment, combines the CIMI interfaces and the
Couchbase instances on physical nodes.  Similarly, the resource
controllers and the underlying resources usually reside on the same
physical node.  Because all of the communication takes place through
the database, the actual mapping of services to physical nodes is
quite flexible and can be adapted to a data center's constraints.

## Service Configuration

In general, the configuration of the StratusLab cloud services also
resides within the database.  This allows deployment of redundant
services while maintaining a consistent configuration of those
services across machines.

There are, however, a couple of exceptions.  The first is the
configuration file containing the access parameters for the database.
This is a file on each node (`/etc/stratuslab/couchbase.cfg`),
allowing each service to find the database.  If this file doesn't
exist, then services will use the default bucket and assume that the
local machine is a member of the Couchbase cluster.  

The second exception consists of the configuration of third-party
services and utilities that StratusLab doesn't control.  One example
are the certificate authorities used for PKI authentication in the
European Grid Infrastructure; these are files on the local file system
and not in Couchbase.

Despite the exceptions, having the majority of configuration
information in the database will make configuring and maintaining the
services simpler for system administrators.


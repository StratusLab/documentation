---
layout: article
title: StratusLab Architecture Proposal
category: review
---

The current StratusLab architecture, centered on OpenNebula, has
worked well.  It allowed StratusLab to quickly develop a complete
cloud distribution that is both straightforward to install on
commodity hardware and to use.  Although a centralized architecture,
it has scaled well from deployments with a few physical machines to a
few tens of machines.  There are also limited deployments of
OpenNebula alone that are larger. 

However, we are already starting to see load and scalability issues
with the current system at LAL, especially when running tutorials with
a significant number of users simultaneously instantiating virtual
machines.  Moreover, much larger deployments of StratusLab with
100-1000 physical machines are expected in the near future.

Given this expected change in scale (of both installations and users),
now is a good time to revisit the overall StratusLab architecture.


Centralized Architecture
------------------------

Systems with a centralized architecture provide a single "address" for
both users and system administrators.  The entire state of the system
is available from a single location allowing easier coodination of
tasks and simplier implementation of a service by developers.

However, the central point (i.e. the OpenNebula frontend for
StratusLab) can become a bottleneck when faced with a large number of
user requests and/or a large number of resources to coordinate.  The
scalability problem can be avoided, to a limited extent, by providing
a larger physical machine for the service.  The grid's Workload
Management System (WMS) provides a concrete example of this technique
along with the inherent limitations.  


Distributed Architecture
------------------------

A fully-distributed architecture that avoids hotspots and single
points-of-failure fundamentally solves the scalability issue.  However
such solutions are notoriously complicated because of the need to
spread the state of the system between machines while maintaining
coherency for the service as a whole.

Nonetheless, if StratusLab is going to scale to allow deployments with
the number of users and physical machines expected in the near future,
a fully-distributed architecture needs to be adopted.


General Proposal
----------------

How can StratusLab adopt a fully distributed architecture while
maintaining a simple, maintainable distribution and with limited
development effort?

One answer is to use an existing distributed database as the backbone
of the overall system.

The overall architecture for StratusLab then becomes very simple (see
diagram below).  The number of service frontend nodes can be easily
scaled as the number of requests increases (using a simple DNS
round-robin technique or with algorithms built into the clients).
Similarly the number of hypervisors in the system can be simply added
to the system as the need for more resources grows.

![Architecture Skeleton][arch]

The database acts as a system bus allowing machine state information
to be managed as well as ancillary data like monitoring and accounting
information. 

Of course, this is a cheat because all of the complexity has been
lumped into the distributed database.  For StratusLab with its limited
number of developers, designing such a database from scratch would be
an insurmountable hurdle.  Fortunately, many such databases exists and
StratusLab can use them "off the shelf" to avoid the effort of
building a reliable, fully-distributed database itself.  So although
this solution is a cheat, it is a good cheat! 


Getting Concrete
----------------

One good system is Cassandra.  It is known to scale well, is fairly
straightforward to configure and to install, and has language bindings
for the languages common in Stratuslab (Java and Python).  The
implementation is mature and stable as evidenced by the large number
of communities using the system and the large scale deployment of
Twitter. 

The new frontend would actually be very similar to the current
authentication proxy.  This would provide the user-facing API (CIMI in
the future) satisfying user requests by interacting with information
in the database.  As with the authentication proxy, all of the
system's authentication would happen within the frontend (possibly
interacting with external services like LDAP, Shibboleth or OAuth).

The hypervisors would contain a set of thin wrappers around the
libvirt library to allow control of virtual machines.  These wrappers
would react to changes in the distributed database to deploy and to
control virtual machines on a physical machine.  The hypervisors would
also contain wrappers to collect monitoring and accounting
information, pushing that information into Cassandra for consumption
by the frontend (or other specialized services).  These latter wrapper
are similar to the current monitoring scripts provided by OpenNebula,
but would avoid the complexities of using SSH to distribute and run
them.

Where to deploy Cassandra itself is an open question.  It would be
possible to deploy Cassandra instances on the hypervisor nodes, on the
frontend nodes, or on both.  This solution minimizes the number of
physical machines in the system.  Another possibility would be to
deploy Cassandra instances on dedicated machines.  Initial deployments
should focus on simple solutions (e.g. Cassandra instances on the
hypervisors only).  More complicated deployments can be tested as
necessary in the future.

Replacing OpenNebula
--------------------

There is a fundamental architectural incompatibility between the
centralized OpenNebula model and the one proposed here.  Consequently,
OpenNebula will likely have to be replaced.

Already StratusLab makes heavy use of libvirt through OpenNebula.
Recent releases of libvirt support many different hypervisors
(although StratusLab currently only uses KVM) and allow the direct
control of external iSCSI, LVM, and NFS file-based volumes.  In short,
the libvirt view of virtual machine management is actually closer to
what StratusLab does than OpenNebula.  It also provides both python
and Java APIs, so integration with other StratusLab services should be
straightforward.

OpenNebula provides A "scheduler" or "placement service" to decide
where to instantiate virtual machines.  There is no equivalent service
with libvirt.  Consequently if OpenNebula is removed, then a placement
service would need to be found elsewhere or developed.  This is an
important point that must be resolved before moving to an architecture
based on Cassandra.


Conclusions
-----------

Adopting a distributed architecture would allow StratusLab to scale to
the number of users and hypervisors expected in the near future.  This
offloads much complexity to Cassandra while allowing our own system to
be simplified.  Moving in this direction would require a significant
number of changes in the current system, but some at least could be
implemented incrementally.  All of the competence exists within the
current set of developers, except for the "placement service", which
would require some more investigation. 


[arch]: http://stratuslab.eu/diagrams/stratuslab-architecture-skeleton.png

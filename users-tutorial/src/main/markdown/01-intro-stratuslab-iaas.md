
# Introduction to StratusLab

StratusLab, an international collaboration, provides a complete, open
source cloud distribution, allowing the installation of public or
private "Infrastructure as a Service" cloud infrastructures.  It aims
to be both simple to use and simple to install.

Cloud infrastructures provide many benefits to end-users (scientists
and engineers), developers, end-users, administrators, and resource
providers.  End-users appreciate the cloud's ability:

  * To provide a customized execution environment, 
  * To make available pre-installed and pre-configured applications,
  * To provision new resources (CPU, storage, etc.) rapidly, and
  * To allow complete control of those resources. 

Application and service developers also appreciate:

  * Easy access to the services through simple APIs and 
  * The elasticity of the cloud to respond to peaks in demand.

Administrators benefit from:

  * More flexible management of machines and services and
  * Separation of hardware, service, platform and user services

and resource providers like:

  * Better utilization of shared computing resources and
  * Possibility of federating resources with other clouds.

StratusLab provides several ways to access the cloud resources
including a command line client, web browser interfaces, and various
APIs.

## Services

StratusLab provides the compute, storage, and network resources
expected from Infrastructure as a Service (IaaS) cloud providers.  It
also provides image management tools that allow trusted sharing of
virtual machine appliances.  

The compute services focus on the fast provisioning of VMs and low
latency start-up.  They also support a variety of contextualization
mechanisms (HEPiX, OpenNebula, and CloudInit) to allow users to
configure parameterized machine images.  OpenNebula is currently at
the core of the VM management for StratusLab.

The storage service provides persistent data volumes to users.  These
volumes can be created and destroyed independently of any particular
virtual machine.  These volumes can be attached or detached either
when a virtual machine starts or dynamically after the machine is
already running.  Volatile and read-only storage is also provided. 

Users can select whether their machine needs a public or private IP
address and they can use the standard firewall services within the VM
to further protect the machine if necessary.  The StratusLab services
use the standard networking services (DHCP, DNS, etc.) that are likely
already in place at a site.

For StratusLab, the Marketplace is the core of the image handling
mechanisms.  It serves as a registry where image creators can
publicize their images and where users can find existing ones.  Site
administrators can use the registry information to enforce a policy on
which images can be run on a their cloud infrastructure.

## Tools

The distribution also contains tools to make accessing and using
StratusLab services easier.  The client command line interface (CLI)
allows users to access all of the StratusLab resources.  The
administrator CLI facilitates and automates the installation of the
StratusLab services.  Both are written in portable Python and can be
easily installed.

StratusLab also provides several APIs for programmatic access to the
services.  There is a set of native python libraries (used also by the
CLI) that can be used to talk to the raw XML-RPC and REST service
interfaces.  A more stable interface is provided by the Libcloud
plugin for StratusLab. 

## Roadmap

The StratusLab cloud distribution is in active development with new
releases approximately every three months.  The major improvements
expected over the next few releases include:

  * Interfaces: CIMI REST interface as a standard for all of the
    StratusLab services and afterwards a complete, web-accessible
    interface. 
  * Simplicity, Scalability & Robustness: Direct use of libvirt as the
    VM manager and use of Couchbase as an information bus between
    elements in the new, simplified architecture. 
  * Better administrator services:  Improved overview and monitoring
    of cloud activity, fine-grained accounting, and VM migration
    control. 

These changes will benefit both the cloud administrators and cloud
users. 

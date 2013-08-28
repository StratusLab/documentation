
# Introduction to StratusLab

[StratusLab][stratuslab], an international collaboration, provides a
complete, open source cloud distribution, allowing the installation of
public or private "Infrastructure as a Service" cloud infrastructures.
It aims to be both simple to use and simple to install.

Cloud infrastructures provide many benefits to end-users (U),
developers (D), administrators (A), and resource providers (R).

- -------------------------------------------------------------------
U To provide a customized execution environment
U To make available pre-installed and pre-configured applications
U To provision new resources (CPU, storage, etc.) rapidly
U To allow complete control of those resources
D Easy access to the services through simple APIs
D The elasticity of the cloud to respond to peaks in demand
A More flexible management of machines and services
A Separation of hardware, service, platform and user services
R Better utilization of shared computing resources
R Possibility of federating resources with other clouds
- -------------------------------------------------------------------

Table: Cloud Benefits

## Services

StratusLab provides the compute, storage, and network resources
expected from Infrastructure as a Service (IaaS) cloud providers.  It
also provides management tools that allow trusted sharing of virtual
machine appliances, VM images with preinstalled and preconfigured
applications and services.

The **compute services** focus on fast provisioning of Virtual
Machines (VMs) and low latency start-up.  They also support a variety
of contextualization mechanisms (HEPiX, OpenNebula, and CloudInit) to
allow users to create and to use parameterized appliances.
[OpenNebula][opennebula] is currently at the core of the VM management
for StratusLab.

The **storage service** provides persistent data volumes.  These
volumes can be created and destroyed independently of any particular
virtual machine.  They can be attached or detached either when a
virtual machine starts or dynamically after the machine is already
running.  Volatile and read-only storage are also provided.

**Network connectivity** is crucial for any remote resource.  Users
can select whether their machine needs a public or private IP address
and they can use the standard firewall services within the VM to
further protect the machine if necessary.  StratusLab make use of
standard networking services (DHCP, DNS, etc.) that are likely already
in place at a site.

The Marketplace is the core of the **appliance handling mechanisms**.
It serves as a registry where image creators can publicize their
images and where users can find appropriate ones.  Site administrators
can use the registry information to define and to enforce a policy
that selects which appliances can be run on a their cloud
infrastructure.

## Tools

The distribution also contains tools to make accessing and using
StratusLab services easier.  The client command line interface (CLI)
allows users to access all of the StratusLab resources.  It is written
in portable Python and runs on all Linux distributions, Mac OS X, and
Windows.

StratusLab also provides several APIs for programmatic access to the
services.  There is a set of native Python libraries (used also by the
CLI) that can be used to talk to the raw XML-RPC and REST service
interfaces.  A higher-level and more stable interface is provided by
the [Libcloud][libcloud] plugin for StratusLab.

## Roadmap

The StratusLab cloud distribution is in active development with new
releases approximately every three months.  The major improvements
expected over the next few releases include:

  * **Interfaces**: CIMI REST interface as a standard for all of the
    StratusLab services and afterwards a complete, web-accessible
    interface.
  * **Simplicity, Scalability & Robustness**: Direct use of libvirt as
    the VM manager and use of Couchbase as an information bus between
    elements in the new, simplified architecture.
  * **Better Administrator Tools**: Improved overview and monitoring
    of cloud activity, fine-grained accounting, and VM migration
    control.

These changes will benefit both the cloud administrators and cloud
users. 


[stratuslab]: http://stratuslab.eu/
[opennebula]: http://opennebula.org/
[libcloud]: http://libcloud.apache.org/

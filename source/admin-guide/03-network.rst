Network Configuration
=====================

The StratusLab network services do not make dynamic changes to the
network configuration, greatly simplifying the integration of the
cloud into the local computing environment.

Network Services
----------------

The StratusLab network configuration relies on standard networking
services and resources already available on your site.  

IP Addresses
~~~~~~~~~~~~

The StratusLab configuration parameters allow for three classes of IP
addresses: public, local, and private.  The public IPs are accessible
from the WAN, local IPs are only visible within the cloud
infrastructure itself, and private IPs are only visible on the
physical machine hosting the virtual machine.

Each class you want to configure must have an associated range of free
IP addresses.  The number of addresses for each range must be large
enough to cover the number of virtual machines you expect to be
running simultaneously.

At least one class of IP addresses must be configured.  This is
usually the "public" class, which the StratusLab client uses by
default. 

DHCP
~~~~

There must be a DHCP server available that will assign IP addresses to
virtual machines when they start.  This DHCP server maintains a
mapping between the MAC addresses (managed by OpenNebula) and the IP
addresses.  The DHCP server must be configured to send all of the
standard networking parameters (gateway, broadcast, domain, etc.) to
the DHCP clients.

DNS
~~~

All of the IP addresses for the virtual machines must have associated
hostnames.  These hostname must be registered in a DNS server, where
both forward and reverse lookups work correctly. 

Isolation
---------

A StratusLab cloud can work correctly with a single physical network
shared between virtual machines and the physical machines running the
StratusLab services.  For a private cloud, this is probably
sufficient. 

For clouds with a larger user base, it is better to isolate the
network for the virtual machines from that for the physical machines.
This can either be done with VLANs or by physically segmenting the
network (assuming that you have multiple network interface cards).  

For large cloud infrastructures, it is also worthwhile to segment the
traffic to the storage service.  This reduces interference between
high-bandwidth data access and the lower-bandwidth for control
messages.

Services and Ports
------------------

All of the StratusLab services now sit behind a nginx web proxy.  This
allows all of the services on a particular machine to share port 443
as well as the server certificate.  This also means that port 443 is
the only port that must be open to the WAN.

Nonetheless, it is useful to know what ports and users are used by
the StratusLab services when debugging problems.  The following table
summarizes those ports.  The name of the `init.d` script to control
the service is the same as the name in the first column.

+-------------------+----------+----------------+
| oned (OpenNebula) | oneadmin | localhost:2634 |
+-------------------+----------+----------------+
| cimi              | slcimi   | localhost:9200 |
+-------------------+----------+----------------+
| registration      | slreg    | localhost:9202 |
+-------------------+----------+----------------+
| marketplace       | slmkpl   | localhost:9204 |
+-------------------+----------+----------------+
| pdisk             | root     | localhost:9206 |
+-------------------+----------+----------------+
| one-proxy         | slauth   | localhost:9208 |
+-------------------+----------+----------------+

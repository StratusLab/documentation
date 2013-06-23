
# Introduction

Describe all of the services and how they fit together in detail.
Provide a detailed view of a cloud deployment.

How StratusLab fits into the various cloud service models.  It is a
IaaS! 

Different types of deployments: public, private, and community. 

## Organization

The guide starts with this introduction and then is followed by an
installation tutorial showing all of the steps to install a minimal,
two-machine StratusLab cloud infrastructure.  The subsequent chapter
provide more detail on various aspects of the software. 

## Terminology

The older terminology, still used in most parts of this (and other)
documents is the following:

  * Frontend: A physical machine that runs the services for virtual
    machine management and storage services.  This is the machine
    which is contacted by the cloud users for managing their cloud
    resources.
  * Node: A physical machine that hosts virtual machines.

The newer terminology that has been adopted is:

  * Cloud Entry Point: One or more physical machines that run the CIMI
    service.  Users use this management interface to acquire, control,
    and release all of their cloud resources.

  * VM Host: One or more physical machines that host virtual
    machines. 

  * Storage Controller: One or more physical machines that act as a
    gateway to a storage services.  

  * Couchbase Node: A physical machine that hosts a Couchbase instance
    running as part of a Couchbase cluster.  This may be combined with
    either the Cloud Entry Points or the VM Hosts to minimize the
    number of physical machines used for the supporting services of
    the cloud.

The software and documentation is being gradually updated to
consistently use this newer terminology.


# Virtual Machine Lifecycle

How to launch, connect to and terminate a Virtual Machine on
StratusLab Cloud.

Prerequisites
-------------

* StratusLab cloud credentials (e.g. on [StratusLab Reference
  infrastructure][ref-infra])
* StratusLab [user client installed and
  configured][user-client-install]

Virtual Machine Lifecycle consists of the following commands:

    Deploy: stratus-run-instance Marketplace_ID
    Describe: stratus-describe-instance VM_ID
    Connect: ssh root@134.158.75.xxx  OR stratus-connect-instance VM_ID
    Terminate: stratus-kill-instance VM_ID

The detailed lifecycle of a machine is more complicated.  The diagram
shows the full lifecycle and describes what is happening behind the
scenes in each of these cases.

![Virtual machine timeline and states.](images/vm-timeline.png)

## Deploy

[Configure][user-client-config] Cloud endpoint and credentials in the
configuration file (default: `$HOME/.stratuslab/stratuslab-user.cfg`).

Search for an image in [StratusLab Marketplace][marketplace] and copy
the image **identifier**. Say we searched for **ttylinux** and chose
**[BtSKdXa2SvHlSVTvgFgivIYDq--][ttylinux-img]**.

Launch an instance of the image

    $ stratus-run-instance BtSKdXa2SvHlSVTvgFgivIYDq--

      :::::::::::::::::::::::::
      :: Starting machine(s) ::
      :::::::::::::::::::::::::
      :: Starting 1 machine
      :: Machine 1 (vm ID: 5508)
             Public ip: 134.158.75.75
      :: Done!

Response should give the VM ID and Public IP address.

## Describe

List all active machines:

    $ stratus-describe-instance
    id   state     vcpu memory    cpu% host/ip                  name
    5507 Running   1    0         0    vm-201.lal.stratuslab.eu one-5507
    5508 Pending   1    0         0    vm-202.lal.stratuslab.eu one-5508
	
State of a single machine:

    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                  name
    5508 Prolog   1    0         0    vm-202.lal.stratuslab.eu one-5507

## Connect

VM states change as the cloud intializes the virtual machines.
In StratusLab VM common machine states are:
    
    Prolog: cloud initialization of an image (e.g. copy and cache image)
    Boot: starting virtual machine from image
    Running: machine is active
    Failed: problem with starting/running the machine
    Unknown: machine is stopped, but still has resources allocated!

Check the VM is running

    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                 name
    5508 Running   1    131072    4    vm-202.lal.stratuslab.eu one-5508

Ping the VM to see when it is accessible:

     $ ping vm-202.lal.stratuslab.eu
     PING vm-202.lal.stratuslab.eu (134.158.75.202): 56 data bytes
     Request timeout for icmp_seq 0
     64 bytes from 134.158.75.201: icmp_seq=1 ttl=63 time=0.876 ms
     64 bytes from 134.158.75.201: icmp_seq=2 ttl=63 time=0.761 ms
     64 bytes from 134.158.75.201: icmp_seq=3 ttl=63 time=0.850 ms
     ...

Connect to the VM as root:

    $ ssh root@vm-202.lal.stratuslab.eu
    # uname -a
    Linux ttylinux_host 2.6.38.1 #1 SMP PREEMPT Tue Apr 10 21:55:48 MST 2012 x86_64 GNU/Linux
    # logout
    Connection to vm-202.lal.stratuslab.eu closed.
    $

## Terminate

To safely stop all services and halt a virtual machine, use the
standard `shutdown` or `halt` commands from within the virtual
machine.

    # shutdown -h
    #
    Connection to vm-202.lal.stratuslab.eu closed by remote host.
    Connection to vm-202.lal.stratuslab.eu closed.

The machine will stop and the status will eventually become an
"unknown" state.

    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                  name
    5508 Unknown   1    131072    0    vm-202.lal.stratuslab.eu one-5508

    $ stratus-kill-instance 5508
    $

This mechanism ensures that all resources (especially data volumes)
are shut down cleanly and released.  Note that the VM resources are
**not** released until the `stratus-kill-instance` command is run.

You can also forcably stop and remove machine by just running the
`stratus-kill-instance` command:

    $ stratus-kill-instance 5508
    $
    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                  name
    5508 Done      1    131072    0    vm-202.lal.stratuslab.eu one-5508

This is the essentially the equivalent of pulling the power cord out
of a physical machine, so be careful when doing this, especially if
persistent data volumes are attached to the virtual machine.

    $ stratus-kill-instance 5508

# Virtual Machine Resources

You can control the number of CPUs, amount of RAM and size of the swap
space allocated to a virtual machine.  StratusLab provides a number of
predefined machine cnofigurations.  You can obtain a list of these
with the command:

    $ stratus-run-instance --list-type
      Type              CPU        RAM       SWAP
      c1.medium       1 CPU     256 MB    1024 MB
      c1.xlarge       4 CPU    2048 MB    2048 MB
      m1.large        2 CPU     512 MB    1024 MB
    * m1.small        1 CPU     128 MB    1024 MB
      m1.xlarge       2 CPU    1024 MB    1024 MB
      t1.micro        1 CPU     128 MB     512 MB

You can select the configuration you want by using the `--type` option
to `stratus-run-instance` and providing the name of the type.  The
default is the type marked with an asterisk ("m1.small").

You can also individually specify the CPU, RAM, and swap space with
the `--cpu`, `--ram`, and `--swap` options.  These will override the
corresponding in the value in the selected type.

Note that the maximum values are determined by the largest physical
machine in the cloud infrastructure.  The cloud administrator of your
infrastructure can provide these limits.

## Deploy a VM of type "m1.xlarge"

    $ stratus-run-instance --quiet --type=m1.xlarge BtSKdXa2SvHlSVTvgFgivIYDq--
    5509, 134.158.75.203

    $ stratus-describe-instance 5509
    id   state     vcpu memory    cpu% host/ip                  name
    5509 Running   2    1048576   5    vm-203.lal.stratuslab.eu one-5509

CPUs and memory can be seen from the command line.
The swap space can be seen from within the machine.
Note: ttylinux doesn't use swap space!


## Deploy a VM with allocated resources

    $ stratus-run-instance --quiet --cpu=3 --ram=6000 --swap=2000 BtSKdXa2SvHlSVTvgFgivIYDq--
    5510, 134.158.75.204

    $ stratus-describe-instance 5510
    id   state     vcpu memory    cpu% host/ip                  name
    5510 Running   3    1572864   5    vm-204.lal.stratuslab.eu one-5510

[ttylinux-img]: https://marketplace.stratuslab.eu/metadata/BtSKdXa2SvHlSVTvgFgivIYDq--


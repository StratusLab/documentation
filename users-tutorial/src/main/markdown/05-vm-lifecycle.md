
# Virtual Machine Lifecycle

This chapter descibes how to launch, to describe, to access, and to
terminate a Virtual Machine on a StratusLab cloud.

## Lifecycle States

The following table summarizes the simplified Virtual Machine
lifecycle and associated commands.

State       Command
----------  -----------------------------------------
Deploy      `stratus-run-instance Marketplace_ID`
Describe    `stratus-describe-instance VM_ID`
Connect     `ssh root@134.158.75.xxx` _or_
            `stratus-connect-instance VM_ID`
Terminate   `stratus-kill-instance VM_ID`

The detailed lifecycle of a machine is more complicated.  The diagram
shows the full lifecycle and describes what is happening behind the
scenes in each of these cases.

![Virtual machine timeline and states.](images/vm-timeline.png)

The rest of this chapter shows how to run a VM through these states.
We will use a simple **ttylinux** appliance.  This is a minimal Linux
operating system used normally for embedded systems.  It is small and
boots quickly, making it a good choice for demonstrations like this.

The Marketplace identifier for the ttylinux machine is:

    BtSKdXa2SvHlSVTvgFgivIYDq--

You will need this when launching the ttylinux VM.  We will explain
where this identifier comes from in the Marketplace chapter. 

We will need this often in the tutorial.  To avoid typing it all of
the time, let's set up a variable:

    $ export TTYLINUX="BtSKdXa2SvHlSVTvgFgivIYDq--"

Any place you see $TTYLINUX in the commands, it refers to this machine
identifier.

## Deploy

Launch an instance of the ttylinux appliance with the
`stratus-run-instance` command:

    $ stratus-run-instance $TTYLINUX

      :::::::::::::::::::::::::
      :: Starting machine(s) ::
      :::::::::::::::::::::::::
      :: Starting 1 machine
      :: Machine 1 (vm ID: 5508)
             Public ip: 134.158.75.75
      :: Done!

Response should give the VM ID and Public IP address of the VM.

All of the StratusLab commands support the `--help` option, which will
give detailed information about the command and its options.

## Describe

List all active machines:

    $ stratus-describe-instance
    id   state     vcpu memory    cpu% host/ip                  name
    5507 Running   1    0         0    vm-201.lal.stratuslab.eu one-5507
    5508 Pending   1    0         0    vm-202.lal.stratuslab.eu one-5508
	
State of a single machine:

    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                  name
    5508 Prolog    1    0         0    vm-202.lal.stratuslab.eu one-5507

## Connect

VM states change as the cloud intializes the virtual machines.  The
following table describes the common states.

State    Description
-------- ------------------------------------------------------
Prolog   cloud initialization of VM (e.g. copy and cache image)
Boot     starting virtual machine from the image
Running  machine is active, booting the operating system
Failed   problem with starting/running the machine
Unknown  machine is stopped, but still has resources allocated!

Table: Common VM States

Check the VM is running:

    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                 name
    5508 Running   1    131072    4    vm-202.lal.stratuslab.eu one-5508

Ping the VM to see when it is accessible:

     $ ping vm-202.lal.stratuslab.eu
     PING vm-202.lal.stratuslab.eu (134.158.75.202): 56 data bytes
     Request timeout for icmp_seq 0
     64 bytes from 134.158.75.202: icmp_seq=1 ttl=63 time=0.876 ms
     64 bytes from 134.158.75.202: icmp_seq=2 ttl=63 time=0.761 ms
     ...

Connect to the VM as root:

    $ ssh root@vm-202.lal.stratuslab.eu
    # uname -a
    Linux ttylinux_host 3.7.1 #1 SMP PREEMPT Mon May 27 13:16:10 MST 2013 x86_64 GNU/Linux
    # logout
    Connection to vm-202.lal.stratuslab.eu closed.
    $

You can also use the `stratus-connect-instance` command.

    $ stratus-connect-instance 5508
    # uname -a
    Linux ttylinux_host 3.7.1 #1 SMP PREEMPT Mon May 27 13:16:10 MST 2013 x86_64 GNU/Linux
    # logout
    Connection to vm-202.lal.stratuslab.eu closed.
    $
 
It just wraps the usual `ssh` command and saves you from having to
look up the hostname.


## Terminate

To safely stop all services and halt a virtual machine, use the
standard `shutdown` or `halt` commands from within the virtual
machine.

    # shutdown -h
    #
    Connection to vm-202.lal.stratuslab.eu closed by remote host.
    Connection to vm-202.lal.stratuslab.eu closed.

The machine will stop and the state will eventually become "unknown".

    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                  name
    5508 Unknown   1    131072    0    vm-202.lal.stratuslab.eu one-5508

This mechanism ensures that all services are shutdown cleanly and that
data volumes are unmounted.

You can then release the machine's resource with the
`stratus-kill-instance` command.

    $ stratus-kill-instance 5508
    $
    $ stratus-describe-instance 5508
    id   state     vcpu memory    cpu% host/ip                  name
    5508 Done      1    131072    0    vm-202.lal.stratuslab.eu one-5508

Note that the VM resources are **not released** until the
`stratus-kill-instance` command is run.  In particular, data volumes
will remain allocated to this VM until this command is run.

The `stratus-kill-instance` command can also be used for a running
machine.  In this case, it will **forcibly stop and remove the
machine**; this can be useful if you cannot access the VM for some
reason.  Be careful when doing this, especially if persistent data
volumes are attached to the virtual machine.

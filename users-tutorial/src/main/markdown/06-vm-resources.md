# Virtual Machine Resources

You can control the number of CPUs, amount of RAM and size of the swap
space allocated to a virtual machine.

## Predefined Machine Types

StratusLab provides a number of predefined machine configurations.
You can obtain a list of these with the command:

    $ stratus-run-instance --list-type
      Type              CPU        RAM       SWAP
      c1.medium       1 CPU     256 MB    1024 MB
      c1.xlarge       4 CPU    2048 MB    2048 MB
      m1.large        2 CPU     512 MB    1024 MB
    * m1.small        1 CPU     128 MB    1024 MB
      m1.xlarge       2 CPU    1024 MB    1024 MB
      t1.micro        1 CPU     128 MB     512 MB

You can select the configuration you want by using the `--type` option
of `stratus-run-instance` and providing the name of the type.  The
default is the type marked with an asterisk ("m1.small").

If the predefined types do not match your needs, you can also
individually specify the CPU, RAM, and swap space with the `--cpu`,
`--ram`, and `--swap` options.  These will override the corresponding
values in the implicitly or explicitly selected type.

Note that the maximum values are determined by the largest physical
machine in the cloud infrastructure.  The cloud administrator of your
infrastructure can provide these limits.

For the StratusLab infrastructure at LAL, you can also view the state
of the physical machines and see the available resources.  Use a web
browser or `curl` to see the contents of the status page:

    $ curl http://cloud.lal.stratuslab.eu/load/load.txt
    2013-08-28T09:40:01+0000
       ID   RVM   TCPU   ACPU   TMEM   FMEM   AMEM   STAT
      29      2   2400   2100  35.3G  28.4G  32.8G     on
      30      0      0    100     0K     0K     0K    err
      31      2   2400   2100  35.3G  31.8G  29.2G     on
      32      2   2400   2200  35.3G  32.2G  32.3G     on
      33      2   2400   2100  35.3G    31G  29.8G     on
      34      2   2400   2000  35.3G  34.6G  33.3G     on
      35      3   2400   2000  35.3G  33.8G  31.3G     on
      36      3   2400   2000  35.3G  32.7G  32.7G     on
      37      3   2400   1500  35.3G  23.5G  17.8G     on
      38      0      0    100     0K     0K     0K    err
      39      3   3200   2000  62.9G  23.4G  23.9G     on
      40      3   3200   2700  62.9G  56.6G  54.4G     on
      41      3   3200   2700  62.9G  61.1G  60.7G     on
      42      4   3200    200  62.9G  56.2G 968.4M     on
      43      4   3200      0  62.9G  50.3G   2.1G     on
      44      2   3200      0  62.9G  45.6G  17.8G     on
      45      2   3200      0  62.9G  16.9G  17.8G     on

The interesting values are probably the TCPU and ACPU (total and
available CPUs x 100) and TMEM and AMEM (total and available RAM).

## Deploy VM with Machine Type

Deploy a VM of type "m1.xlarge"

    $ stratus-run-instance \
        --quiet --type=m1.xlarge $TTYLINUX
    5509, 134.158.75.203

Looking at the status of the machine:

    $ stratus-describe-instance 5509
    id   state     vcpu memory    cpu% host/ip                  name
    5509 Running   4    8388608   5    vm-203.lal.stratuslab.eu one-5509

will give you the allocated CPUs (vcpu) and memory.  The swap space
can be seen from within the machine.  **These values are provided by a
monitoring process on the physical machines and may take a couple of
minutes to be updated.**

Note that ttylinux resides entirely in memory and doesn't use swap
space!

## Deploy VM with Customized Resources

Try deploying a machine with customized resources.  You can use one or
more of the options `--type`, `--cpu`, `--ram`, and `--swap`: 

    $ stratus-run-instance \
        --quiet --cpu=3 --ram=6000 --swap=2000 $TTYLINUX
    5510, 134.158.75.204

Again use the status of the VM to see what resources were allocated:

    $ stratus-describe-instance 5510
    id   state     vcpu memory    cpu% host/ip                  name
    5510 Running   3    6144000   5    vm-204.lal.stratuslab.eu one-5510

Note that the memory value for the status is given in KiB.

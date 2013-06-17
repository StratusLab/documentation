
# Virtual Machine Lifecycle

## Launch

[Configure][user-client-config] Cloud endpoint and credentials in the
configuration file (default: `$HOME/.stratuslab/stratuslab-user.cfg`).

Search for an image in [StratusLab Marketplace][marketplace] and copy 
the image **identifier**. Say we searched for **ttylinux** and chose 
**[BN1EEkPiBx87_uLj2-sdybSI-Xb][ttylinux-img]**.

Launch an instance of the image

    $ stratus-run-instance BN1EEkPiBx87_uLj2-sdybSI-Xb
    
      :::::::::::::::::::::::::
      :: Starting machine(s) ::
      :::::::::::::::::::::::::
      :: Starting 1 machine
      :: Machine 1 (vm ID: 5507)
             Public ip: 134.158.75.75
      :: Done!

## Connect

Check the VM is up and running

    $ stratus-describe-instance 5507
    id   state     vcpu memory    cpu% host/ip                 name
    5507 Running   1    131072    4    vm-75.lal.stratuslab.eu one-5507

Connect to the VM

    $ ssh root@vm-75.lal.stratuslab.eu
    # uname -a
    Linux ttylinux_host 2.6.38.1 #1 SMP PREEMPT Tue Apr 10 21:55:48 MST 2012 x86_64 GNU/Linux
    # logout
    Connection to vm-75.lal.stratuslab.eu closed.
    $

## Terminate

    $ stratus-kill-instance 5507


[ref-infra]: /try/2012/12/04/try-reference-cloud-infrastructures.html
[user-client-install]: /try/2012/01/10/try-user-cli-installation.html
[user-client-config]: /try/2012/01/10/try-user-cli-installation.html
[marketplace]: https://marketplace.stratuslab.eu
[docs]: /documentation
[ttylinux-img]: https://marketplace.stratuslab.eu/metadata/BN1EEkPiBx87_uLj2-sdybSI-Xb




# Tutorial Overview

This tutorial explains the major features of the StratusLab cloud
distribution.  It shows how to start, access, and control virtual
machines and how to use various types of storage resources with those
virtual machines.

More advanced topics, such as creating customized images, are touched
upon, but the reader should consult the User's Guide for more details
on how to use those features.

## Conventions

The exercises of this tutorial can be done from any Linux, Mac OS X,
or Windows machine.  The examples show the commands for the Linux and
Mac OS X operating systems; the commands must be adapted appropriately
for use on Windows.

Colored text boxes in Courier font show interactions with a command
line shell.  The command is preceeded by a dollar sign and possibly
continued onto multiple lines with trailing backslashes.  All other
text are the results of running the given command.  The output may be
abbreviated; this is indicated by an ellipsis.

## Service Parameters

This tutorial presumes that the StratusLab Reference Cloud at LAL is
being used.  The relevant parameters for this cloud (and needed for
the client configuration) are given in the following table.

Parameter             Value
--------------------  ---------------------------------
marketplace_endpoint  https://marketplace.stratuslab.eu
endpoint              cloud.lal.stratuslab.eu
pdisk_endpoint        pdisk.lal.stratuslab.eu

You will also need to provide values for the parameters `username` and
`password`.  These correspond to your account on the StratusLab
cloud. 

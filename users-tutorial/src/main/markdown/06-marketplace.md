
# Marketplace

Building new virtual machine images can be a tedious and
time-consuming task.  Your first instinct should be to first look to
see if someone else has done the work for you!

## Image Registry

The [StratusLab Marketplace](https://marketplace.stratuslab.eu/)
provides a registry for available, public virtual machine images.
These are created by the people within the community as well as by
StratusLab partners to help people get started using the cloud
quickly.

The listing contains the basic information for the images along with a
link to see more detailed information.  **The machine identifier is
the most important piece of information.** Only the identifier is
needed to run an instance of that machine; simply provide it to the
`stratus-run-instance` command.

## Base Images

The StratusLab partners themselves provide "base" images for ttylinux,
CentOS (a RHEL derivative), Ubuntu, Debian, and OpenSuSE.  These are
minimal, but functional, installations of these operating systems.
They can be used directly or customized to create personalized
machines.  These can be found by typing "images@stratuslab.eu" for the
endorser in the "Filter" boxes on the right side of the screen.  (See
the screenshot.)

![StratusLab Base Images](images/baseimages-screenshot.png)

You can also use the filters, for example on the operating system, to
further refine the list.

## Customized Images

Although not covered in this tutorial, you can also create and
register your own images to share with others.  For example, IGE has
prepared images for testing Globus services and for running tutorials
with the services.  IBCP has created appliances with numerous
bioinformatics applications already installed.

Search the Marketplace to find appliances that interest you.


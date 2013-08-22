
# Customized Images

One of the strongest reasons to use a cloud is the capability of
providing a completely customized computational environment.  The
relieves users from the often painful installation and configuration
of applications.

This short tutorial does not have the time to cover the topic of
customized images in detail, but you should be aware of the following
information when you reach the point where you want to use or to
create a customized image. 

## Contextualization

Contextualization allows a virtual machine instance to learn about its
cloud environment (the 'context') and to configure itself to run
correctly there. StratusLab now supports CloudInit contextualization
in addition to the OpenNebula and HEPiX contextualization schemes.

Image creators can use the contextualization mechanism to create
"parameterized" images, allowing users to pass additional information
when starting the instance to alter the behavior of the image.  This
could be used, for instance, to configure an application or to send
the location of another service.  

## Create Image

Creating a new image can be difficult and time-consuming.  StratusLab
facilitates this process by providing the `stratus-create-image`
command.  This takes the identifier of a base image, a list of
additional packages, and a configuration script as input.  It then
automates the full creation process by launching an instance of the
base image, installing the packages, and running the script.  The
result is saved and the information sent to the user.  This is the
easiest way to create a new image.  

You can also create an image from scratch or convert an VirtualBox
image.  These require you to completely understand the
contextualization process and to include the contextualization
mechanisms in the image.  

More information on all of these processes can be found in the [Users
Guide][users-guide].

## Image Metadata

After creating an image, it must be registered in the Marketplace
before it can be used.  Again StratusLab provides a number of commands
to aid this process.  The `stratus-build-metadata` command will
provide a skeletal metadata description of an image with all of the
necessary checksums and identifiers included.  You then only need to
complete the other fields with your favorite text editor.  **You must
provide the location URL from where the image contents can be
obtained.**

After the metadata is complete, you must sign it with the
`stratus-sign-metadata` command.  (You'll need a personal certificate
to do this, either a self-signed one or one from a provider.)  Once
signed, it can be uploaded to the Marketplace via the browser or the
`stratus-upload-metadata` command.

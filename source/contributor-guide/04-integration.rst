Continuous Integration
======================

The collaboration uses `Jenkins <http://jenkins-ci.org>`__ for
continuous integration. Each time a change is committed to a
StratusLab repository, the Hudson server launches a job to rebuild the
software, executing the defined unit tests and repackaging the
software.

Assuming that the unit tests pass, jobs to reinstall a test cloud and to
verify the high-level functionality are also run.

The `continuous integration server <https://ci.stratuslab.eu/>`__
provides a list of all of the defined jobs and a dashboard of the
latest status.

Supported Platforms
-------------------

The project currently supports CentOS.  All of the builds run on this
platform along with the cloud installation and functionality tests.

All of the code is highly portable and should run on any Linux
platform. Additional operating systems can be supported if there is
enough effort to support the packaging and testing of the code on
the operating system. 

Package Repository
------------------

The artifacts (packages) from all of the builds are uploaded to a Maven
repository. The project runs a `Nexus
server <http://repo.stratuslab.eu:8081/>`__ for this.

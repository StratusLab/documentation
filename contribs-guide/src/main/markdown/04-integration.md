
# Continuous Integration

The collaboration uses Hudson (probably Jenkins in the future) for
continuous integration.  Each time a change is committed to a
StratusLab repository, the Hudson server launches a job to rebuild the
software, executing the defined unit tests and repackaging the
software. 

Assuming that the unit tests pass, jobs to reinstall a test cloud and
to verify the high-level functionality are also run.

The [Hudson server](https://hudson.stratuslab.eu/) provides a list of
all of the defined jobs and a dashboard of the latest status.

## Supported Platforms

The project currently supports CentOS and OpenSuSE.  All of the builds
are automatically run on both platforms when changes are committed.

The cloud installation and functionality tests are currently only run
on CentOS. 

## Package Repository

The artifacts (packages) from all of the builds are uploaded to a
Maven repository.  The project runs a [Nexus
server](http://repo.stratuslab.eu:8081/) for this.

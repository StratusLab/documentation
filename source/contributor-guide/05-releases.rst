Releases
========

Frequency
---------

The project follows a timed-release policy where a new release is
prepared every quarter. The release numbering follows the Ubuntu-style
format (e.g. 13.05.0 for the May 2013 release). Patch releases may also
be prepared for individual components between the quarterly releases as
necessary.

Milestones
----------

GitHub issues should be tied to a milestone. Where the milestones are
named after the releases (e.g. 13.05 for the May 2013 release). This
allows the changelog for the release to be prepared easily.

Tagging and Packaging
---------------------

All of the tagging and packaging of the releases are handled through
manually-triggered jobs in Hudson. The "tagging" jobs should be executed
first, with the "release" jobs executed afterwards for both CentOS and
OpenSuSE.

Note that these must be done in the correct order to ensure that the
proper dependencies are picked up. The proper dependencies will need to
be updated by hand in the pom files.

Publishing
----------

The releases are made available through yum repositories. Again, Hudson
is used to create the content of these repositories. They must be copied
by hand to the StratusLab yum repository machine.

There are also a couple of python modules (stratuslab-client and
stratuslab-libcloud-drivers) that must be pushed into PyPi. Once these
are tagged, they should be built by hand and then pushed into PyPi.

Announcements
-------------

A release announcement and changelog must be prepared for each release.
It should be published on the website and then tweeted.

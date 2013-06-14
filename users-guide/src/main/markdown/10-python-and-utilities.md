
# Python and Python Utilities

The StratusLab command line client and other tools are written
Python and are packaged to take advantage of the Python installation
tools.

An environment with an acceptable version of Python must be used.  In
addition, it is strongly recommended that `pip` and `virtualenv` be
used to manage the installation and configuration of the StratusLab
tools. 

## Python

All of the StratusLab python code requires a recent version of Python
2, version 2.6 or later.  The StratusLab code is **not** compatible
with Python 3.

### Linux Operating Systems

Python packages are a standard part of all Linux distributions and can
be installed via the distribution's standard package management
tools.

Once installing Python verify that a version compatible with
StratusLab has been installed:

    $ python --version
    Python 2.6.6

If the version is not a recent Python 2 version (2.6+), then you must
install a separate version that is compatible with StratusLab and
modify your environment.  You will find the `virtualenv` section below
useful in this case.

### Mac OS X

All recent versions of Mac OS X provide a version of Python that works
with StratusLab.  No special configuration is needed for Python
itself. 

### Windows

Python is not a standard part of the Windows distributions.
Consequently, you must install and configure Python yourself.  You can
find installation packages on the [python.org](http://python.org)
website.  Be sure to select an installer for Python 2 and **not**
Python 3.

## Pip

Pip is a tool for installing and managing Python packages.  It allows
packages to be installed, listed, queried, upgraded, and uninstalled.
It uses information in the downloaded packages to automatically
resolve dependencies.

If pip is not already installed on your system, then you can find out
how to download and install it on the [pip-installer.org
website](http://www.pip-installer.org/en/latest/).  This website also
contains information on how to use it.

StratusLab **strongly recommends using pip** to manage the
installation of the its Python-based tools.  The `easy_install`
command can also be used, but pip provides better management
capabilities. 

## Virtualenv

The virtualenv package allows users to create customized virtual
Python environments.  This avoids having to modify the global system
environment, permits use of different Python versions, and avoids
potential dependency conflicts between installed packages.

The [virtualenv page](https://pypi.python.org/pypi/virtualenv) in
[pypi](https://pypi.python.org/pypi) provides complete information on
installing and using virtualenv.

StratusLab recommends using virtualenv, especially if you are on a
machine with a shared environment that you cannot easily change.


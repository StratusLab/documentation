
# Preface

## Target Audience

This guide provides information of interest to researchers,
scientists, and engineers looking to use an existing StratusLab cloud
infrastructure.  It also provides information for application
developers looking to port their software to a StratusLab cloud
environment.

System administrators wanting to install a StratusLab cloud on their
own resources should start with the StratusLab Administrator's Guide,
although they will also want to use the information in this guide to
test their cloud after installation. 

Those wishing to contribute to the StratusLab software or
documentation should consult the StratusLab Contributor's Guide. 

## Typographic Conventions

This guide uses several typographic conventions to improve the
readability.

---------  ---------------------------------------
links      [some link](http://example.org/)
filenames  `$HOME/.stratuslab/stratuslab-user.cfg`
commands   `stratus-run-instance`
options    `--version`
---------  ---------------------------------------

Table: Typographic Conventions

Extended examples of commands and their outputs are displayed in the
monospace Courier font.  Within these sections, command lines are
prefixed with a '$ ' prompt.  Lines without this prompt are output
from the previous command.  For example, 

    $ stratus-run-instance -q BN1EEkPiBx87_uLj2-sdybSI-Xb
    5507, 134.158.75.75

the `stratus-run-instance` is the command line which returns the
virtual machine identifier and IP address.

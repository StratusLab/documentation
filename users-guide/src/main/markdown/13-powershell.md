
# Windows PowerShell

In recent versions of Windows, there are two command line interfaces:
the traditional "cmd" shell and the newer "PowerShell".

If available, PowerShell is recommended.  It can be started directly
from the graphical interface or indirectly from the "cmd" shell by
typing the command `powershell`.

For security reasons, PowerShell does not allow scripts to be
executed by default.  As the StratusLab command line interface
consists of Python scripts, these cannot be executed without changing
the default configuration.

To allow PowerShell to run scripts, execute the following within the
PowerShell:

    Set-ExecutionPolicy Unrestricted -Scope CurrentUser

The shell does not need to be restarted.  This configuration change
only needs to be done once.



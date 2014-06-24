
Front End Installation
======================

Now that we have defined all of the configuration parameters, you can
now do the full Front End installation by issuing the following
command::

    $ stratus-install -vv 2>&1 | tee frontend-install.log

To get more details on what the command is (because of curiosity or
errors), use the option ``-v``, ``-vv``, or ``-vvv``.

If you run into errors, the ``stratus-install`` command can simply be
rerun after adjusting the configuration parameters.

When the installation completes successfully, you should see the
following services running on the machine::

    $ service nginx status
    nginx (pid  15215) is running...

    $ service one-proxy status
    Checking arguments to Jetty: 
    ...
    Jetty running pid=15847

    $ service pdisk status
    Checking arguments to Jetty: 
    ...
    Jetty running pid=17099

You should also see a web page running at `https://${FRONTEND_HOST}/`
that lists these services::

    $ curl -k https://${FRONTEND_HOST}/ 
    <html>
    ...
    <a href="readme.html">readme.html</a>
    ...
    <a href="svc-one-proxy.html">svc-one-proxy.html</a>
    ...
    <a href="svc-pdisk.html">svc-pdisk.html</a>
    ...
    </html>

If the page doesn't appear or any of the services are not running, you
should investigate the errors in the log files.  The log files for all
StratusLab services are in `/var/log/stratuslab`.  Each service in a
separate subdirectory.

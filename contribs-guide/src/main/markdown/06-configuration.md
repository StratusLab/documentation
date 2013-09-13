
# Service Configuration

All StratusLab services must follow a common configuration scheme
where the core of the configuration is stored in the Couchbase
database.  Only the Couchbase configuration parameters are read from a
file on the local filesystem.

This scheme allows the overall system to scale with multiple service
instances while maintaining consistent configurations between them.

# Couchbase Connection Parameters

The Couchbase connection parameters must reside in a file on the
physical machine to bootstrap the configuration mechanism.  This file
`/etc/stratuslab/couchbase.cfg` is in the standard "ini" format:

    [DEFAULT]
    hosts=host1,host2,host3
    bucket=bucket1
    password=bucket1_password

    [service]
    hosts=host4
    bucket=other_bucket
    password=other_password
    cfg_docid=docid

Each section corresponds to the parameters for a particular service.
The name of the section is defined by the service.  If parameters are
not defined in a specific section, then the ones in the `DEFAULT`
section will be used.  Services may have a default value for the
`cfg_docid` value.

# Service Instance Configuration 

After reading the Couchbase connection parameters, the service should
read its configuration from a document in Couchbase.  The document
identifier containing the configuration is defined by the service, but
may be overridden by the `cfg_docid` parameter in the local
configuration. 

To use Couchbase efficiently, these configuration files should be in
JSON format.  This allows them to be pulled into native data
structures easily for our preferred implementation languages.

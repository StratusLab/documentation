Authentication
==============

As mentioned in the previous chapter, the CIMI server also handles all
of the user authentication for the cloud. The server supports the use of
internal or external databases of users.

The internal database, with user records stored in Couchbase, is
convenient because all information is contained within the cloud
infrastructure and user records can be managed in the same way as other
configuration files.

Using an external database, such as LDAP, allows the user information to
be exported to other system or allows better integration of the cloud
with other services in the data center. In the case of VOMS proxies,
this also delegates some authority to third parties to manage the users
associated with a particular Virtual Organization.

Overview
--------

There are two aspects to the authentication configuration for a
StratusLab cloud: defining the methods to be used and managing the
users.

To define the authentication mechanisms for the cloud infrastructure,
you must create the document "ServiceConfiguration/authn" in the
Couchbase database. You can use the same web interface shown earlier to
browse to the "ServiceConfiguration" collection and then to add the new
document.

The document must follow the defined schema:

::

    {
      "service": "authn",
      "localdb":
        {
          "password-enabled": true,
          "cert-enabled": false
        },
      "ldap": 
        { 
          "password-enabled": false,
          "cert-enabled": false,
          ...
        },
      "voms": 
        {
          "enabled": false
        }
    }

where the "service" and "localdb" fields are required (and the value for
the "service" field must be "authn"!). The fields "ldap" and "voms" are
optional. The details of the "ldap" map are given below.

If the configuration document does not exist, then the default is to
only use the local user database with password credentials. It is
recommended to always keep this method active along with at least one
administrative account defined in the database.

    **NOTE**: Enabling or disabling authentication methods requires that
    the CIMI service be restarted.

Adding or removing users from the system, requires changes to the local
or external user databases. The details on this are given in the
following sections.

    **NOTE**: Adding, removing, or modifying users does **not** require
    restarting the CIMI service. These changes will be taken into
    account immediately for all future authentication actions.

You can also define "roles" for the users to either indicate group
membership or rights to perform particular actions. There are three
special roles "::ADMIN", "::USER", and "::ANON". The first confers
administrative rights to the user; users with this role will have
complete control over the cloud configuration. The "::USER" role is
given to anyone who has been authenticated. The "::ANON" role is used
for users that have not been authenticated.

Couchbase User Entries
----------------------

You can manage users directly in the Couchbase database via the CIMI
interface. There is a "User" collection and you can add, modify, and
remove entries from the system with the same web browser interface
you've used for the other resources.

The schema for user records is:

::

    {
      "last-name": "required",
      "first-name": "required", 
      "username": "required, must be unique",
      "password": "optional, value is bcrypt hash",
      "enabled": true,
      "roles": [ "list", "of", "roles" ],
      "altnames": {
        "x500dn": "DN of user in RFC2253 format"
      }
    }

If the "enabled" field is not supplied, the default value is "false".
The "password" is used only for password authentication; similarly, the
"x500dn" field is only used for certificate authentication.

The schema is a "loose" one, meaning that other fields may be added if
you want to track additional information. One example would be an
"email" field.

Using Passwords
~~~~~~~~~~~~~~~

To use password authentication, the "localdb/password-enabled" flag must
be true in the "ServiceConfiguration/authn" document. The user record
for the given username must exist, must be enabled, and must have a
password set.

The value of the password field is the bcrypt hash of the clear text
password. See the preceeding text for generating the bcrypt hash.

Using Certificates
~~~~~~~~~~~~~~~~~~

To use certificate authentication, the "localdb/cert-enabled" flag must
be true. The field "x500dn" must exist and have the RFC2253-formatted DN
of the user's certificate. The user can use the raw certificate or a
proxy generated from that certificate. The user will be identified with
the value of the "username" field in the user record.

For certificate authentication to work, the SSL configuration for the
Jetty server must be done. By default, the configuration for trusting
the EGI Certificate Authorities is done by the ``stratus-install``
command. If you want to trust different Certificate Authorities, you
must adjust the SSL configuration. See the previous chapter for what
needs to be done.

LDAP User Database
------------------

To authenticate users against an LDAP database, you must have deployed
and populated an LDAP server. The authentication configuration for using
LDAP is quite flexible, so nearly any standard layout for the
information should work.

The "ldap" field in the "ServiceConfiguration/authn" document must
follow the schema:

::

    "ldap": {
      "password-enabled": true,
      "cert-enabled": false,

      "connection": {
        "host": {
          "address": "localhost",
          "port": 389
        },
        "ssl?": false
      },

      "user-object-class": "inetOrgPerson",
      "user-base-dn": "ou=users,o=cloud"
      "user-id-attr": "uid",

      "role-object-class": "groupOfUniqueNames",
      "role-base-dn": "ou=groups,o=cloud",
      "role-member-attr": "uniqueMember",
      "role-name-attr": "cn",

      "skip-bind?": false,
    }

All of the fields are required. The "connection" values provide the
location of the server. The "user-" fields indicate what objects in the
LDAP database are user records and the "role-" fields indicate the
groups that are mapped to roles.

Using Passwords
~~~~~~~~~~~~~~~

The "skip-bind?" field indicates whether to try to bind to the database
with the given user password to authenticate the user. Normally, for
password authentication this should be false and the LDAP server should
be setup to allow only the user to view her user record.

Using Certificates
~~~~~~~~~~~~~~~~~~

This is not supported in the current version, but is planned for a
future version.

VOMS Proxy Authentication
-------------------------

VOMS proxies are a mechanism by which users can delegate some rights to
a third party such as a service. These proxies also convey certain
rights associated with a user who belongs to a Virtual Organization. The
Virtual Organization itself manages its membership, defines the rights,
and allocates those rights to members.

By default, the SSL configuration of the server will be setup to allow
validation of VOMS proxies according to the policies of the European
Grid Infrastructure. You must use this configuration (or a similar one
if you are an expert) to use VOMS proxy authentication.

To enable this, set the "voms/enabled" flag to true in the
authentication configuration. You must then add a pseudo-user entry in
the local database for each Virtual Organization you want to support.

An example pseudo-user entry for the "vo.lal.in2p3.fr" Virtual
Organization is:

::

    {
      "last-name": "Virtual Organization",
      "first-name": "LAL", 
      "username": "vo:vo.lal.in2p3.fr",
      "enabled": true
    }

It is important that the username contain the "vo:" prefix to the name
of the Virtual Organization that is being authorized. The entry should
*not* have a password associated with it.

The "username" associated with users identified via VOMS proxies is the
DN of their certificate. The FQANs (fully-qualified attribute names) of
the VOMS certificate are mapped to roles in the StratusLab
authentication.

Future Authentication Methods
-----------------------------

The authentication framework used by StratusLab is extensible allowing
different mechanisms to be incorporated fairly easily. Candidates for
additional mechansims to support in the future include OAUTH2 and
Shibboleth. Feedback on the desire for these (or other) mechanisms is
welcome.

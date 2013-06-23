
# Authentication and Authorization

## Authentication Methods

StratusLab supports a wide range of different authentication methods
and account storage options, allowing it to fit into a wide range of
sites with pre-existing authentication systems.

All of the configuration files for authentication are located in the
directory `/etc/stratuslab/authn`.  The main configuration file
controlling the active authentication methods and their parameters is
`login.conf`.  Properties files with username/password or certificate
information are also located in this directory. 

### Username and Password

Users can be identified through username/password pairs.  Accounts
associated with these credentials can be stored in:

  * A java properties file or
  * An LDAP server

If you use an LDAP server, the StratusLab Registration service can be
used to allow users to register for an account. 

### X509 Credentials

Users can also be identified via client X509 credentials.  Raw X509
certificates, RFC3820 certificate proxies, or VOMS proxies can all be
used to connect to and to authenticate with the StratusLab services.  

As above, these credentials can be authorized in:

  * A java properties file or
  * An LDAP server

The registration service can also manage the required X509
Distinguished Names (DNs) if using an LDAP server. 

## Authorization

Apart from the super user, users can only see and control resources
that they create themselves.

## Registration Service

StratusLab provides an optional service, the "Registration Service",
that allows users to register for an account on your cloud
infrastructure.  Through a web-accessible interface, users can
register for an account and update the information associated their
accounts.

The registration workflow proceeds through the following stages:

  1. User submits form with required information,
  2. Service sends email validation message to user,
  3. User confirms email address by visiting confirmation link,
  4. Service sends account validation request to administrator,
  5. Administrator accepts or rejects account request, and
  6. An email is sent to the user with the decision.

When users update their email addresses, the new email address is also
validated through an email and confirmation link. 

The user information is stored in a separate LDAP server.  The
registration service must have full access to that server, so that it
can manage the user entries.

To use the accounts managed by the Registration Service on your cloud
infrastructure, you must configure the authentication service to use
the LDAP server as a source of information.  Users who supply a
certificate DN, can also authenticate with the cloud using their
certificate. 

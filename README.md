ssm-dupl-send
=============


The command first run the apelclient executable. Then, by using rsync, it duplicates the record messages to a second location in the filesystem
two instances of the ssmsend command are then used to publish the messages to two different message brokers.

### CONFIGURATION:

Three configuration files are involved:

#### /etc/apel/client.cfg

This is the config file for the command that joins the information collected by apelparser into a mysql database and generates the record messages.
in this file is mandatory to set:

````
[ssm]
enabled = false
````

The default is 'true', so pay attention to change it!

All the other parameters should be set following the apel documentation.

This Change in the conf file tells the apel client to not directly instantiate the ssmsend executable right after finishing its job. 
Thus allowing us to duplicate them.

#### /etc/apel/sender.cfg

This is the configuration file used by ssmsend to publish the records to the EGI brokers following the standard route. Should be set 
following the apel documantation

#### /etc/apel/faust-sender.cfg

This is the configuration file used by ssmsend to publish the 2nd copy of the records to the local broker Should be configured as follow:

````
################################################################################
# Required: broker configuration options
#

[broker]

# The SSM will query a BDII to find brokers available.  These details are for the
# EGI production broker network
#bdii: ldap://lcg-bdii.cern.ch:2170					<-- NOTE: THIS IS COMMENTED
#network: PROD										<-- NOTE: THIS IS COMMENTED
# OR (these details will only be used if the broker network settings aren't used)
host: SETINFNBROKERURLHERE						<--NOTE THIS
port: 61613											<--NOTE THIS

# broker authentication.  If use_ssl is set, the certificates configured
# in the mandatory [certificates] section will be used.
use_ssl: false										<--NOTE THIS


################################################################################
# Required: Certificate configuration

[certificates]
certificate: /etc/grid-security/hostcert.pem
key: /etc/grid-security/hostkey.pem
capath: /etc/grid-security/certificates
# If this is supplied, outgoing messages will be encrypted
# using this certificate
#server_cert: /etc/grid-security/servercert.pem      <-- NOTE: THIS IS COMMENTED


################################################################################
# Messaging configuration.
#
[messaging]

# Queue to which SSM will send messages
destination: SETQUEUEHERE							<-- NOTE THIS

# Outgoing messages will be read and removed from this directory.
path: /var/spool/faust/outgoing						<-- NOTE THIS

[logging]
logfile: /var/log/apel/ssmsend-faust.log			<-- NOTE THIS
# Available logging levels:
# DEBUG, INFO, WARN, ERROR, CRITICAL
level: INFO
console: true
````

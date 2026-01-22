[//]: # (Please update the Confluence page titled "EJBCA CE DockerHub - Markdown" if making any changes to this page)

EJBCA Community
===============

>**Note:**
>
>*We take the security of EJBCA and the trust of our users seriously. If you believe you have identified a security vulnerability in EJBCA, please report it responsibly by contacting us at security@primekey.com.* 
>
>*EJBCA Community Edition is not intended for production use. For PKI production deployments, use Keyfactor EJBCA Enterprise Edition [EJBCA Enterprise](https://www.keyfactor.com/products/ejbca-enterprise/).* 

## Welcome to EJBCA Community

EJBCA is a public key infrastructure (PKI) and certificate authority (CA) solution and one of the longest-running CA software projects. It is platform-independent and covers the full certificate lifecycle, from enrollment and management to validation. 

EJBCA is developed in Java and runs on a JVM such as OpenJDK, available on most platforms, such as Linux and Windows.  

There are two editions of EJBCA: 

*  **[EJBCA Community](https://www.ejbca.org/)** (EJBCA CE)

EJBCA Community Edition is an LGPL-licensed open-source subset of EJBCA Enterprise, certified by the Open Source Initiative, and designed for learning, testing, and prototyping certificate and PKI workflows. 

EJBCA CE is not recommended for production use, as production deployments typically require security certifications, high assurance features, SLAs, support, and operational assurances available in EJBCA Enterprise Edition.  

*  **[EJBCA Enterprise](https://www.keyfactor.com/products/ejbca-enterprise/)** (EJBCA EE)

EJBCA Enterprise Edition is designed for production PKI deployments, offering advanced features, security certifications, high assurance features, enterprise-grade operations, and commercial support with SLAs.  

Learn more about the differences between EJBCA CE and EJBCA EE: [EJBCA Community vs Enterprise](https://www.ejbca.org/community-vs-enterprise/) 

Get started with the EJBCA Community Docker container
===============

The EJBCA Community Docker container can be pulled straight from the command line using the docker tool. To download and unpack the latest EJBCA Community container image from Docker Hub, use the following command: 

```
$ sudo docker pull keyfactor/ejbca-ce 
```

To run the Docker container, scroll down to either of the following sections for instructions: 

* Quick start - ephemeral test instance 

* Quick start - classic workflow

Get started with EJBCA Enterprise 
===============
Keyfactor offers 30-day free trials of EJBCA Enterprise use cases in ready-to-use environments, no installation or setup required: [Keyfactor Test Drives](https://docs.keyfactor.com/test-drives/)

Get in Contact: [Request a Demo](https://www.keyfactor.com/demo-request/)

Community Support
=================

The Community software is open source and community-supported; there is no support SLA, but a helpful best-effort Community. Need guidance or want to report an issue? Head over to [GitHub Discussions](https://github.com/Keyfactor/ejbca-ce/discussions) or [Issues](https://github.com/Keyfactor/ejbca-ce/issues).

For more information about how to engage in the community, see: [Engage in the EJBCA Community](https://www.ejbca.org/engage/)

Enterprise Support
==================

The Enterprise Edition is a licensed software backed by professional support services. Get in contact to learn more: [Contact us](https://www.keyfactor.com/contact-us/).

Open-Source License
=======

EJBCA Community Edition is licensed under the **[LGPL license](https://opensource.org/licenses/lgpl-license.html)**.

Tutorials
=======
There are many good tutorials available on the Keyfactor for Developers [Youtube channel](https://www.youtube.com/@KeyfactorDev). For example:

* [Quick start EJBCA Docker Container](https://www.youtube.com/watch?v=x-kEqPrz1g0)
* [Start out with EJBCA Docker container](https://www.youtube.com/watch?v=oWC5vsGWXQ4)
* [EJBCA Container - Issue Client Authentication Certificate](https://www.youtube.com/watch?v=wMD1GgSF-JE)
* [Deploy EJBCA container in Kubernetes](https://www.youtube.com/watch?v=iz2M7V9Pvs4)
* [Deploy EJBCA with Helm](https://www.youtube.com/watch?v=C9dprcbCo4o)
* [Upgrade with the Container](https://www.ejbca.org/case/upgrade-your-ejbca-docker-container-to-the-latest-version/)

And many more using the container for different use cases. New tutorials are released monthly.

Minimum System Requirements
===========================

To run the EJBCA container a system should fulfill these minimum requirements:

*   at least two CPU cores
    
*   at least 1GB of RAM
    

Usage
=====

The container's behaviour can be customized by overriding environment variables.

Starting the container without setting any environment variables will:

*   Use the bundled H2 database that is persisted on graceful stop (unless --rm is used). Also see 'Using an external database' below.
    
*   Generate a Management CA on first run with an empty database and output the enrollment information for the first administrator to the console.
    
*   Generate a TLS server side certificate from the Management CA on first startup for the instance's assigned hostname.
    
*   Log sufficient and necessary information for most common setups to console.
    
*   Accept plain HTTP connections of port 8080 and TLS connections on port 8443 with optional acceptance of client TLS certificates from any CA known to the application on startup.
    
Security Parameters
================
There are two application specific password settings related to security that you should set to custom values in a production environment. Set them with "-e" flags.
* PASSWORD_ENCRYPTION_KEY: This key allows for encrypting passwords (PBE) used in EJBCA, like End Entity clear text passwords (for batch generation), Crypto Token passwords (for auto activation), passwords for CMP Alias, SCEP Alias, etc. This property should be set before initial EJBCA installation and it should't be changed later, because there could exist passwords encrypted with the key about to be changed and EJBCA would be unable to decrypt them. You could use any password you consider safe, but it is strongly recommended that you use a randomly generated password
* CA_KEYSTOREPASS: This password is used internally to protect software Crypto Tokens (not HSM) holding CA private keys in the database unless a password has been set manually, i.e. a default password. It is recommended that you set your own password on crypto tokens and not generate default ones for anything other than test purposes.
* EJBCA_CLI_DEFAULTPASSWORD: when using the local command line interface (only available inside the host running the app server), the CLI authenticates using a user (default 'ejbca') and a password. This password can be set to a custom value with this parameter. 

Set by adding something like the following to the docker run command to pass the variables:
```
-e PASSWORD_ENCRYPTION_KEY="myrandomkey" -e CA_KEYSTOREPASS="anotherrandomkey" -e EJBCA_CLI_DEFAULTPASSWORD="randompassphrase"
```
A vault can be used for variables.

Quick start - ephemeral test instance
=====================================

Start of ephemeral instance where anyone with _unauthenticated_ network access to the instance can manage the system:

```
docker run -it --rm -p 80:8080 -p 443:8443 -h mycahostname -e TLS_SETUP_ENABLED="simple" keyfactor/ejbca-ce
```

Once the system is fully started, go to `https://mycahostname:443/ejbca/adminweb/` to access the EJBCA Admin GUI where the instance can be managed. In the menu of the EJBCA Admin GUI you will find a link to additional Documentation.

`TLS_SETUP_ENABLED="simple"` anyone with HTTPS access will be given full access to the Admin UI. It is _not_ recommended except for ephemeral tests without public network access as it gives access to the Admin UI without authentication. Production installation should use `TLS_SETUP_ENABLED="true"` when installing in order to require client certificate access to the Admin UI. With "true" the container will generate a ManagementCA that will be used to issue both server and initial client TLS certificate used for administration. The third option is `TLS_SETUP_ENABLED="later"` in which case it requires TLS configured on reverse proxy in front of EJBCA, and allows anyone access over TLS to begin using EJBCA.

When you stop the container all data will be gone. (Skip the `--rm` flag to persist data in the containers file system or use `DATABASE_JDBC_URL` described below to persist data in an external database.)

See full documentation and video tutorial: 
**[Quick Start Guide - Start EJBCA Container with Unauthenticated Network Access](https://doc.primekey.com/x/mpuKAw)**

Quick start - classic workflow
==============================

Start of ephemeral instance with client certificate authenticated access to management of the instance (`TLS_SETUP_ENABLED="true"`):

```
docker run -it --rm -p 80:8080 -p 443:8443 -h mycahostname -e TLS_SETUP_ENABLED="true" keyfactor/ejbca-ce
```

Once the system is fully started, in the console output, you will find instructions for how to enroll for the initial SuperAdmin client certificate keystore that needs to be imported to your browser.

**Note**: Select RSA2048 or EC P-256 for the initial superadmin key when asked. The default choice may be something non supported by browsers (such as the PQC algorithm Dilithium2).

Your browser likely remembers your non-certificate authenticated session, so restart the browser (or open a private window) and go to https://mycahostname:443/ejbca/adminweb/ to access the EJBCA Admin GUI where the instance can be managed. In the menu of the Admin GUI you will find a link to additional Documentation.

When you stop the container all data will be gone. (Skip the `--rm` flag to persist data in the containers file system or use `DATABASE_JDBC_URL` described below to persist data in an external database.)

See full documentation: 
**[Quick Start Guide - Start EJBCA Container with Client Certificate Authenticated Access](https://doc.primekey.com/ejbca/tutorials-and-guides/quick-start-guide-start-ejbca-container-with-client-certificate-authenticated-access)**

Additional examples
===================

More complex deployment examples are available on [Github](https://github.com/Keyfactor/keyfactorcommunity).

Using an external database
==========================

EJBCA uses a shared database model for clustering where all EJBCA nodes have a consistent view of the data. Hence, clustering EJBCA will require the use of an external SQL database. MariaDB with Galera clustering is an open source example of such database that can be both highly available and consistent with synchronous multi-master replication.

`DATABASE_JDBC_URL`: The JDBC drivers for MariaDB (works with MySQL as well) and Postgres are also bundled with the container and works out of the box by specifying the corresponding JDBC URL.

*   `jdbc:h2:/mnt/persistent/ejbcadb;DB_CLOSE_DELAY=-1` will use the bundled H2 database that is persisted when the container is gracefully stopped. This is the default value.
     - You need to use docker volumes in order to read the same /mnt/persistent directory every time, otherwise docker will create a new random volume every time
    - Example parameter to re-use an old random volume (from /var/lib/docker/volumes): -v 850e26b80f6f75d1c43f8cf0e2af4d9c98784191694162655d57b0fca7876a81:/mnt/persistent
    
*   `jdbc:h2:mem:ejbcadb;DB_CLOSE_DELAY=-1` will use the bundled H2 database where nothing is persisted when the container is stopped.
    
*   `jdbc:mariadb://database:3306/ejbca?characterEncoding=UTF-8` will connect to an MariaDB instance with a database named `ejbca` at the host database
    
*   `jdbc:postgresql://database/ejbca` will connect to a Postgres instance with a database named `ejbca` at the host database
    

`DATABASE_USER`: The username part of the credentials to access the external database. Not required for use of the H2 database.

`DATABASE_PASSWORD`: The password part of the credentials to access the external database. Not required for use of the H2 database.

Using an external database, you can run multiple containers, or stop-start containers, in an ephemeral style. For example, starting a container first with an empty database:

```
docker run -it --rm -p 80:8080 -p 443:8443 -h mycahostname -e TLS_SETUP_ENABLED="true" -e DATABASE_JDBC_URL="jdbc:mariadb://172.26.0.1:3306/ejbcatest?characterEncoding=UTF-8" -e DATABASE_USER="ejbca" -e DATABASE_PASSWORD="password" keyfactor/ejbca-ce
```

The first time started, database database tables will be created, a ManagementCA will be created, and a superadmin client certificate will be created (for import into your webbrowser) for access to adminweb. If you stop and start the container again, a new TLS server certificate will be created for this instance on startup, but not a new ManagementCA and superadmin keystore, as the old ones from the first startup are present in the database and valid.

Using the container behind a proxy
==================================

Configuring the container as a proxy back-end will disable legacy installation workflow or any local TLS server side certificate generation. The Admin UI will be open to anyone will network access until configured otherwise.

Running the container behind a front-end proxy (like Nginx or Apache Httpd) that terminates TLS connections is currently the expected setup for any kind of production-like deployment.

When binding a proxy back-end protocol port to

*   an IP that can later be exposed outside the container (e.g. "0.0.0.0") care needs to be taken to ensure that no traffic can reach the bound port directly.
    
*   a local IP (e.g. "127.0.0.1") it is expected that a side-car deployment in the same Kubernetes Pod will be used and forward requests inside the Pod.
    

`PROXY_AJP_BIND`: Run container with an AJP proxy port :8009 bound to the IP address in this variable.

`PROXY_HTTP_BIND`: Run container with two HTTP back-end proxy ports :8081 and :8082 configured bound to the IP address in this variable. Port 8082 will accepts the `SSL_CLIENT_CERT` HTTP header.

`TLS_SETUP_ENABLED`: Setting this to "later" requires TLS configured on reverse proxy in front of EJBCA, and allows anyone access over TLS to begin using EJBCA. Setting it to "false" for no proxy back-end setup, but still disable container internal TLS setup. Setting it to "false", the applications Admin UI will grant full access to anyone that is able to connect. Currently EJBCA's Admin GUI is not very functional in this setup without a URL-content rewriting proxy in front. By setting this to "simple", you will end up with a Management CA and server side TLS certificate, but anyone can manage the system over HTTPS. Such unauthenticated access is allowed with a PublicAccessAuthenticationToken configured in Admin GUI → Roles → Super Administrator Role → Members. 

Sending email
=============

When using email notifications EJBCA by default uses a service in the application server `java:/EjbcaMail`. Such a mail service is pre-configured in the application servers' `standalone.xml`, with environment variables for SMTP host and port.

`SMTP_DESTINATION`: The hostname/IP of the SMTP server that should be used for sending email, default localhost (there is no SMTP server in the container though).

`SMTP_DESTINATION_PORT`: the SMTP port to use on the host, default 25.

You can set the variables with an argument with a '-e' argument on the command line, see Quick Start for example usage.
Other variables relevant for SMTP settings are `SMTP_TLS_ENABLED` (default true), `SMTP_SSL_ENABLED` (default true), `SMTP_USERNAME`, `SMTP_PASSWORD`, `SMTP_FROM`.

An example to use Gmail's SMTP server:

```
docker run -it --rm -p 80:8080 -p 443:8443 -h mycahostname -e TLS_SETUP_ENABLED="simple" -e SMTP_DESTINATION="smtp.gmail.com" -e SMTP_DESTINATION_PORT="587" -e SMTP_USERNAME="user@gmail.com" -e SMTP_PASSWORD="userssecretpassword" -e SMTP_FROM="user@gmail.com" -e SMTP_SSL_ENABLED="false" keyfactor/ejbca-ce
```

Overriding properties
=====================

The ejbca.ear file in the container is built with enabled what is called `allow.external-dynamic.configuration=true`, this means that it looks for configuration properties, in order of priority:

*   Java system property (`java -Dfoo.prop=bar`)
    
*   `/etc/cesecore/conf/` or `/etc/ejbca/conf/` which are both sym-linked to a common folder
    
*   configuration files built into the ejbca.ear file
    

Changing these directly might affect container startup behavior which is leveraging the same mechanism and might break when you update the container. Make sure you know what you do before you override these.

Changing port for static link in Public Web
===========================================

If you want to run EJBCA on a different HTTPS port than the default (443), for example:

```
docker run -it --rm -p 80:8080 -p 9443:8443 -h mycahostname keyfactor/ejbca-ce
```

Everything works as expected but there is one link from the _deprecated_ Public Web, [https://locahost:9443](https://locahost:9443/), that has a static configuration of the port s the link does not work.

You can fix this with the above property override.

1.  In the machine where docker is running, create a directory and create a file web.properties that contains the same as your container version does in a fully started state.
    
2.  Run the container with
    
    `docker run -it --rm -p 8080:8080 -p 9443:8443 -h mycahostname -v $PWD/config/web.properties:/opt/primekey/ejbca/conf/web.properties keyfactor/ejbca-ce`
    

Now you can browse to [https://localhost:8443](https://localhost:8443/) and see that the link Miscellaneous -> Administration is: [https://localhost:9443/ejbca/adminweb/](https://localhost:9443/ejbca/adminweb/)

Using External Plugin
===========================================
If you want to use a an external plugin with EJBCA, volume mount the plugin to the container file path of /opt/primekey/ejbca/plugins. Any jar file in this directory will be picked up by EJBCA.

1. Create a directory called ejbca-plugins with the jar file

2. Launch the container
`docker run -it --rm -p 80:8080 -p 443:8443 -h mycahostname -v $PWD/ejbca-plugins:/opt/primekey/ejbca/plugins keyfactor/ejbca-ce`

More information about plug-ins can be found in the EJBCA Documentation: [Creating Plugins](https://docs.keyfactor.com/ejbca/latest/creating-plugins).

Environment Variables
===========================

There are other environment variables that you can set. You can list see them and their defaults by running:

```
docker inspect keyfactor/ejbca-ce:latest
```

for example you can enable DEBUG logging with '`-e LOG_LEVEL_APP=DEBUG`'.

See also the documentation for the [EJBCA Community Helm chart](https://github.com/Keyfactor/ejbca-ce/tree/main/charts/ejbca), where many variables are documented. 

Directories of importance
===========================

Please note that these directory are provided here for transparency and might be subject to change without notice.

**/mnt/persistent**

This directory is where the H2 database persists its data on graceful shutdown by default.

**/mnt/external/secrets/tls/cas**

Any DER-encoded (binary) certificate in this directory will be offered as a CA to import for the application. EJBCA will use the filename as CA name (e.g. "ManagementCA.crt" → "ManagementCA").

The certificates will be also be merged with any CA certificate the application has into a truststore used for configuring TLS in non-proxy mode.

**/mnt/external/secrets/tls/ks**

Presence of the files "server.jks", "server.storepasswd" and optional "server.keypasswd" will be interpreted as a keystore to use when configuring TLS in non-proxy mode.

**/mnt/external/secrets/tls/ts**

Presence of the files "truststore.jks", "truststore.storepasswd"  will be interpreted as a truststore keystore to use when configuring TLS truststore in non-proxy mode.

**/opt/keyfactor**

In general. Most adaptions compared to a base OS installation with Java can be found under this tree.

**/opt/keyfactor/ejbca/conf**

This directories contain run-time overridable application configuration property files.

**/opt/keyfactor/bin**

This directory contain the startup script and other exposed CLI commands like "ejbca.sh", "ejbcaClientToolBox.sh" etc.

**/opt/keyfactor/configdump/staged.d/**

On the EJBCA EE 7.3+ container, an EJBCA ConfigDump present here will be imported on first run with an empty datatbase.

**/opt/keyfactor/configdump/initialize.d/**

On the EJBCA EE 7.3+ container, an EJBCA ConfigDump present here will be imported and initialized on first run with an empty database.

**/opt/keyfactor/ebjca/plugins**

Mount EJBCA plugins here that are jar files to use external plugins with EJBCA.

**/opt/keyfactor/appserver/standalone/configuration/logger-appserver**

Mount XML logger file called logger-appserver for application server logging to have customized log levels for the different loggers. This is useful for troubleshooting or when very specific log levels are needed and not use global logging values such as INFO, WARN, DEBUG, etc.

**/opt/keyfactor/appserver/standalone/configuration/logger-app**

Mount XML logger file called logger-app for application logging to get customized log levels for the application loggers. This is useful for troubleshooting or when very specific log levels are needed and not use global logging values such as INFO, WARN, DEBUG, etc.

**Mounting in own files**

You can mount in your own files, for example for TLS keystores, EJBCA/SignServer properties, or HSM drivers.

Example command (to be adapted) to mount in your own keystore from local directory /pki-ejca:

```
sudo docker run -it --rm -p 80:8080 -p 443:8443 -v /pki-ejbca/pki-ejbca/server.jks:/mnt/external/secrets/tls/ks/server.jks -v /pki-ejbca/pki-ejbca/server.keypasswd:/mnt/external/secrets/tls/ks/server.keypasswd -e DEBUG=info keyfactor/ejbca-ce
```

Verify that files were mounted:

 ```
sudo docker run -it --rm --name thales_test -p 18080:8080 -p 18443:8443 -v /opt/thales:/opt/thales keyfactor/ejbca-ce
sudo docker ps
sudo docker exec -ti thales_test /bin/bash
ls -al /opt/thales
```

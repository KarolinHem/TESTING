[//]: # (Please update the Confluence page titled "SignServer CE DockerHub - Markdown" if making any changes to this page)

SignServer Community Edition
============================

>**Note:**
>
>We take the security of SignServer and the trust of our users seriously. If you believe you have identified a security vulnerability in SignServer, please report it responsibly by contacting us at security@primekey.com 
>
>SignServer Community Edition is not intended for production use. For production signing services, use the Keyfactor SignServer Enterprise or Keyfactor Signum 

SignServer is a signing solution for digitally signing code, documents, and artifacts while keeping signing keys secure and signing workflows auditable. It is platform-independent and supports centralized, policy-driven signing operations. 

SignServer is developed in Java and runs on a JVM such as OpenJDK, available on most platforms, such as Linux and Windows. 

There are two editions of SignServer: 

* **[SignServer Community](https://www.signserver.org/)**  

SignServer Community Edition is an LGPL-licensed open-source subset of SignServer Enterprise, certified by the Open Source Initiative, and designed for learning, testing, and prototyping signing workflows for code, documents, and artifacts. 

SignServer CE is not recommended for production use, as production signing environments typically require security certifications, advanced key protection, auditability, SLAs, support, and operational assurances available in SignServer Enterprise Edition. 

* **[SignServer Enterprise](https://www.keyfactor.com/products/signserver-enterprise/)**

SignServer Enterprise Edition is designed for production signing environments, offering advanced signing features, enterprise-grade key management, auditability, compliance capabilities, and commercial support with SLAs. 

Learn more about the differences between SignServer CE and SignServer EE: [SignServer Community vs Enterprise](https://www.signserver.org/community-vs-enterprise/)

Get started with the SignServer Docker Container
=================

The SignServer Community Docker container can be pulled straight from the command line using the docker tool. To download and unpack the latest SignServer Community container image from Docker Hub, use the following command: 

```
$ sudo docker pull keyfactor/signserver-ce 
```

To run the Docker container, see the following sections below for full instructions: 

* Usage 

* Quick Startup 

Get started with SignServer on Kubernetes

Install the SignServer Helm chart from Artifact Hub.

Get Started with SignServer Enterprise Edition
==============================================
Keyfactor offers 30-day free trials of SignServer Enterprise use cases in ready-to-use environments, no installation or setup required through [Keyfactor Test Drives](https://docs.keyfactor.com/test-drives/)

Get in Contact: [Request a Demo](https://www.keyfactor.com/demo-request/)

Community Support
=================

In our [Community](https://www.keyfactor.com/community/) we welcome contributions. The Community software is open source and community-supported, there is no support SLA, but a helpful best-effort Community.

For information about how to engage in the community, get support, ask questions, and post comments, see [Engage in the SignServer Community](https://www.signserver.org/engage/)

Enterprise Support
==================

The Enterprise edition is a licensed software backed by professional support services. Get in contact to learn more: [Contact us](https://www.keyfactor.com/contact-us/).

License
=======

SignServer Community is licensed under the **[LGPL license](https://opensource.org/licenses/lgpl-license.html)**.

Tutorials
=======
There are many good tutorials available on the Keyfactor for Developers [Youtube channel](https://www.youtube.com/@KeyfactorDev). For example:

* [Quick start SignServer Docker Container](https://www.youtube.com/watch?v=wMqPWKi3ukE)
* [Code Signing – Set up SignServer and OpenPGP to Sign Code and Packages](https://www.youtube.com/watch?v=cefQkP8XXs8)
* [Helm Chart for Signing – Quick Start SignServer by Using Kubernetes and Helm](https://www.youtube.com/watch?v=nntAbPvxQAg)

And many more using the container for different use cases. New tutorials are released monthly.

Minimum System Requirements
===========================

To run the SignServer container, a system should fulfill these minimum requirements:

*   CPU cores: Minimum 2 cores
    
*   RAM: 1GB
    

Usage
=====

The container's behavior can be customized by overriding environment variables.

Starting the container without setting any environment variables will:

*   Use the bundled H2 database that is persisted on graceful stop.
    
*   Generate a self-signed TLS server-side certificate on the first startup for the instance's assigned hostname.
    
*   Log sufficient and necessary information for most common setups to console.
    
*   Accept plain HTTP connections of port 8080 and TLS connections on port 8443 with optional acceptance of client TLS certificates from any Certificate Authority (CA) known to the application on startup.
    

Quick Startup
=============

To start an ephemeral instance with client certificate-authenticated access to management of the instance, run the 'docker run' command according to the following example.

Provide the CA certificate that issued your authentication certificate. Save it in the current directory as `TrustedCA.pem` or replace the below "`$(pwd)/TrustedCA.pem`" with the file path to your certificate file. The server certificate will be automatically generated and self-signed. See log output for the fingerprint: "Generated TLS certificate with fingerprint xxxx".

```
docker run -it --rm --name signserver \
    -p 80:8080 -p 443:8443 \
    -v $(pwd)/TrustedCA.pem:/mnt/external/secrets/tls/cas/ManagementCA.crt \
    -h signserver.example.com \
    keyfactor/signserver-ce
```

When you stop the container, all data will be gone. Exclude the `--rm` option to persist data in the container file system or use `DATABASE_JDBC_URL` described below to persist data in an external database.

Open [http://signserver.example.com/signserver/](http://signserver.example.com/signserver/adminweb/) for SignServer.

Note the links for the SignServer Client Web, Administration Web, and Documentation.

See full documentation and video tutorial: [Quick Start Guide - Start SignServer Container with Client Certificate Authenticated Access](https://docs.keyfactor.com/signserver/latest/quick-start-guide-start-signserver-container-with-).

Setting up SignServer
=====================

The following provides instructions for setting up SignServer, either for testing purposes with sample keys and certificates, or for setting up SignServer with new keys and certificates, requiring keys to be generated and certificates issued by a Certificate Authority (CA).

Alternative 1: Quick Setup with Snake Oil Keys and Certificates for Demo or Testing Only
----------------------------------------------------------------------------------------

The following provides instructions for setting up SignServer either for testing purposes, using sample keys and certificates.

Note that this setup is only intended for testing or demonstrating SignServer functionality. The method uses pre-generated sample keys and certificates that are part of SignServer, which must not be relied upon in any way.  

### Add Crypto Token

1.  Go to the SignServer Administration Web.
    
2.  On the Workers page, click **Add**.
    
3.  Click **From Template**.
    
4.  Select `keystore-crypto.properties` in the **Load From Template** list and click **Next**.
    
5.  Update the following in the configuration:  
    
    *   Remove the "`#`" before "`WORKERGENID1.KEYSTOREPASSWORD=foo123`".
        
6.  Click **Apply** to add the Crypto Token.
    

### Add Signers

1.  On the Workers page, click **Add**.
    
2.  Click **From Template**.
    
3.  Select the properties in the **Load From Template** list for the signer to add, for example, `pdfsigner.properties`, and click **Next**.
    
4.  Click **Apply** to load the configuration and verify that the signer is in state ACTIVE and ready to be used.
    

### Test Signing

1.  Go to the SignServer Client Web: [https://signserver.example.com/signserver/clientweb/](https://signserver.example.com/signserver/clientweb/)
    
2.  Under **File Upload,** specify the Worker **Name** used, for example, PDFSigner.
    
3.  Click **Browse** to select a PDF file.
    
4.  Click **Submit** and store the resulting signature file.
    

Alternative 2: Setup with new Keys and Certificates
---------------------------------------------------

The following provides instructions for setting up SignServer with new keys and certificates, requiring keys to be generated and certificates issued from a Certificate Authority (CA).

### Add Crypto Token

1.  Go to the SignServer Administration Web.
    
2.  On the Workers page, click **Add**.
    
3.  Click **From Template**.
    
4.  Select `keystore-crypto.properties` in the **Load From Template** list and click **Next**
    
5.  Update the following in the configuration:
    
    *   Change "`WORKERGENID1.KEYSTORETYPE=PKCS12`" to "`WORKERGENID1.KEYSTORETYPE=INTERNAL`".
        
    *   Remove the line starting with "`WORKERGENID1.KEYSTOREPATH`".
        
6.  Click **Apply** to add the Crypto Token.
    
7.  Select on the newly created "CryptoTokenP12" and click **Activate**.
    
8.  Enter a new password to use as the protection for the keystore.
    
    Ensure to make a backup of this password as it will be needed again, for instance upon application restart. It is recommended to ensure that the password works by deactivating and then activating the crypto token.
    
    Alternatively, set the worker property **PIN** with this password for the token to remember it and stay in auto-activated mode.
    
9.  To generate a key for the Crypto Token, select the "CryptoTokenP12" to open the Worker page and then click **Crypto Token**.
    
10.  On the Crypto Token page, click **Generate key**.
    
11.  On the Crypto Token Key Generation page, specify a **New Key Alias** name for the key, for example "testkey0",
    
12.  Click **Generate** and verify that the worker is now in state ACTIVE.
    

### Add Signers

1.  On the SignServer Workers page, click **Add**.
    
2.  Click **From Template**.
    
3.  Select the properties in the **Load From Template** list for the signer to add, for example, `pdfsigner.properties`, and click **Next**.
    
4.  Click **Apply** to load the configuration. The worker is OFFLINE as it needs a key and certificate.
    

### Generate Keys and Request and Install Certificates

To generate keys for the signer:

1.  Select the signer in the Workers list, and click **Renew key**.
    
2.  Under **Renew Keys,** specify the following:
    
3.  Select **Key Algorithm,** for example **RSA**.
    
4.  Select **Key Specification,** for example **3072**.
    
5.  Specify a **name** for the new key, for example **pdfsigner001**.
    
6.  Click **Generate**.
    

To generate the CSR for the signer:

1.  Select the signer in the Workers list, and click **Generate CSR**.
    
2.  Specify a **DN,** for example "CN=PDFSigner 0001", and then click **Generate**.
    
3.  Click **Download** and store the CSR/PKCS#10 file.
    

Next, you need to bring the CSR to the CA and obtain a certificate in PEM format for it.  

Then, to install the signer certificates issued by the CA in SignServer, do the following:

1.  Bring the CSR to the CA and obtain a certificate in PEM format for it.
    
2.  Select the signer in the SignServer Workers list, and click **Install Certificates**.
    
3.  Browse for the PEM certificate file and click **Add**.
    
4.  Click **Install** and confirm that the signer is now listed as ACTIVE and ready to be used.
    

### Test Signing

1.  Go to the SignServer Client Web: [https://signserver.example.com/signserver/clientweb/](https://signserver.example.com/signserver/clientweb/)
    
2.  Under **File Upload,** specify the Worker **Name** used, for example, PDFSigner.
    
3.  Click **Browse** to select a PDF file.
    
4.  Click **Submit** and store the resulting signature file.
    

Additional Examples
===================

More complex deployment examples may be made available on [GitHub](https://github.com/Keyfactor).

Using an External Database
==========================

If using an external SQL database, MariaDB with Galera clustering provides an open source example of such a database that can be both highly available and consistent with synchronous multi-master replication.

`DATABASE_JDBC_URL`: The JDBC drivers for MariaDB/MySQL and Postgres are also bundled with the container and work out of the box by specifying the corresponding JDBC URL. For historical reasons, the MariaDB JDBC URL begins with `jdbc:mysql:`.

*   `jdbc:h2:/mnt/persistent/ejbcadb;DB_CLOSE_DELAY=-1` will use the bundled H2 database that is persisted when the container is gracefully stopped. This is the default value.
*   `jdbc:h2:mem:ejbcadb;DB_CLOSE_DELAY=-1` will use the bundled H2 database where nothing is persisted when the container is stopped.
*   `jdbc:mysql://database:3306/signserver?characterEncoding=UTF-8` will connect to an MariaDB/MySQL instance with a database named `signserver` at the host database.
*   `jdbc:postgresql://database/signserver` will connect to a Postgres instance with a database named `signserver` at the host database.
    

`DATABASE_USER`: The username part of the credentials to access the external database. Not required for use of the H2 database.

`DATABASE_PASSWORD`: The password part of the credentials to access the external database. Not required for use of the H2 database.

Using the Container Behind a Proxy
==================================

Configuring the container as a proxy back-end will disable TLS setup and the Admin UI will be open to anyone with network access until configured otherwise.

Running the container behind a front-end proxy (like Nginx or Apache Httpd) that terminates TLS connections is currently the expected setup for any kind of production-like deployment.

When binding a proxy back-end protocol port to

*   an IP that can later be exposed outside the container (e.g. "0.0.0.0") care needs to be taken to ensure that no traffic can reach the bound port directly.
    
*   a local IP (e.g. "127.0.0.1") it is expected that a side-car deployment in the same Kubernetes Pod will be used and forward requests inside the Pod.
    

`PROXY_AJP_BIND`: Run container with an AJP proxy port :8009 bound to the IP address in this variable.

`PROXY_HTTP_BIND`: Run container with two HTTP back-end proxy ports :8081 and :8082 configured bound to the IP address in this variable. Port 8082 will accept the `SSL_CLIENT_CERT HTTP` header.


Audit log
==================================

SignServer uses Log4j for debug logging and the security events logger from CESeCore for system/audit logging. In addition, the worker logger (transaction log) can be configured to use Log4j and/or the security events logger.
Audit logging is enabled by default and will be configured with the following properties in cesecore.properties:
```
securityeventsaudit.implementation.0=org.signserver.server.log.SignServerLog4jDevice
securityeventsaudit.implementation.1=org.cesecore.audit.impl.integrityprotected.IntegrityProtectedDevice
securityeventsaudit.exporter.1=org.cesecore.audit.impl.AuditExporterXml
```

Audit logging can be disabled by setting environment variable `LOG_AUDIT_TO_DB` to `false`

It is possible to also mount in your own cesecore.properties, if this is the case it is recommended to put `LOG_AUDIT_TO_DB` to `false`.

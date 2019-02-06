# shrine-webclient-cas

[Georgia Clinical and Translational Science Alliance (Georgia CTSA)](http://www.georgiactsa.org), [Emory University](http://www.emory.edu), Atlanta, GA

## What does it do?
This is a stock shrine-webclient-1.20.1 patched with support for 
* delegating authentication to [Eureka! Clinical CAS](https://github.com/eurekaclinical/cas) or other CAS server that supports version 2 of the CAS protocol
* automated i2b2 account creation using the [Eureka! Clinical i2b2 Integration microservice](https://github.com/eurekaclinical/eurekaclinical-i2b2-integration-service)
* requiring users to sign an electronic data use agreement using [Eureka! Clinical User Agreement microservice](https://github.com/eurekaclinical/eurekaclinical-user-agreement-service)


## Version history
### Version 1.0
Initial release using version 1.25.4 of the web client.

## CAS implementations supported
We expect any full implementation of version 2 of the CAS protocol to work. In particular, the implementation must support proxying. The following implementations of CAS are known to work:
* [Eureka! Clinical CAS](https://github.com/eurekaclinical/cas), which is a patched version of [JASIG CAS version 3.5.2](https://wiki.jasig.org/display/CASUM/Home)
* [Shibboleth Identity Provider version 3](https://wiki.shibboleth.net/confluence/display/IDP30/Home) with CAS emulation turned on

## Requirements
See the [shrine Web Client Install section ] (https://open.med.harvard.edu/wiki/display/SHRINE/Upgrading+SHRINE+to+1.20#UpgradingSHRINEto1.20-DeployNewSHRINEWebclient) for requirements for the web client itself. The Eureka! Clinical components require:
* [Oracle Java JRE 8](http://www.oracle.com/technetwork/java/javase/overview/index.html)
* [Tomcat 7](https://tomcat.apache.org)
* One of the following relational databases:
  * [Oracle](https://www.oracle.com/database/index.html) 11g or greater
  * [PostgreSQL](https://www.postgresql.org) 9.1 or greater
  * [H2](http://h2database.com) 1.4.193 or greater (for testing)

## Installation
1) Install our patched i2b2 project management cell, [i2b2-pm-cas](https://github.com/eurekaclinical/i2b2-pm-cas).
2) Install [eurekaclinical-i2b2-integration-service](https://github.com/eurekaclinical/eurekaclinical-i2b2-integration-service).
3) Install [eurekaclinical-i2b2-integration-webapp](https://github.com/eurekaclinical/eurekaclinical-i2b2-integration-webapp) on the same host as the shrine web client.
4) Install [eurekaclinical-user-agreement-service](https://github.com/eurekaclinical/eurekaclinical-user-agreement-service).
5) Install [eurekaclinical-user-agreement-webapp](https://github.com/eurekaclinical/eurekaclinical-user-agreement-webapp) on the same host as the shrine web client.
6) Follow the [shrine Web Client Install section ](https://open.med.harvard.edu/wiki/display/SHRINE/Upgrading+SHRINE+to+1.20#UpgradingSHRINEto1.20-DeployNewSHRINEWebclient) for installing the web client itself. The steps for installing our patched web client are identical.

## Configuration

### Configuring the web client
The configuration are there in `i2b2_config_data.js` file which is in `shrine-webclient` directory.   

### Pointing the webclient to your CAS server
Open the `i2b2_config_data.js` file, and add the following properties to your domain settings:
* `CAS_server`: the URL for your CAS server.
* `CAS_LOGOUT_TYPE`: either `CAS` or `LOCAL`, depending on whether you want the web client's `Logout` link to log the user out of CAS (`CAS`) or just log the user out of shrine (`LOCAL`).
* `EC_LOGOUT_LANDING_PAGE_URL`: for `LOCAL` logouts, the URL to load after the user's session has been ended.
* `EC_I2B2_INTEGRATION_URL`: the URL for your eurekaclinical-i2b2-integration-webapp.
* `EC_USER_AGREEMENT_URL`: optional URL for your eurekaclinical-user-agreement-webapp, if you want users to be redirected to it.
* `EC_SUPPORT_CONTACT`: optional email address to display when an error has occurred.

### Configuring the shrine tomcat
 We are using two tomcats on the same box, one is for shrine components which is in `/opt/shrine/tomcat` and the other one for eureka components which is in `/opt/tomcat`.
* Update the following Connectors in the `/var/shrine/tomcat/conf/server.xml` file to avoid the conflict with other tomcat server.
  * Update the `port:8080` connector to `<Connector port="8081" protocol="HTTP/1.1" 
               connectionTimeout="20000" 
               redirectPort="6443" />` .
  * Comment or remove the `port:8443` connector.
  * Update the `port:8009` connector to `<Connector port="6009" protocol="AJP/1.3" redirectPort="6443" />` .
* If you are using web server like Apache/httpd then please remove or comment the CATALINA_OPTS variable in the `/opt/shrine/tomcat/bin/setenv.sh` file.
## Licensing

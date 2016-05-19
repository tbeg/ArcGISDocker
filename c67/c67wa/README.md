### CentOS 6.7 ArcGIS Web Adaptor 
This Image starts with a Minimal CentOS 6.7.
Installs java, http
Adds tomcat 8 and ArcGIS Web Adaptor

- ags user is also tomcat admin
- docker build -t c67wa .
- You can access via.  docker run -it --rm c67wa /bin/bash

### Admin
- Configure Web Server
  - # openssl req -x509 -nodes -newkey rsa:2048 -keyout server.key -out server.crt -days 365
  - # vi /etc/http/conf.d/ssl.conf

Change lines for SSLCertificateKeyFile and SSLCertificateFile
- Move the server.key and server.crt files to locations noted in the ssl.conf file
  - # cp server.key /etc/pki/tls/private/
  - # chmod 600 /etc/pki/tls/private/server.key
  - # cp server.crt /etc/pki/tls/certs/
  - # chmod 600 /etc/pki/tls/certs/server.crt
  - # vi /etc/httpd/conf.d/proxy.conf  <<  Creates a file named proxy.conf

ProxyRequests off
ProxyPass /arcgis ajp://localhost:8009/arcgis
ProxyPassReverse /arcgis ajp://localhost:8009/arcgis
ProxyPass /portal ajp://localhost:8009/portal
ProxyPassReverse /portal ajp://localhost:8009/portal

- Start Web Server
  - # chkconfig httpd on
  - # service httpd start

- Deploy WA
  - $ cp /home/ags/arcgis/webadaptor10.4/java/arcgis.war /opt/tomcat/webapps/
  - $ cp /home/ags/arcgis/webadaptor10.4/java/arcgis.war /opt/tomcat/webapps/portal.war
  - $ /opt/tomcat/bin/startup.sh

  - $ cd /home/ags/arcgis/webadapator10.4/java/tools
  - $ ./configurewebapaptor.sh -m server -w http://c7a.jennings.home:8080/arcgis/webadaptor -g https://c7a.jennings.home:6443 -u siteadmin -p <PASSWORD> -a true






